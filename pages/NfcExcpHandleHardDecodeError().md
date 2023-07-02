- ### 準備hard retry
- 以下條件都符合嗎？
  1.gNfcRrMode不為0
  2.opCmd->option.retrySeq小於LDPC_MAX_SD_VTH_SHIFT
	- 是
		- 若記憶體資源足夠： - #NfcCheckSubmitResource()
			- 建立chlid - #NfcExcpCreateNandRetryChild()
			- 把child推入WaitQueue - #DList_PushTail()
			- child->state = NFC_SCHED_WAIT_SCHED;
			- opCmd->option.retrySeq++;
			- 根據opCmd->state做不同的處理 - #NfcSched_SchedCmd()
		- 回傳RET_BUSY並結束
- opCmd->option.subState = SUB_STATE_WAIT_SOFT_RETRY;
  opCmd->option.ldpcRetrySeq = 0;
  opCmd->option.retrySeq = 0;
- 回傳RET_OK