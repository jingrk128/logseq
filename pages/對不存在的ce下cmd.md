- #2311 #ROM #r24767 #issus #對不存在的ce下cmd
	- 2311 rom 對不存在的ce下reset+read status
	  cmd q的內容和flaskdisk一樣
	  會正常得到timeout
	  即使不跑Nfc_PinInit()也會正常得到timeout
	- 嘗試把edo mode換成和flashdisk一樣的toggle mode
	  不論有沒有跑Nfc_PinInit()
	  都會正常得到timout