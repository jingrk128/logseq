- ### 結束cmd
- 釋放opCmd - #NfcCom_FreeOpCmd()
- 如果raidCtrl不是null 且 opType是OP_TYPE_PROG：
	- #Nfc_ReleaseRaidResource() #待辦(不急)
- 如果opType是OP_TYPE_READ 且 metaAddr[0]不是null：
	- 如果isSlcMode是0：
		- 處理meta - NfcMeta_ModifyData() #待辦
		- NOW 為什麼cmd都要結束了還要去弄meta？
		  :LOGBOOK:
		  CLOCK: [2023-05-21 Sun 17:09:22]
		  CLOCK: [2023-05-21 Sun 17:09:24]
		  :END:
- cmd有指定callback嗎？
	- 有
		- #NfcInj_AmendResult() #待辦(不急)
		- gNfcBlockCbFlag是0嗎？
			- 不是(gNfcBlockCbFlag在這邊看起來一定是0，所以可以不用做這個判斷) #改進空間
				- 執行callback且不加任何資訊 - #CallBack_Nfc() #待辦
				- 釋放cmd - #Nfc_FreeCmd()
			- 是
				- 把callback放在pendCbQ裡面 - #NfcInPendCbQ()
				- NOW 上面不是已經執行過callback了嗎？為什麼還要放進pendCbQ?
				  :LOGBOOK:
				  CLOCK: [2023-05-21 Sun 17:52:30]
				  CLOCK: [2023-05-21 Sun 17:52:32]
				  :END:
	- 沒有
		- 釋放cmd - #Nfc_FreeCmd()
- 1.檢查是否有pending中的cmd？ - #NfcIsPendCmd()
- 2.gNfcNandResetFlag是否為0？ - #NfcSched_GetTimeoutResetFlag()
- 3.檢查記憶體資源是否足夠？ - #NfcCheckSubmitResource()
- 若以上三者皆是，就處理pending的cmd - #NfcSched_RunPendCmd()
	- NOW 看起來只會處理一筆pending，為什麼不把所有的pending都做完？
	  :LOGBOOK:
	  CLOCK: [2023-05-21 Sun 18:07:49]
	  :END: