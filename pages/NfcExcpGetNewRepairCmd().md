- ### 處理gExcpCtrl
- 如果指定channel的gNfcRepairQA裡面是空的，就回傳false
- 從指定channel的gNfcRepairQA裡撈出opCmd
- opCmd->option.childCnt設0
	- NOW 那為什麼在[[NfcExcp_Push2Repaire]]的時候要先設15，不是白設了嗎？#2311 #問題集
	  :LOGBOOK:
	  CLOCK: [2023-05-19 Fri 10:39:07]
	  CLOCK: [2023-05-19 Fri 10:39:08]
	  :END:
- gRepairCnt++
- 如果gNfcExcpReapMgr是空的
	- 從Reap申請一個名為"NfcE"的空間，並讓gNfcExcpReapMgr指向此空間 - #ReapAlloc_Create()
	- 從gNfcExcpReapMgr申請一塊空間，大小為sizeof(EXCP_CTRL_T)，並讓gExcpCtrl向指此空間 - #ReapAlloc_Allocate()
	- NFC最大有幾個channel，以下動作就做幾次：
		- 從gNfcExcpReapMgr申請一塊空間，大小為sizeof(EXCP_REPAIR_RES_T)，並讓gExcpCtrl->nfcExcpRes[ch]指向此空間
			- NOW 為什麼要以nfc最大的channle來決定迴圈次數
			  collapsed:: true
			  :LOGBOOK:
			  CLOCK: [2023-05-19 Fri 10:45:49]
			  CLOCK: [2023-05-19 Fri 10:45:54]
			  :END:
			  像2311的nfc有4ch，但nfc2242的盤只有2ch，這樣ch2 ch3的部份不是就白做了嗎？ #2311 #問題集 #已解決
				- 答：
					- 因為2311的channel數macro不像2312
					- 2312有分NFC_CH_MAX_HW和NFC_MAX_CH_NUM
					  NFC_CH_MAX_HW是硬體最大的channel數
					  NFC_MAX_CH_NUM是自訂每個be的channel數
					- 2311只有一個NFC_MAX_CH_NUM，是硬體最大的channel數
					  所以只能用這樣的方式
		- 讓gExcpCtrl->freeSgd指向NULL - #NfcExcpResInitSgd()
	- 假設指定的channel是x，就讓gExcpResInUse的bit x設為1
	- NOW gExcpCtrl->nfcExcpRes[ch]->errcCmd = opCmd;
	  :LOGBOOK:
	  CLOCK: [2023-05-19 Fri 10:54:01]
	  :END:
	  gExcpCtrl->nfcExcpRes[ch]->repairBitMap = gLdpcDecErrBmp[opCmd->nfcTag].errBitmap;
	  gExcpCtrl->nfcExcpRes[ch]->offset = 0;
	  gExcpCtrl->nfcExcpRes[ch]->cwCnt = 0;
	  gExcpCtrl->nfcExcpRes[ch]->status = REPAIR_MGR_BUSY;
	- 回傳true