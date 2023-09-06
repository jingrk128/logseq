- 用途是記錄當前的read retry是第幾次
- 遇到decode fail的時候會用到
- decode fail時，會進到[[NfcSchedHandleError()]]
	- 在 [[NfcExcp_Push2Repaire()]] 會把childCnt設為15
		- 這一步應該是多餘的，可以不做 #改進空間
	- 在[[NfcExcp_SchedUncProc()]]，
		- 會判斷透過gExcpResInUse判斷該顆die所在的ch是否正在執行read retry
			- 如果有，就什麼也不做
			- 如果沒有，就會進入[[NfcExcpGetNewRepairCmd()]]
				- 如果gNfcRepairQA不是空的，就會
					- 從gNfcRepairQA的head拉出一個opCmd
					- 設opCmd->option.childCnt=0
					- 把gExcpCtrl->nfcExcpRes[ch]->errcCmd設為該opCmd
				- 如果該ch的gNfcRepairQA是空的，就直接跳出NfcExcpGetNewRepairCmd()
		- 檢查gExcpCtrl->nfcExcpRes[ch]->errcCmd的option.childCnt是否為0
			- 是0才會做read retry
			- 為什麼發生read第一次發生decode fail並進入[[NfcExcp_SchedUncProc()]]的時候，明明還沒有做過read retry，但是在`while (isContinue)`之前印出opCmd->option.childCnt，值應該是15才對(在[[NfcExcp_Push2Repaire()設的]])，為什麼實際上是1？ #問題集 #已解決
				- 答：因為此時的opCmd很可能並不是剛剛decode fail的opCmd，而是gExcpCtrl->nfcExcpRes[ch]->errcCmd，可以把tag或row addr印出來比對看看
- 當使用sync操作，在child cmd做完時，childCnt的值是會影響執行[[NfcSched_SchedChildCmd()]]或[[NfcSched_SchedCmd()]]的因素之一
	- NOW 如果是child cmd，把NfcSched_SchedChildCmd()換成NfcSched_SchedCmd()應該也行吧？ #問題集
	  :LOGBOOK:
	  CLOCK: [2023-07-17 Mon 19:22:52]
	  :END: