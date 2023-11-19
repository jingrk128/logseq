- 參考資料：
	- X3-9060 Datasheet Auto 1.0
	- [Intel Corporation (micron.com)](https://media-www.micron.com/-/media/client/onfi/specs/onfi_4_2-gold.pdf)
- Write Training是指在寫入NAND FLASH時，對寫入時序進行校正和優化的過程。
- Write Training的目的是提高NAND FLASH的寫入速度和可靠性，減少寫入錯誤和數據損壞的風險。
- Write Training需要在每次啟動或重置NAND FLASH後執行一次，並且需要使用特定的指令和數據序列來完成。
- P1[0]: DCCE_EN
	- enabler of explicit [[DCC Training]]
		- datasheet上沒有說明DCCE_EN的完整名字，這是在ONFI 4.2 spec看到的
- P1[2]: DCC Factory Setting
	- 設1時，lun會使用原廠的DCC設定
	- 設0時，lun會使用校正後的DCC設定
- P1[7]: DCC_OUT_DIS
	- 設0時，在DCC期間DQS和DQ會output；設1則不會
		- X3-9060 Datasheet Auto 1.0第241頁的表格沒這個欄位，但第80頁有提到這個功能
- P3[3:0]: Write Training Data Size
  id:: 653255da-c952-421f-adb9-748d4fe25ea3
- P3[4]: User Defined Pattern Length
  id:: 65325602-8a17-4a05-b82c-ae4242f544b9
	-