- ### step1.設定flash timeout threshold為80ms
- 以下每個channel都依序做一遍(依據NFC_MAX_CH_NUM)
	- 選擇channel #NfcDrv_EnableCh()
	- 設定flash timeout threshold為80ms - #NFCDRV_SET_FSH_TIMEOUT_THRESH()
- ### step2.畫出ce map
- 2-1.reset+read status
	- 透過reset+read status畫面出ce map - #NfcResetAllFlash() #待辦
- 2-2.READ_TOPO
	- 檢查 ONFI_PAD_LVSEL暫存器，如果是1.2V的話
		- 選擇所有channel - #NfcDrv_EnableCh()
		- 把所有channel的nfc設為toggle，並設定差動 - #NfcDrv_SetFlashBusMode() #待辦
	- 把每個channel的read success訊息的mask打開 - #NfcDrv_EnableStsReadOk() #待辦
	- 按照ce map把每個die都執行READ_TOPO
	  collapsed:: true
		- READ_TOPO有什麼作用？ #問題集
		- ```
		      while (ceMapTem)
		      {
		          if (ceMapTem & SET0)
		          {
		              cmd = NFC_Cmd_Aquire();
		              cmd->ops.raw = 0;
		              cmd->request.rdReq.die = die;
		              cmd->opType = OP_TYPE_READ_TOPO;
		              NFCInf_Sync_Req(cmd);
		          }
		          die++;
		          ceMapTem >>= 1;
		      }
		  ```
	- 把每個channel的read success訊息的mask關掉 - #NfcDrv_EnableStsReadOk()
	- 對每個die執行read status
		- 如果結果不是success，就把該顆die從ce map上刪掉
		- ```
		      die = 0;
		      ceMapTem = ceMap;
		      while (ceMapTem)
		      {
		          if (ceMapTem & SET0)
		          {
		              if (NF_RET_OP_OK != Nfc_ReadStatus(die))
		              {
		                  ceMap &= ~(1 << die);
		                  NfcDrv_SoftReset(die%4);
		              }
		          }
		          die++;
		          ceMapTem >>= 1;
		      }
		  ```
- 2-3.檢查每個die的id byte0，如果不是五大廠商的maker，就從ce map上刪掉
  collapsed:: true
	- ```
	      //1, FPGA,can't drive DQ down or up, minimal probalility of error. need read id.
	      //2, toshiba id cotain lun cnt
	  #if defined(FPGA) || defined (TOSHIBA_SELECT) || defined (YMTC_SELECT)
	      uint8_t bufTem[12] = {0};
	                        //hynix toshiba micron intel YM
	      uint8_t vendor[] = {0xAD,   0x98, 0x2C,  0x89,0x9B};
	      uint8_t vendorCnt = sizeof vendor;
	      gk_bool isGet = FALSE;
	      gk_bool isFirstId = TRUE;
	      uint8_t backup[6];
	      die = 0;
	      ceMapTem = ceMap;
	      while (ceMapTem)
	      {
	          if (ceMapTem & SET0)
	          {
	              isGet = FALSE;
	              Nfc_ReadFlashId(die, 0, 12, bufTem);
	              for (uint8_t i = 0; i < vendorCnt; i++)
	              {
	                  if (vendor[i] == bufTem[0])
	                  {
	  #if defined(TOSHIBA_SELECT) || defined(YMTC_SELECT)
	                      if (isFirstId)
	                      {
	                          isFirstId = FALSE;
	                          NfcExtratIdInfo(bufTem, backup);
	                      }
	                      else
	                      {
	                          NfcExtratIdInfo(bufTem, bufTem);
	                          DBG_ASSERT(MemUtil_MemCmp8(backup, bufTem, 6));
	                      }
	  #endif
	                      isGet = TRUE;
	                      break;
	                  }
	              }
	              if (!isGet)
	              {
	                   ceMap &= ~(1 << die);
	              }
	          }
	          die++;
	          ceMapTem >>= 1;
	      }
	  ```
- ### step3.設定lun的數量
- 根據id的byte2[1:0]來設定gNfcCfg.flashParam.lunPerTargetNum - #NFC_CONFIG_SET_LUN_CNT()
- ### step4.設定gNfcCfg參數
- 根據ce map決定ceMapPerCh、validChMap、totalDieCnt的值 - #NfcCfg_InitChCeMap() #待辦
- ### step5.設定flash timeout threshold為670ms
- 以下每個channel都依序做一遍(依據NFC_MAX_CH_NUM)
	- 選擇channel #NfcDrv_EnableCh()
	- 設定flash timeout threshold為80ms - #NFCDRV_SET_FSH_TIMEOUT_THRESH()
- ### step6.初始化gNfcSmart.opTimeoutCount
- 把gNfcSmart.opTimeoutCount設為0