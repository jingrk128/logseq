- ### 分配nfc hw cmd queue
- ##### CMD_QUEUE_BYTE_SIZE()是所有ce cmd q的大小加總
- ##### HP_BYTE_SIZE()是一個ce high priority cmd q的大小
- 從NFC_EXIST_CHS()和NFC_EXIST_CES()取得ch和ce的數量 - #NfcDrvGetChCeCnt()
- 算出一個ce的low priority size並存進lpSize，單位是byte，但要對齊dw
	- lpSize = (CMD_QUEUE_BYTE_SIZE() / ceCnt - HP_BYTE_SIZE())/4*4
	- 後面的/4*4是為了對齊dw
- 算出不足一個dw部份加起來總共有幾byte並存進remainder，但一樣要對齊dw
	- remainder = (CMD_QUEUE_BYTE_SIZE() - ((lpSize + HP_BYTE_SIZE() ) * ceCnt))/4*4
- 把remainder均分給每個channle，加上lpSize並對齊dw後後存進lpSizeExtra
	- lpSizeExtra = lpSize + (remainder / chCnt)/4*4
	- lpSizeExtra會分配給每個channel的第一個ce
- 取得cmd q start addr的offset，存進current
- 從NFC_MAX_CH_NUM和NFC_CONFIG_CH_MAP()看有幾個ch，以下每個ch都做一次
	- 選擇ch
	- 清掉cmd_ptr_init_done - NFCDRV_EN_CMD_PTR_CLEAR_DONE()
	- 從NFC_MAX_CE_NUM_PER_CH和NFC_CONFIG_CE_MAP_PER_CH()看有哪些ce
	- 以下每個ce都做一次
		- 如果是第一個ce，lpSizeTem = lpSizeExtra
		- 如果不是，lpSizeTem = lpSize
		- 設定ce的cmd q
		  NFCDRV_LP_START_REG(ce) = current;
		  NFCDRV_LP_TAIL_REG(ce) =  NFCDRV_LP_START_REG(ce);
		  NFCDRV_LP_HEAD_REG(ce) =  NFCDRV_LP_START_REG(ce);
		  NFCDRV_LP_END_REG(ce)  =  NFCDRV_LP_START_REG(ce) + lpSizeTem - 1;
		  NFCDRV_HP_START_REG(ce) = NFCDRV_LP_START_REG(ce) + lpSizeTem;
		  NFCDRV_HP_TAIL_REG(ce)  = NFCDRV_HP_START_REG(ce);
		  NFCDRV_HP_HEAD_REG(ce)  = NFCDRV_HP_START_REG(ce);
		  NFCDRV_HP_END_REG(ce)   = NFCDRV_HP_START_REG(ce) + HP_BYTE_SIZE()-1;  
		  current = NFCDRV_HP_END_REG(ce) + 1;
		- 設定cmd fifo cnt - NfcSeqDrv_SetCfFreeCnt(ch, ce, NfcSeqDrv_GetCfFreeDwCnt(ce, 0))
	- 設定cmd_ptr_init_done - NFCDRV_EN_CMD_PTR_INIT_DONE()