- ### 設定dll
- 把ck1_soft_rst_ctl_pw1的ck1_dll_srst_n設0 - CK1_DLL_SRST_EN()
- ### 設定DLL_FSH_CLK_CTRL和DLL_DQS_CTRL
	- 如果nfcFreq小於200MHz
		- R32_WRITE(0x54700100, 0x00A07E7E);
		  R32_WRITE(0x54700104, 0x00A07E7E);
		  R32_WRITE(0x54700108, 0x00A07E7E);
		  R32_WRITE(0x5470010C, 0x00A07E7E);
	- 如果大於等於200MHz
		- R32_WRITE(0x54700100, 0x00A00606);
		  R32_WRITE(0x54700104, 0x00A00606);
		  R32_WRITE(0x54700108, 0x00A00606);
		  R32_WRITE(0x5470010C, 0x00A00606);
- delay 1us - `for (volatile uint32_t i = 0; i < 500; i++);`
- ck1_soft_rst_ctl_pw1的ck1_dll_srst_n設1 - CK1_DLL_SRST_RELEASE()
- ### 設定tWP (WE_n LOW pulse width) - NFCDRV_SET_CTL_EXT_TWP()
	- 選擇所有nfc channel
	- 如果nfcFreq<80MHz，tWP=0 (tWP = 1 flash clock cycle)
	- 如果160MHz> nfcFreq >=80MHz，tWP=1 (tWP = 2 flash clock cycle)
	- 如果320MHz> nfcFreq >=160MHz，tWP=2 (tWP = 3 flash clock cycle)
	- 如果nfcFreq>=320MHz，tWP=3 (tWP = 4 flash clock cycle)
	-