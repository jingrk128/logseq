- ### 拿opCmd所需記憶體
- [[NfcCom_AllocOpCmd()]]
	- 取出gNfcOpCmdMgr.head指向的記憶體，大小是NFC_OP_CMD_T
	- gNfcOpCmdMgr.head改為指向next
	- gNfcOpCmdMgr.freeTagCnt--;
	- opCmd->hwSglResIdx = INVALID_UINT16;
	- opCmd->parent = NULL;
	- opCmd->option.raw = 0;
	- NOW opCmd->nfcReason = NFC_RSP_CODE_OP_OK;
	  :LOGBOOK:
	  CLOCK: [2023-05-02 Tue 11:15:30]
	  :END:
	- NOW opCmd->allocSerial = gNfcOpCmdMgr.allocSerial ++;
	  :LOGBOOK:
	  CLOCK: [2023-05-02 Tue 11:15:25]
	  :END:
	- opCmd->dwCnt = 0;