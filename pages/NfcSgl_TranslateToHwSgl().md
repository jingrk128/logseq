- NOW 如果是read操作
  :LOGBOOK:
  CLOCK: [2023-05-09 Tue 17:59:56]
  :END:
	- **if(cmd->ops.nfcOpOption.onlyMeta)**
		- **return(NfcSgl_NoData(totalSector));**
	- **preDummySec = (cmd->request.rdReq.pageOffset) & 0x7;**
	  **postDummySec = (cmd->request.rdReq.pageOffset + cmd->request.rdReq.reqLen) & 0x7;**
- 如果是program操作
	- **preDummySec = 0;**
	  **postDummySec = 0;**
- preDummySec和postDummySec加起來是0嗎？
	- 是
		- data addr的bit0是1嗎？
			- 是 - 執行[[NfcSgl_SglTranslateLite()]]
			  :LOGBOOK:
			  CLOCK: [2023-05-10 Wed 09:53:56]
			  :END:
			- 不是 - 執行[[NfcSgl_BufferTranslateLite()]]
				- 取得sgl addr - #NfcSgl_AllocateIndex()
				- 取得sgl index - #convertAddr2Index()
				- 填入sgl資訊
					- buf addr
					- data size(以sector為單位、0 base)
					- nex sgl index
	- 不是
		- data addr的bit0是1嗎？
			- 是 - 執行#NfcSgl_SglTranslate() #待辦
			  :LOGBOOK:
			  CLOCK: [2023-05-10 Wed 09:53:58]
			  :END:
			- NOW 不是 - 執行**#NfcSgl_BufferTranslate()**
			  :LOGBOOK:
			  CLOCK: [2023-05-10 Wed 09:53:59]
			  :END: