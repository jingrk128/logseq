- ### 送出read retry cmd
- opType是 且 option.subType是SUB_TYPE_SOFT_RETRY 嗎？
	- NOW 為什麼soft retry要特別處理?
	  :LOGBOOK:
	  CLOCK: [2023-05-19 Fri 19:34:53]
	  CLOCK: [2023-05-19 Fri 19:34:55]
	  :END:
	- 是
		- 執行soft retry - #NfcSched_HandleSoftDecodeRead() #待辦
		- 若soft retry的cmd成功送出
			- gOpCnt減少(LDPC_RETRY_READ_COUNT-1)個 - #NfcDrv_SubOpCnt()
	- 不是
		- 依參數執行direct或template cmd - #NfcSched_ConvertCmd()