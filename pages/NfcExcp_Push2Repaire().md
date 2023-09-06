- 初始化read retry的table - #NfcExcp_InitReadRetryCal()
	- NOW 為什麼每次unc都要執行? 在nfc init時做一次不就好了嗎? #問題集
	  :LOGBOOK:
	  CLOCK: [2023-05-18 Thu 22:11:53]
	  CLOCK: [2023-05-18 Thu 22:11:56]
	  :END:
	- 把opCmd-推到gNfcRepairQA[opCmd->target.ch] - #DList_PushTail()
	- opCmd->option.childCnt設為15
		- 之後在[[NfcExcp_SchedUncProc()]]->[[NfcExcpGetNewRepairCmd]]裡，會因為這個opCmd被推到gNfcRepairQA的關係，而把opCmd->option.childCnt設為0，所以這邊設為15的動作是多餘的，可以刪掉 #改進空間
		- childCnt是幹嘛的? #問題集 #已解決
		  :LOGBOOK:
		  CLOCK: [2023-05-18 Thu 22:15:04]
		  CLOCK: [2023-05-18 Thu 22:15:09]
		  :END:
			- ans: childCnt用途有以下兩種
				- 在[[NfcExcp_SchedUncProc()]]裡，如果childCnt為0，才會做read retry
				- 在[[NFCInf_Sync_Req()]]和[[NFCInf_MultiSync_Req()]]必須childCnt有值，才會去執行[[NfcSched_SchedChildCmd()]]
	- gRepairCnt++