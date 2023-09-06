- 如果opCmd->node.next有連到別的queue，就將opCmd從該queue中抽掉 - #DList_Delete()
- 判斷以下三個條件
	- opType是OP_TYPE_READ
		- 判斷這點是多餘的吧?本來就只有read才會進來吧? #問題集 #已解決
		  :LOGBOOK:
		  CLOCK: [2023-05-18 Thu 21:49:35]
		  :END:
			- 答：不是read也會進來，詳見[NFC_SCHED_RECVD_ERR](646d7d20-c17a-4298-8716-f1f74fbbf181)
	- nfcReason是NFC_RSP_CODE_OP_OK
	- gNfcRrMode不是0
- 條件都成立的話：
	- 把opCmd->node推到gNfcRepairQA[ch] - #NfcExcp_Push2Repaire()
	- 處理unc流程 - #NfcExcp_SchedUncProc()
	- 回傳RET_BUSY並結束
- 回傳RET_CONTINUE並結束