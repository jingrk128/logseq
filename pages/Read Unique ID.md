- 跟read id的流程差不多，但是R/B pin會動作，所以會用到read status
- 用了read status，之後再做讀取，都只會讀到status的值，讀不到unique id
- 必須在read status之後用cle下0x00，這樣之後的read才會讀到unique id
- 可以從cmd q設定讀到的unique id要存放在哪邊
	- 可以存放在Data-manual暫存器或sgl指定的記憶體位址
	- Data-manual只能存12byte，只讀一次的話沒辦法讀到完整的unique id，至少要讀三次才能把UID complement一起讀回來
		- 從讀第二次開始，cmd q只要下DATA-manual即可，其它都不用，如下：
			- ```
			  00-[0x6d000020]
			  01-[0x000000c6]
			  02-[0x0000000f]
			  ```
		- 存到Data-manual時，data不會像read id一樣兩兩成對