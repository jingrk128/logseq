- 預設為single plane，plane的長度是1
- 填入nop for twb
- 如果是EnIntv
	- NOW EnIntv是什麼意思
	  :LOGBOOK:
	  CLOCK: [2023-05-09 Tue 15:22:41]
	  CLOCK: [2023-05-09 Tue 15:22:42]
	  :END:
	- 填入setup end
	- 如果是multi plane，就修改plane的長度 - #NfcSeqDrv_GetMultiPlaneInfo()
- 重覆執行以下，每重覆一次plane就+1，重覆的次數就是plane的長度
	- 填入read status enhanced
	- 把plane的值寫入row addr - #NfcSeqDrv_SetPlane()
	- 填入row addr - #NfcSeqDrv_SendRowAddr()
	- 填入polling until status
		- read value 0xe0
		- value mask 0x60
		- error mask 0x01