- 若指定ch的ldma_done_statuts_fifo_left_cnt有值
- 重覆以下步驟，重覆次數為ldma_done_statuts_fifo_left_cnt的值
	- 讀取ldma_done_status_fifo_data
	- 讀取裡面的tag，以獲得opCmd
	- 把gFreeCfCnt[][]加回去 - #NfcSeqDrv_IncCfFreeCnt()
	- NOW **如果opCmd->nfcReason不是NFC_RSP_CODE_OP_OK的話** #待辦
	  :LOGBOOK:
	  CLOCK: [2023-05-12 Fri 21:39:49]
	  :END:
		- to do
		- 跳出迴圈
	- channel的error message填寫gCwMsgList[]和errBitmap - #HandleLdpcErrStatus()
	- NfcDrv_SubOpCnt(1)
	- 進gLdmaDoneHandle處理ldma_done_status_fifo_data的內容 - #NfcSchedHandleLdmaDone()