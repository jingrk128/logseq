- XTE210_FIP_Specification_V1.0.pdf第15頁上方
	- 當FIX2FLH_EN=0，且FCTL_BUF_VALID不是1也不是0的話，FCTL_OTHER_SET的fixed value是哪一種配置？
- XTE210_FIP_Specification_V1.0.pdf 第23頁
	- FCTL_CHNL_SET的bit30是reserved，但為何FW裡在hal_flash_init()卻把bit30設1了？
	- FW裡把bit30取名為SET_SPR_INV_BYPASS，SPR是什麼意思？
		- 有可能是spare的意思
- XTE210_FIP_Specification_V1.0.pdf第25頁下方
	- 此處寫FCTL_SEED_MODE[6:3]可以設定tWPSTH
	- 但是XTE210_Flash_AC_Timing_mapping.pdf第二頁下方卻寫tWPSTH由HW決定
- XTE210_FIP_Specification_V1.0.pdf 第25頁下方
	- DQS_OEH是什麼？
- XTE210_FIP_Specification_V1.0.pdf 第38頁
	- FCON_INFO[7:0]，當要read ch0但不要read ch1的時候，為什麼是設00h or 02h or 03h？
	- 如果FCON_INFO[7:0]是0101b，則read ALL的時候它會輸出內容是ch1的嗎？
- XTE210_FIP_Specification_V1.0.pdf 第39頁中間
	- FCON_ROT_SAT和FCON_ROT_LEN是設定buffer，這個buffer的用途是什麼？
- XTE210_FIP_Specification_V1.0.pdf第49頁
	- FCON_ADR_GEN裡的bit13和bit9說會參考FCTL_OTHER_SET[28:24]，但FCTL_OTHER_SET[28:27]是reserved
	  id:: 64f5ae6b-381f-4ba2-b592-5fb131cc8fb9
- ### XTE210_FIP_Specification_V1.0.pdf第63頁中間
	- E3D是一塊RAM的名字，它的用途是什麼？為什麼FW裡要設成0x50000？
	- ans：[[E3D - End-to-End Error Detection]]
-
- XTE210_FIP_Specification_V1.0.pdf第7頁中間
	- FCTL_PIO_DAT這邊寫read/write的流程要設定FCTL_PIO_DAT和FCTL_PIO_DAT_H，但在第17頁FCTL_PIO_DAT_H的內容被劃刪除線了，是否代表read/write的過程只要設定FCTL_PIO_DAT就可以了？
- XTE210_FIP_Specification_V1.0.pdf第71、72頁
	- 71頁的DLL_PHASE_R_CTL[31:6]都是reserved，但是其中DLL_PHASE_R_CTL[25:24]又定義了功能
	- 72頁的DLL_PHASE_W_CTL也是一樣的狀況
- hal_flash.c 第164行 hal_flash_pio_write_data()
	- write data時，為什麼寫法是`reg[FCTL_PIO_DAT] = (data << 8) | data;`，而不是`reg[FCTL_PIO_DAT] = data;`？
- hal_flash.c 第241到258行 hal_flash_get_interface()
	- 為什麼把FLASH_IF_ONFI_v30和FLASH_IF_TOGGLE_v20都視為FLASH_IF_LEGACY？
- hal_flash.c 第871到875行
	- 為什麼要跳過buf[1]和buf[2]，這兩個addr不能使用嗎？
	- FCTL_OTHER_SET不會被更動，但為什麼要把FCTL_OTHER_SET的值備份到buf[3]？
- XTE210_RegSpec_System.pdf 第82、83頁
	- FAD指的是什麼？
-
- XTE210_FIP_Specification_V1.0.pdf 第45頁
	- FCON_DQ_BIT_REV的說明第三點，`IF the Flash CE0~15 is enabled more than one at the same time, the DQ bit reverse function will refer to least of enabled Flash CE.`，即使搭配此項說明下的範例，還是不懂它的意思
- XTE210_RegSpec_SEC.pdf 第39頁上方
	- ATARTDATA應該是打錯了，是STARTDATA才對
- hal_flash.c 第376到379行
	- `rdw_PKE_CTRL[PKE_STA] & CHK_CMDQ_FULL`應該要回傳TRUE才對
-
-
- 以下還沒問的：
- XTE210_FIP_Specification_V1.0.pdf第51頁中間
	- FCON_GEN_CONV_IDX4[14:12]是依nand一個block有多少page來選擇嗎？
- XTE210_RegSpec_System.pdf 第87頁中間
	- 關於CONOF和SONOF，是不是CONOF要enable，SONOF才能enable？
- XTE210_FIP_Specification_V1.0.pdf第7頁中間
	- 什麼是OR/AND result
- hal_flash_init()
	- ```
	  #if ENABLE_FAKE_FF_RETRY
	      rdw_FLASH_CH_ALL[FCTL_CNT_ONE] |= SET_COUNTER_MODE_EN;
	      rdw_FLASH_CH_ALL[FCTL_CNT_ONE] &= CLR_S_COUNT_ZERO;
	  #endif
	  ```
		- 為什麼enable count後，count卻設成zero？這樣有意義嗎？
- 在hal_flash_init()裡，以下只定一個channel就夠了嗎？
	- FCON_INFO[23:16] no write mask
	- FCON_FLH_FUNC[3] Don't mask sign-off by firmware
	- FCON_FCE_ENB[15:0] disable all ce
	- FCON_E3D_BASE[31:0] 0x50000
	- FCON_ADR_GEN[9] 開啟column addr自動產生
	- FCON_ADR_GEN[13] 產生的來源指定FSA
	- FCON_ADR_GEN[7] frame ptr auto update after address generate enable
	- FCON_ADR_GEN[8] frame ptr auto update next frame ptr when DMA done
	- FCON_FLH_FUNC[0] Sign-off the frames after uncorrectable error occurs
-
- ```
      if (rdw_FLASH_CTRL[FCON_FLH_FUNC] & FW_SGN_MSK_EN) {
          rdw_FLASH_CTRL[FCON_FLH_FUNC] &= ~FW_SGN_MSK_EN;
      }
  ```
	- 為什麼要先判斷？直接設定值不就好了嗎？
- FCON是什麼的縮寫？
- Inversion bypass是什麼？
- FCTL_CHNL_SET裡DUMMY_DATA的長度如何判斷要設多少？
- FCTL_OTHER_SET裡fixed value是做什麼用的？
- sign-off是什麼意思？Sign-off the frames是什麼意思？