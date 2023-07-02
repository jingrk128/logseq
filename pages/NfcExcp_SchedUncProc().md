- ### 處理unc
- 以下是迴圈A，NFC最大支援幾個channel，迴圈A就做幾次：
  ch初始值是0，迴圈每重覆一次，ch就+1
	- 如果gExcpResInUse的bit(ch)是0
	  就設定該ch的gExcpCtrl->nfcExcpRes - #NfcExcpGetNewRepairCmd()
	- 如果NfcExcpGetNewRepairCmd()回傳false，就跳過這次迴圈，換下一個ch
	- 從gExcpCtrl->nfcExcpRes撈出opCmd
	- 如果opCmd->option.childCnt不是0，就跳過這次迴圈，換下一個ch
	- 以下內容是迴圈B，當isContinue設true時就會一直重覆
		- 把isContinue設false
		- 如果opCmd->option.subState是SUB_STATE_NORMAL：
			- opCmd->option.retrySeq = 0;
			  opCmd->option.ldpcRetrySeq = 0;
			  opCmd->option.subState = SUB_STATE_WAIT_NAND_RETRY;
			- 執行hard retry
		- 如果opCmd->option.subState是SUB_STATE_WAIT_NAND_RETRY
			- 處理hard retry - #NfcExcpHandleHardDecodeError()
			- NfcExcpHandleHardDecodeError()回傳RET_OK嗎？
				- 不是 - 強制結束迴圈B
				- 是 - 繼續往下做
		- 如果opCmd->option.subState是SUB_STATE_WAIT_SOFT_RETRY
			- 處理soft retry - #NfcExcpHandleSoftDecodeError()
			  id:: 646c8278-0212-412e-8ff2-4f5ad8275241
			- NfcExcpHandleSoftDecodeError()回傳RET_OK嗎？
				- 不是 - 強制結束迴圈B
				- 是 - 繼續往下做
		- 如果opCmd->option.subState是SUB_STATE_NONE_CHK
			- 看這次是做哪幾個cw，就把nfcExcpRes[ch]->repairBitMap對應的bit清掉
			- repairBitMap不是0 且 opCmd->cmd->result不是NF_RET_ABANDON，正確嗎？
				- 正確
					- opCmd->option.subState = SUB_STATE_NORMAL;
					- 把isContinue設為true
				- 不正確
					- opCmd->state是NF_RET_ABANDON嗎？
						- 不是
							- opCmd->state = NFC_SCHED_DONE;
								- gLdpcDecErrBmp[opCmd->nfcTag].errBitmap 是0嗎？
									- 是
										- 算出一共read幾個au
											- 即使只有讀取到某個au的1個sector，就算是取讀那了那個au
											- 此段code如下
											- ```auCnt=(opCmd->cmd-request.rdReq.pageOffset+opCmd->cmd-request.rdReq.reqLen+7-(opCmd->cmd-request.rdReq.pageOffset&(~7))) >> 3;
											  ```
											- 簡單來看可以以下是這樣
											- `auCnt=(pageOffset+reqLen+7-pageOffset&(~7)) >> 3;`
												- pageOffset+rdReq是算出最後read到第幾個sector
												- +7是為了如果尾段沒有read滿一個au，就當作是有讀了那個au
												- - pageOffset&(~7)是考量到如果起點是從某個au的中間開始read的話，就當作是有讀了那個au
										- 檢查auStatusList
											- 只要有任何一個au status是EMPTY，則cmd->result = NF_RET_EMPTY
											- 若都沒有EMPTY，則opCmd->cmd->result = NF_RET_RD_OVER_LIMIT
										- 如果是slc，則gNfcSmart.slcRetryOkCnt+1
										- 如果是xlc，則gNfcSmart.xlcRetryOkCnt+1
									- 不是
										- 按照bitmap把au status設為NF_RET_LDPC_DECODE_FAIL - #NfcSched_SetAuStatus()
										- 如果是slc 且 gUncDbgMessageEn有開啟
											- gNfcSmart.slcRetryFailCnt++;
											- 印出以下格式資訊：
												- ```
												  [NFC]SLC_UNC-1, die:0,blk:3,pg:0,off:0,len:32.
												  SLC Retry OK:0, fail:1.
												  ```
										- 如果是xlc 且 gUncDbgMessageEn有開啟
											- gNfcSmart.xlcRetryFailCnt++;
											- 印出以下格式資訊：
												- ```
												  [NFC]UNC-1, die:0,blk:3,pg:0.
												  XLC Retry OK:0, fail:1, SD OK CW:0.
												  ```
								- gExcpCtrl->nfcExcpRes[ch]->status = REPAIR_MGR_IDLE;
								- 檢查gExcpResInUse並釋放gNfcExcpReapMgr和gExcpCtrl - #NfcExcpFreeExcpRes() #待辦
								- 結束cmd - #NfcSched_HandleDone()
								- 處理gExcpCtrl->nfcExcpRes - #NfcExcpGetNewRepairCmd()
									- 如果NfcExcpGetNewRepairCmd()回傳TRUE：
										- 從gExcpCtrl->nfcExcpRes[ch]撈出opCmd
										- gExcpCtrl->nfcExcpRes[ch]->status = REPAIR_MGR_BUSY;
											- NOW 已經在NfcExcpGetNewRepairCmd()設了為什麼還要再設一次？
											  :LOGBOOK:
											  CLOCK: [2023-05-23 Tue 20:14:10]
											  CLOCK: [2023-05-23 Tue 20:14:12]
											  :END:
										- 把isContinue設為true
						- 是
							- opCmd->state = NFC_SCHED_DONE;
							- gExcpCtrl->nfcExcpRes[ch]->status = REPAIR_MGR_IDLE;
							- 檢查gExcpResInUse並釋放gNfcExcpReapMgr和gExcpCtrl - #NfcExcpFreeExcpRes() #待辦
							- 結束cmd - #NfcSched_HandleDone()
		- 如果isContinue是true，就再做一次迴圈B
-
	-
	-