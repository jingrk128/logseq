- 參數：errBitmap
- 如果reg system_ctl1的fsh_dqs_miss_int是1的話
	- gNfcSmart.dqsMissCount[opCmd->target.ch]++;
	- 清掉nfc interrupt status - NFCDRV_CLR_NFC_INT_STATUS()
		- DONE register table說要用寫1的方式清掉 #問題集
		  :LOGBOOK:
		  CLOCK: [2023-05-12 Fri 22:36:42]
		  CLOCK: [2023-05-12 Fri 22:36:44]--[2023-05-18 Thu 13:58:12] =>  135:21:28
		  :END:
		  但為什麼NFCDRV_CLR_NFC_INT_STATUS()是用寫0的方式清？
			- 而且為什麼讀的時候是讀fsh_dqs_miss_int
			  但清的時候是清ldpc_enc_int？
		- 如果sgl index不是0xffff就釋放sgl - **#NfcSgl_ReleaseIndex()** #待辦
		  :LOGBOOK:
		  CLOCK: [2023-05-12 Fri 22:40:33]
		  :END:
			- NOW 有必要判斷sgl index是不是0xffff嗎？ #問題集
			  :LOGBOOK:
			  CLOCK: [2023-05-18 Thu 11:53:49]
			  :END:
			  因為只有read會進[[NfcSchedHandleLdmaDone()]]
			  且[[NfcDirect_ReadPageData()]]裡如果index得到0xffff的話也不會做下去了
		- gNfcSmart.totalReadCnt++;
		- 如果errCwCnt不是0 且opCmd不是ChildCmd
			- 把範圍內的au都設為指定的status - #NfcSched_SetAllAuStatus()
			  :LOGBOOK:
			  CLOCK: [2023-05-12 Fri 22:42:28]
			  CLOCK: [2023-05-12 Fri 22:42:29]
			  :END:
		- **if(gNfcRrErrInjAct && (0 == rspMsg->errCwCnt) && !opCmd->cmd->ops.nfcOpOption.isSlcMode)** #待辦(不急)
			- to do
		- errCwCnt是0嗎？
			- 不是
				- maxDiffZeroOne設為0
					- maxDiffZeroOne是記錄所有偶數cw中最大的diffZeroOne
				- cwIdx設為0
				- 讀取errBitmap的值，若不為0就反覆以下動作
					- 若errBitmap的bit0是1
						- 如果subType是SUB_TYPE_NAND_RETRY  && cwIdx是偶數
							- tempDiffZeroOne = gCwMsgList[cwIdx].fail.diffZeroOne & 0x7f;
								- NOW diffZeroOne 是什麼意思？
								  :LOGBOOK:
								  CLOCK: [2023-05-24 Wed 16:40:22]
								  CLOCK: [2023-05-24 Wed 16:40:25]
								  :END:
							- 如果subType是SUB_TYPE_NAND_RETRY && maxDiffZeroOne比tempDiffZeroOne還小
								- NOW 多判斷了subType==SUB_TYPE_NAND_RETRY，可以拿掉 #改進空間
								  :LOGBOOK:
								  CLOCK: [2023-05-24 Wed 16:35:16]
								  CLOCK: [2023-05-24 Wed 16:35:27]
								  :END:
								- maxDiffZeroOne = tempDiffZeroOne
						- gCwMsgList[cwIdx].fail.errReason有包含EMPTY嗎？
							- 有 - 若cwIdx是x，就把decEmptyBitmap的第x bit設1
							- 沒有
								- 若cwIdx是x，就把decFailBitmap的第x bit設1
								- 若gCwMsgList[cwIdx].fail.errReason等於0x02，gNfcSmart.decOkCrcFailCnt就+1
					- cwIdx++
					- errBitmap >>= 1
				- 如果maxDiffZeroOne不是0 && 
				  (opCmd->parent->best01Diff[LDPC_MAX_SD_VTH_SHIFT-1] > maxDiffZeroOne)
					- 調整bestRrtIndex[]和best01Diff[] - #NfcSched_InsertBestRrt()
					- 如果insertUnc不為0：
						- 這個判斷式是和ENABLE_NFC_RR_ERR_INJ相關的東西，也許不重要
						- 把retrySeq設為slcTotalRRCnt或xlcTotalRRCnt - #NfcCfg_GetRetryCnt()
				- decFailBitmap有值嗎？
					- 有 - opCmd->cmd->result = NF_RET_LDPC_DECODE_FAIL
					- 沒有
						- decEmptyBitmap有值嗎？
							- 有 - opCmd->cmd->result = NF_RET_EMPTY;
				- opCmd不是ChildCmd 且 opType是OP_TYPE_READ？
					- NOW opType一定是OP_TYPE_READ才進得來吧？
					  :LOGBOOK:
					  CLOCK: [2023-05-18 Thu 17:55:13]
					  CLOCK: [2023-05-18 Thu 17:55:18]
					  :END:
					- 正確
						- decEmptyBitmap有值嗎？
							- 把auStatusList[]按照decEmptyBitmap將對應的元素設為NF_RET_EMPTY- #NfcSched_SetAuStatus()
						- decFailBitmap有值嗎？
							- 有
								- opCmd->state = NFC_SCHED_RECVD_ERR;
								- gNfcRrMode是RR_MODE_NONE嗎？
									- 是 - 待會要設定的status為NF_RET_LDPC_DECODE_FAIL
									- 不是 - 待會要設定的status為NF_RET_RD_OVER_LIMIT
								- 把auStatusList[]按照decFailBitmap將對應的元素設為指定的status - #NfcSched_SetAuStatus()
								- gLdpcDecErrBmp[tag].errBitmap設為decFailBitmap - #NfcExcp_SetErrBitmap()
							- 沒有 - opCmd->state = NFC_SCHED_DONE
					- 不正確
						- opCmd->state = NFC_SCHED_DONE
			- 是
				- opCmd->cmd->result = NF_RET_OP_OK;
				- opCmd->state = NFC_SCHED_DONE;
				- opCmd是ChildCmd嗎？
					- 不是
						- 不做事
					- 是
						- opCmd->option.subType是SUB_TYPE_NAND_RETRY嗎？
							- 調整bestRrtIndex[]和best01Diff[] - #NfcSched_InsertBestRrt()
			- 把opCmd->node抽掉
			- 把opCmd->node推進gNfcRspQ
		-