- 初始化read retry的table - #NfcExcp_InitReadRetryCal()
	- NOW 為什麼每次unc都要執行? 在nfc init時做一次不就好了嗎? #問題集
	  :LOGBOOK:
	  CLOCK: [2023-05-18 Thu 22:11:53]
	  CLOCK: [2023-05-18 Thu 22:11:56]
	  :END:
	- 把opCmd-推到gNfcRepairQA[opCmd->target.ch] - #DList_PushTail()
	- opCmd->option.childCnt設為15
		- NOW childCnt是幹嘛的? #問題集
		  :LOGBOOK:
		  CLOCK: [2023-05-18 Thu 22:15:04]
		  CLOCK: [2023-05-18 Thu 22:15:09]
		  :END:
	- gRepairCnt++