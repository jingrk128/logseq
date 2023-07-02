- #2311 #trunk #svn25943 #git #IAR #code_size
	- ### 解決code size容易爆的問題
		- 如果沒特別指定，東西都會放到ITCM裡面
		- ITCM似乎快爆了，因為只要多印一些東西，build code就過不了，且compiler會提示：
			- ```
			  Error[Lp011]: section placement failed 
			              unable to allocate space for sections/blocks with a total estimated minimum size of 0x67f4 bytes (max align 0x4) in <[0x0-0x1'ffff]> (total uncommitted space 0x641c). 
			  Error while running Linker 
			  ```
		- 解決方法：
			- 由於ITCM爆了，所以要將多印的東西放在ITCM以外的地方
			- 我多印的東西在所以NfcBaseCmd.c，build出來的檔案會是NfcBaseCmd.o
			- 解法1，放在OCB
				- FullmaskFlashdiskCore0.icf裡的267行是
					- `"OCB" : place at address 0x40230000 {section icode, section mcode, section .text object FullDiskRebuild.o};`
				- 在它的後面加上section .text object FullDiskRebuild.o，改完後如下：
					- `"OCB" : place at address 0x40230000 {section icode, section mcode, section .text object FullDiskRebuild.o, section .text object NfcBaseCmd.o};`
				- 這樣子就會把NfcBaseCmd.c印出的東西，放在OCB的記憶體區段
				  所以build code就沒問題了
			- 解法2，放在AON_OCB0
				- 在FullmaskFlashdiskCore0.icf裡的284行下面插入一行，內容為：
				  `"AON_OCB0" : place in AON_OCB0_region {section .text object NfcBaseCmd.o};`
				- 這樣子就會把NfcBaseCmd.c印出的東西，放在AON_OCB0的記憶體區段
				  所以build code就沒問題了
			- 不管用哪一種解法，都要先確認我們放的記憶體位址，有沒有被其它地方使用，而可能導致某些功能不正常
				- 如果放在OCB，就要檢查fw裡有沒有人用到FREE_OCB0_START，實際上是沒有，所以可以放在
				- 如果放在AON_OCB0，就要檢查FREE_AON_OCB0_START，會發現Reap有用到AON_OCB0
					- Reap是管理run time記憶體配置，所以要小心會不會造成run time時的某些錯誤
	- ### run time的記憶體配置
		- 由Reap管理
		- 從FlashDiskCore0Main.c 709-712行得知
		  Reap配置的起點是FREE_AON_OCB0_START，終點是FREE_AON_OCB0_END
		- FREE_AON_OCB0_START和FREE_AON_OCB0_END的位址都是由FullmaskFlashdiskCore0.icf指定
			- FREE_AON_OCB0_START是等其它人都拿完後的下一個位址
				- `"AON_OCB0" : place in AON_OCB0_region {block FREE_AON_OCB0_START};`
			- FREE_AON_OCB0_END是AON_OCB0的最後一個位址
				- `"AON_OCB0" : place at end of AON_OCB0_region {block FREE_AON_OCB0_END};`
			- 可以在FlashdiskASICCore0.map觀察這兩個參數的實際位址，來算出它們能用的空間
- ### 使用NFC_ONLY_USE_DIRECT_CMD，code size會小很多
	- 目前都是習慣有define NFC_ONLY_USE_DIRECT_CMD
	- 今天把NFC_ONLY_USE_DIRECT_CMD關掉後，build code就不過了
	- 某個空間只有0x2000 byte，一關掉NFC_ONLY_USE_DIRECT_CMD，就會爆code
	- build log顯示該空間用到了0x202a8byte，超過了600byte