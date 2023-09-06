- 9050之後推出的顆粒都要加上這個patch
- 實際做法為每做一次erase，後面都加上lun set feature
- 嘗試過以下幾種方法
	- direct mode在erase的cmd q的後面，EOS的前面插入lun set feature
		- 失敗，推斷原因為header的type和rw_sel都不符合lun ser feature的設定
	- direct mode在erase的cmd q的後面，EOS的前面插入新的header+lun set feature
		- 新的header的type要設non data + rw_sel要設1才會成功，少設其中一個都會失敗
	- template mode在erase template table和cmd q後面直接接lun set feature
		- 失敗，推斷原因為header的type和rw_sel都不符合lun ser feature的設定
	- template mode在erase template table直接接lun set feature
	  cmd q後面接新的header+header+lun set feature
		- 失敗，新的header的type設non data + rw_sel設1也失敗
		- 新的header後面不論有沒有加dma都失數
	- 新增set lun feature的template table，並在template erase的eos後面跑template set lun feature
		- 失敗，原因不明
		- 但是也不推薦這個做法，因為
	- 在erase流程跑到NFC_SCHED_DONE的時候再執行lun set feature
		- 成功
		- 但是跑lun set feature之前不能先執行NFCInf_Wait_All_Req_Done()
			- 因為這樣erase和lun set feature之間有可能會插入別的cmd，這樣不確定是否會有問題
			- Johnny認為的確會有這個問題
	- 在NfcTemplate_EraseBlock()結束的前一行直接執行Nfc_LunSetFlashFeature()
		- 會進trap，原因如下：
		- 執行Nfc_LunSetFlashFeature()後，進入NfcSchedHandleCmdNfcStatus()時，會拉到erase的opCMD
			- 因為是erase先放進cmd q memory的
		- 但是erase的opCmd沒有被推入gNfcWaitRspQ
		- 所以問題就在NfcSchedHandleCmdNfcStatus()最後執行DList_Delete(&opCmd->node);的時候
		- DList_Delete()會將opCmd->node.prev指向到別的地方
		- 恐怖的是此時opCmd->node.prev是null，所以會引發trap
	- 在NFCInf_Req_Submit()、NFCInf_Sync_Req()、NFCInf_MultiSync_Req()裡
	  在opCmd被推入gNfcWaitRspQ的下一行執行
		- ```
		  #if (defined(YMTC_SELECT) && defined(SUPPORT_ELITE_IN_CLIENT))
		      if(OP_TYPE_ERASE == opCmd->cmd->opType)
		          YmtcErasePatch(opCmd->cmd->request.erReq.die);
		  #endif
		  ```
		- ### 成功
- ### 在direct mode erase時，不用加這個patch，feature的值也會自動變成我們要的
	- 這是我弄錯了！！這是因為我的code在direct erase function的最後加了lun set feature的關係
- ### 加入erase patch的影響
	- 跑erase的流程為：
		- erase cmd放進WaitRspQ->等erase 的state變成NFC_SCHED_DONE->釋放cmd和opCmd
	- 加入patch後的流程：
		- erase cmd放進WaitRspQ->
		  patch cmd放進WaitRspQ->**等patch的state變成NFC_SCHED_DONE**->釋放patch cmd和opCmd->
		  等erase的state變成NFC_SCHED_DONE->釋放erase cmd和opCmd
		- 其中"等patch的state變成NFC_SCHED_DONE"的時候，在等到之前，erase的state也會變成NFC_SCHED_DONE
		- 所以回到"等erase的state變成NFC_SCHED_DONE"這個步驟時，就不需要等待了
		- 而程式的寫法上，因為"釋放erase cmd和opCmd"是包在`while (!NfcSched_IsCmdDone(opCmd))`裡面
		- 也就是說，必須要有等待這個動作，後續才會執行釋放；而現在沒有等待了，所以當下並不會釋放
		- 要等到執行`NfcCheckSubmitResource()`或`NFC_Cpl_Polling()`才會釋放
- ### 測試sync erase有跑patch和沒跑patch的時間差異
	- 測試只跑一次erase+patch的時間 250997cycle
	- 測試只跑一次erase的時間(沒有patch) 250459cycle
	- 有跑patch只差了538cycle
	- 一個cycle = 1/25000000秒