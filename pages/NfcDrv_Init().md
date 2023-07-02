- ### 初始化nfc暫存器
- 把sgl和cmd fifo設為init status - #NFCDRV_CMD_SGL_INIT()
- #NfcDrv_CmdQueueInit()
- #NfcDrv_DllConfig()
- 以下對所有channel都做：
	- NFCDRV_SOFT_RESET(); // TODO, check can read from NFC_CH_ALL.
	      NFCDRV_SET_WDMA_TIMEOUT_THRESH(NFC_WDMA_TIMEOUT_VAL); 
	      NFCDRV_SET_RDMA_TIMEOUT_THRESH(NFC_RDMA_TIMEOUT_VAL);
	      NFCDRV_SET_FSH_TIMEOUT_THRESH(NFC_OPER_TIMEOUT_VAL);
	      // disable init interruption
	      NFCDRV_TURN_OFF_INT();
	      // init status fifo mask
	      NFCDRV_DIS_ALL_STS();
	      NFCDRV_EN_STS_FLASH_FAIL();
	      NFCDRV_EN_STS_FLASH_TIMEOUT();
	      NFCDRV_EN_STS_WDMA_TIMEOUT();
	      NFCDRV_EN_STS_RDMA_TIMEOUT();
	      NFCDRV_EN_STS_WR_OK();
	      NFCDRV_EN_STS_ERASE_OK();
	      NFCDRV_EN_STS_MISC_OK();
	      // init scrambler
	      NfcDrvInitScrMode(NFC_CONFIG_GET_SCR_MODE());
	- 設定Padding - #NfcDrv_InitPaddingDummyData()
	- NFCDRV_SET_EDO_MODE();
	  id:: 6481c628-cb34-45ef-a261-6d62c9f15884
	- 設定bus timing - #NfcDrvSetCtrlAcTiming();
	- 設定tWP - #NfcDrvSetTwpByFreq()
		- 在[[NfcDrv_DllConfig()]]已經設定過了
	- 設定tR、tProg、tBers - #NfcDrvSetFlashLatency();
	- 設定DS(Drive Strength) - #NfcDrv_SetAllCtrlIoDriver(gNfcCfg.ifcfg[NfcCfgIf_Default].IOdriving);
	- 把data io設為pull down - #NfcDrvPutDownDataIo();
	- 開啟WP pin pad的input和ouput - #EN_FSH_WP_PAD();
	- 關閉WP pin的output - #DIS_FSH_WP();
	- 設定scramble seed AGT11-AGT15 - #NfcDrvSetSeedAg();
	- 把LDPC相關中斷都關掉、設定LDPC相關的threshold - #NfcDrvSetLdpcPara();
	- 把DQS_N和REN_N關掉 - #NfcDrv_SetDqsReSignalMode();
	- 把REN、DQS、DATA和HW_AUTO的ODT都關掉 - #NfcDrv_DisCtrlIoOdt();
	- gOpCnt = 0;