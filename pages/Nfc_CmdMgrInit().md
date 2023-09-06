### 初始化cmd記憶體
- 把gNfcCmdMgr內容都清0
- 把gNfcCmdMgr.fastHead和gNfcCmdMgr.slowHead都指到null
- gNfcCmdMgr.freeFastCmdCnt、gNfcCmdMgr.freeSlowCmdCnt、gNfcCmdMgr.totalSlowCmdCnt都設0
- 把所有的gNfcCmdMgr.cmds都依序用非環狀的single linked list串起來
	- 第一個指到null
	- 接下來每個gNfcCmdMgr.cmds都指向前一個
	- gNfcCmdMgr.fastHead指向最後一個gNfcCmdMgr.cmds
- 把gNfcCmdMgr.pendCmdQHead和gNfcCmdMgr.pendCmdQTail都指向null
- gNfcCmdMgr.reapMgr設為null