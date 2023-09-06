- X3-9070 Package Datasheet ClientPlus Rev1.1
- CLE: 80-15
- 15的作用是將page register的data寫入到cell，以及把cache register的data寫入到page register
- 流程：
	- SLC
		- `0xDA->0x80->column row addr->tADL->Din->0x15->tWB tPCBSY2->`
		  `0xDA->0x80->column row addr->tADL->Din->0x10/0x15->tWB tPROG`
	- TLC
		- TLC的lower page和middle page必須用80-1A，只有upper page才能用80-15
		- single plane
			- `0x80->column row addr->tADL->Din(lower page)->0x1A->tWB tPCBSY2->`
			  `0x80->column row addr->tADL->Din(middle page)->0x1A->tWB tPCBSY2->`
			  `0x80->column row addr->tADL->Din(upper page)->0x15->tWB tPCBSY2->`
		- multi plane
			- `0x80->column row addr->tADL->Din(plane a lower page)->0x11->tWB tPLPBSY->`
			  `0x80->column row addr->tADL->Din(plane b lower page)->0x1A->tWB tPCBSY2->`
			  `0x80->column row addr->tADL->Din(plane a middle page)->0x11->tWB tPLPBSY->`
			  `0x80->column row addr->tADL->Din(plane b middle page)->0x1A->tWB tPCBSY2->`
			  `0x80->column row addr->tADL->Din(plane a upper page)->0x11->tWB tPLPBSY->`
			  `0x80->column row addr->tADL->Din(plane b upper page)->0x15->tWB tPCBSY2->`
- cache program優勢
	- 假設現在要program兩個page，page1和page2
	- 在第一次0x80->DMA page1時，page1的data會放在cache register
	- 發0x15，會把page1的data會搬到page register
	- 第二次0x80->DMA page2，會同時做兩做事
		- 把page2搬到cache register
		- 把page1搬到cell
	- 因為DMA和[[tPROG]]同時進行，所以看DMA和[[tPROG]]哪一個的時間比較短，就節省哪一個的時間
	- 假設 [[tProg]] 的時間比較短，program n個page的話，就可以節省[[tPROG]]*(n-1)的時間
	-