### 知識
- 參考資料：
	- [NAND Flash 101: Flash Device Interfaces - Phison Blog](https://phisonblog.com/nand-flash-101-flash-device-interfaces-2/)
	- YMTC X3 NAND Flash New Features Introduction
- DCC training是指Duty Cycle Correction training，也就是占空比校正訓練。
- 校正的對象是RE_t pin和RE_c pin的duty cycle。
- DCC training是在ONFI 4.1規範中新增的功能，適用於超過800MT/s的速度。
- DCC training需要在每次啟動或重置NAND FLASH後執行一次，並且需要使用特定的指令和數據序列來完成。
- ---
- ### 設定
- 參考資料：
	- X3-9060 Datasheet Auto 1.0
	- YMTC X2-9060 Package Datasheet Appendix VSC Rev 1.2
- 當NAND的速度超過800MT/s時，就應該要支援DCC Training
- DCC Training的時機在[[ZQ Calibration]]完成之後
- 方法一：透過set feature(feature addr 0x20)
	- 1.set feature, feature addr 0x20, P1[0]設1
		- YMTC X2-9060 Package Datasheet Appendix VSC Rev 1.2說P1[2]也要設1
	- 2.CLE 0x00->ALE 6byte addr都填0->CLE 0x05->ALE 2byte addr都填0->CLE 0xE0->Dout
		- Dout的長度為1個page size
		- Dout的data是無效的，controller要在Dout的期間校正RE_t和RE_c的duty cycle
	- 3.read status(0x70 or 0x78)，檢查SR[0]
		- 若SR[0]為fail，就再做一次random data out
			- 應該是指從CLE 0x05開始，或是從CLE 0x00開始
			- 但是YMTC X2-9060 Package Datasheet Appendix VSC Rev 1.2的說法是若失敗的話就從set feature開始重新做
	- 4.完成DCC Training後，要把P1[0]設回0
- 方法二：透過command 0x18
	- 步驟：CLE 0x18->ALE lun->[[tWHRT]]->Dout 1個page->read status
	- 如果SR[0] fail，controller要發佈0xff reset，然後從CLE 0x18開始再做一次DCC training
	- 如果SR[0] fail，則factory DCC設定就會生效