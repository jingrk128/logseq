- X3-9060 Datasheet Auto 1.0
- 功能有兩種可能，不知是下面的哪一種(個人認為比較有可能是第一種) #nand問題集
	- 1.讓controller校正data latch的時機
	- 2.讓nand對齊DQ和DQS的rising/falling的訊號
- 當NAND的速度超過800MT/s時，就應該要執行此Training
- 流程：CLE 0x62->ALE lun->ALE inverse set->ALE 1st Pattern->ALE 2nd Pattern->[[tWHRT]]->Dout D0~D15/D31
	- Dout的長度應該是定義在[[Write Training (Tx Side) - 0x20]]的[P3[4]](65325602-8a17-4a05-b82c-ae4242f544b9)
	- pattern是一個byte的大小，其中每個bit表示training時每個byte是inverse set的值或inverse set的反向
		- 0代表inverse set的值，1代表inverse set的反向
		- bit0代表第一個byte，bit7代表最後一個byte
	- inverse set也是一個byte的大小，可以看成當pattern為正向時，每個DQ的值是1或0
		- bit0表示DQ0，bit7表示DQ7
	- 舉例，若inverse set為0x35、1st Pattern為0x5a、2nd Pattern為0x82
		- 因1st Pattern的bit0為0，所以training的第一個byte就是inverse set的值，也就是0x35
		- 1st Pattern的bit1為1，所以training的第二個byte就是inverse set的反相，也就是0xca
		- 所以training的byte0到byte7為：
			- |index|value|
			  |byte0|0x35|
			  |byte1|0xca|
			  |byte2|0x35|
			  |byte3|0xca|
			  |byte4|0xca|
			  |byte5|0x35|
			  |byte6|0xca|
			  |byte7|0x35|
		- byte8到byte15就是參考2nd Pattern，如下：
			- |index|value|
			  |byte08|0x35|
			  |byte09|0xca|
			  |byte10|0x35|
			  |byte11|0x35|
			  |byte12|0x35|
			  |byte13|0x35|
			  |byte14|0x35|
			  |byte15|0xca|