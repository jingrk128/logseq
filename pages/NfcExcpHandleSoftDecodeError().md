- ### 準備soft retry
- 判斷以下條件是否都符合？
  1.gNfcRrMode是RR_MODE_BOTH
  2.opCmd->bestRrtIndex[opCmd->option.retrySeq] 不是 INVALID_RRT(0xff)
  3.opCmd->option.retrySeq小於指定次數 - #NfcCfg_GetRetryCnt()
	- 是
		- 確認記憶體資源是否足夠 - #NfcCheckSubmitResource()
			- 是
				- 建立soft chlid - #NfcExcpCreateSoftRetryChild()
				- 把child推入WaitQueue - #DList_PushTail()
				- child->state = NFC_SCHED_WAIT_SCHED;
				- opCmd->option.retrySeq++;
				- 根據opCmd->state做不同的處理 - #NfcSched_SchedCmd()
		- 回傳RET_BUSY並結束
- opCmd->option.subState = SUB_STATE_NONE_CHK;
- 回傳RET_OK