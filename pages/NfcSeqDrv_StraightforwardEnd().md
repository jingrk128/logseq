### 為cmd作結尾
	- 填入eos
	- 把header補上cmd的長度
		- 把cmd丟進cmd q memory - [[NfcSeqDrvCopyToCmdQ()]]
		- 計算cmd fifo剩餘多少 [[gFreeCfCnt[opCmd->target.ch][opCmd->target.ce] -= cmdCnt;]]
		- 記錄opCmd->dwCnt
		- gOpCnt加1 - #NfcDrv_AddOpCnt()