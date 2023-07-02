- ### 初始化ldpc
- 如果gCurLdpcCodecMode是custom codec
	- decInit = Ldpc_DecInit;
	  encInit = Ldpc_EncInit;
- 如果是common codec
	- decInit = LdpcCommonCodec_DecInit;
	  encInit = LdpcCommonCodec_EncInit;
- 檢查ldpc dec0和1的ldma是否為idle - #LDMA_IS_IDLE()
	- 若不是，就再次檢查，最多10次
- 以下依序把每個ldpc decode都做一次
	- #Ldpc_Config() #待辦
	- #decInit() #待辦
- 以下依序把每個實際存在的ch做一次
	- 檢查ldpc enc buffer若為不為空 - #NFCDRV_LDPC_CHECK_INPUT_BUFF_EMPTY()
		- 印出ch編號以及Encoder busy
	- #encInit() #待辦
- 準備soft decode資源 #Ldpc_SdResourceInit() #待辦
- 檢查ldpc dec0和1的ldma是否為idle - #LDMA_IS_IDLE()
	- 若不是，就再次檢查，最多10次
- #LDMA_SOFT_RESET()