- ### 把gNfcRspQ和gNfcWaitQ裡面的child拉出來執行NfcSched_SchedCmd()
- 參數：NFC_OP_CMD_T *pCmd
- 如果gNfcRspQ不是空的：
	- gNfcRspQ裡面有幾個node，以下迴圈就重覆幾次：
	  n的初始值為0，每重覆一次n就+1
		- 把gNfcRspQ裡的第n個node視為opCmd
		- 如果這個opCmd沒有parent，就結束跳過這次迴圈，直接開始下次迴圈
			- 這邊的opCmd有可能沒有parent嗎？ #問題集 #已解決
			  :LOGBOOK:
			  CLOCK: [2023-05-23 Tue 00:35:05]
			  CLOCK: [2023-05-23 Tue 00:35:40]
			  :END:
				- 答：有可能，如果有其它opCmd剛剛得到response且沒有unc，那麼該opCmd就不會是child，也不會有parent
		- 把這個opCmd從gNfcRspQ當中給刪掉 - #DList_Delete()
		- 依據opCmd->state來決定下一步要怎麼做 - #NfcSched_SchedCmd()
		-
- 以下步驟重覆兩次
  第一次ch=pCmd的ch&(~1)，第二次ch=ch+1
- 為什麼要做兩個ch？#問題集 #已解決
  :LOGBOOK:
  CLOCK: [2023-05-23 Tue 01:03:03]
  CLOCK: [2023-05-23 Tue 01:03:12]
  :END:
	- 答：因為2311的是兩個ch共用一個ldpc decode，而decode的buffer無法同時讓兩個ch一起使用，當出現兩個ch同時都要用soft retry的時候，[[Ldpc_RequireSdResource()]]會回傳busy阻止fw對nfc下cmd。
	  所以這裡必須讓同一組的ch都掃過，讓正在佔用decode資源的ch先執行完畢，釋放掉資源，才能讓下一個人使用
	- 以下把ch內的gNfcWaitQ[ce]都做一次，ce為0到7：
	  :LOGBOOK:
	  CLOCK: [2023-05-23 Tue 01:04:45]--[2023-05-23 Tue 01:04:47] =>  00:00:02
	  :END:
	- 2311的2242的盤只有兩個ce，為什麼也要做ce2-ce7？ #問題集 #已解決
	  :LOGBOOK:
	  CLOCK: [2023-05-23 Tue 01:04:50]
	  CLOCK: [2023-05-23 Tue 01:04:53]
	  CLOCK: [2023-05-23 Tue 01:04:55]
	  :END:
		- 答：應該是因為考量到使用的ce不連續的關係，所以讓每個ce都跑過一遍比較保險
		- 如果gNfcChCmdMgr[ch].gNfcWaitQ[ce]不是空的：
			- gNfcChCmdMgr[ch].gNfcWaitQ[ce]裡有幾個node，以下迴圈就重覆幾次：
			  設n的初始值為0，每重覆一次n就+1
				- 把gNfcChCmdMgr[ch].gNfcWaitQ[ce]裡的第n個node視為一個opCmd
				- 如果opCmd沒有parent，就直接跳到下次迴圈
					- NOW 沒有parent的opCmd，也就是非child的opCmd，有可能會被推到gNfcWaitQ裡嗎？ #問題集
					  :LOGBOOK:
					  CLOCK: [2023-05-23 Tue 01:12:03]
					  CLOCK: [2023-05-23 Tue 01:12:11]
					  :END:
				- 依據opCmd->state來決定下一步要怎麼做 - #NfcSched_SchedCmd()
				- 如果opCmd->state是NFC_SCHED_WAIT_SCHED，就強制結束迴圈
	-
	-