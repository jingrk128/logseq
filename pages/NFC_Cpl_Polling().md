- ### 執行未完成的任務
- #2311 #trunk #svn25943 #DL_Flashdisk_ASIC_YMTC_X1_9050_CLIENT
- 如果gOpCnt有值 - #NfcDrv_GetOutstanding()
	- 設定所有已完成nfc status和ldma done的opCmd - #NfcDrv_IntService()
- 如果gNfcNandResetFlag有值 - #NfcSched_GetTimeoutResetFlag()
	- 執行timeout handle - #NfcExcp_HandleTimeout() #待辦
	- 結束此function
- 如果NfcGetBlockCbFlag是0 (應該是不可能為1) - #NfcGetBlockCbFlag()
	- 為什麼這邊的語法是`!NfcGetBlockCbFlag()`?加反相才處理不是很違反直覺嗎? #問題集
	- 如果有待執行的callback - #NfcIsPendCb()
		- 執行gNfcOpCmdMgr.pendCbQHead指向的callback並釋放cmd - #NfcOutPendCbQ()
		- 重覆執行到gNfcOpCmdMgr.pendCbQHead指向NULL為止
- 如果執行中的opCmd數量 > 未完成的cmd數量
	- 什麼時候會出現這種狀況？ #問題集 #已解決
		- ans：有tag剛被nfc執行完的時候，也就是還在gNfcRspQ的時候會出現這種狀況
	- 把還在gNfcRspQ裡的opCmd都執行NfcSched_SchedCmd() - #NfcSched_ScheduleRspQ()
- 每顆die都依序執行以下
	- ```
	          if(0 != gDieMap[dieIdx].vol)
	          {
	              continue;
	          }
	  ```
	- 上述的用意是什麼？ #問題集
	- 如果還有執行中的opCmd
		- 若這顆die有opCmd在WaitQueue裡面，就全部拉出來執行[[NfcSched_SchedCmd()]] - #NfcSched_ScheduleChCeWaitQ() #待辦
- 如果有還未被執行的cmd - #NfcIsPendCmd()
	- 如果資源足夠 - #NfcCheckSubmitResource()
		- 執行等待中的cmd - #NfcSched_RunPendCmd()
- 如果gRepairCnt有值 - #NfcExcp_GetRepairCnt()
	- (只有[[NfcExcp_Push2Repaire()]]才會執行gRepairCnt++)
	- 如果gExcpResInUse"沒有"值 - #NfcExcp_IsRepairMgrIdle()
		- (gExcpResInUse只有在[[NfcExcpGetNewRepairCmd()]]會被設0以外的值)
		- 處理unc流程 - #NfcExcp_SchedUncProc()
- 如果沒有等待執行的opCmd - #NfcCom_IsAllCmdIdle()
	- 如果沒有等待執行的cmd - #NfcIsPendCmd()
		- 如果沒有等待執行的call back - #NfcIsPendCb()
			- 執行[[Nfc_SchedulerSuspend()]]
				- 這是什麼? #問題集