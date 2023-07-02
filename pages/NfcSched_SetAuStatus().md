- ### 依照cwBitMap來將auStatusList內指定的元素設為指定的status
	- 依照pageOffset算出startAu
		- `startAu = (MOD(opCmd->cmd->request.rdReq.pageOffset, NFC_CONFIG_GET_USER_SECTOR_CNT())) / NAND_AU_SEC_CNT;`
		- NOW 為什麼要除以NFC_CONFIG_GET_USER_SECTOR_CNT()?
		  :LOGBOOK:
		  CLOCK: [2023-05-18 Thu 19:07:37]
		  :END:
		  這樣如果是plane1的話，原本要填到auStatusList裡plane1的位置不就會變成填到plane0了嗎？
	- auStatusIdx設為0
	- cwBitMap >>= startAu * 2;
	- 讀取cwBitMap，若不為0就反覆執行以下動作
		- 如果cwBitMap最低兩個bit有值
			- auStatusList[auStatusIdx]設定指定的status
			- 如果指定的status是NF_RET_LDPC_DECODE_FAIL的話
				- NOW 執行VendorSmart_SetAttributeVaule() 看不懂這是幹嘛的 #問題集
				  :LOGBOOK:
				  CLOCK: [2023-05-18 Thu 19:29:28]--[2023-05-18 Thu 19:29:30] =>  00:00:02
				  :END:
		- auStatusIdx++
		- cwBitMap >>= 2