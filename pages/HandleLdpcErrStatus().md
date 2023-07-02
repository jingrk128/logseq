- ### 讀取LDPC_DEC_ERR_STATUS_DATA，並設定gCwMsgList[]和errBitmap
- 外部會帶參數cnt進來
- cnt為以下迴圈的次數
	- 從指定的channel讀取LDPC_DEC_ERR_STATUS_DATA暫存器，存到status
	- 從status算出cwIdInOp
		- cwIdInOp = status.fail.cwId + status.fail.dmaIdx * NFC_CONFIG_GET_CW_CNT_PER_PAGE();
			- status.fail.cwId是第幾個cw，從0開始
			- status.fail.dmaIdx目前看到一直都是0，不知道什麼時候才不會是0 #問題集
			  :LOGBOOK:
			  CLOCK: [2023-05-18 Thu 15:53:05]
			  :END:
				- 目前自己做實驗看到的是，大部份都是0
				- 只有在multi plane，read到plane1的時候才會變1，但read retry到plane1的部份又變成0了
				- 如果只有single plane去read plane1，dmaIdx也是0
			- NFC_CONFIG_GET_CW_CNT_PER_PAGE() 是 32
	- gCwMsgList[cwIdInOp] = status;
	- 設cwIdInOp為x，把errBitmap的bit x設為1