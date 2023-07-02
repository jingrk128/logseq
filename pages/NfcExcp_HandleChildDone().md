- ### 結束child cmd
- 如果dataBuf.dataAddr不是unll 且 dataAddr是sgl：
	- NOW child cmd的sgl是依據什麼來決定使用uni_buf或sgl mode？
	  :LOGBOOK:
	  CLOCK: [2023-05-21 Sun 18:59:46]
	  CLOCK: [2023-05-21 Sun 18:59:48]
	  CLOCK: [2023-05-21 Sun 19:00:02]
	  :END:
	- 釋放掉sgl內的所有sgd - #NfcExcpResFreeSgd() #待辦
- 如果result是NF_RET_OP_OK的話：
	- NOW 有什麼情況是result不是ok，但卻會進NfcExcp_HandleChildDone()？
	  :LOGBOOK:
	  CLOCK: [2023-05-21 Sun 19:03:56]
	  CLOCK: [2023-05-21 Sun 19:04:08]
	  :END:
	- 看這次retry是哪幾個cw，就把gLdpcDecErrBmp[tag].errBitmap對應的bit給清零
	- parent->option.subState = SUB_STATE_NONE_CHK;
	- 如果是hard retry：gNfcSmart.nandRetryOkCwCnt += gExcpCtrl->nfcExcpRes[ch]->cwCnt;
	- 如果是soft retry：gNfcSmart.softRetryOkCwCnt += gExcpCtrl->nfcExcpRes[ch]->cwCnt;
- 如果result是NF_RET_OP_TIMEOUT、NF_RET_DMA_TIMEOUT、NF_RET_ABANDON其中的任何一個的話：
	- parent->cmd->result = child->cmd->result;
	  parent->option.subState = SUB_STATE_NONE_CHK;
- 釋放child->cmd - #Nfc_FreeCmd()
- 釋放child - #NfcCom_FreeOpCmd()
- parent->option.childCnt--;
- 繼續處理unc - #NfcExcp_SchedUncProc()