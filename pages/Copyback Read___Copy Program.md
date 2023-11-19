- X1-9050 Package Datasheet Client rev4.0
- 可以把同一個plane裡面的某個page的data複製到另一個page
	- 中間只會用到page register當暫存空間，不會用到controller的buffer
	- 有需要的話，複製到另一個page之間可以
		- 用dma(05-E0)把data讀到controller
		- controller用dma修改裡面的data
- copyback read是00-35
- copyback program是85-10
- SLC to SLC
	- DA->00->addr1->35->[[tWB]] [[tR]] [[tRR]](->dma Dout)->85->addr2->[[tADL]](->dma Din)->10->[[tWB]] [[tPROG]]->DF
- SLC to TLC
	- DA->00->addr1->35->[[tWB]] [[tR]] [[tRR]](->dma Dout)->DF->
	  85->addr LP->[[tADL]](->dma Din)->1A->[[tWB]] [[tPCBSY]]->
	  DA->00->addr2->35->[[tWB]] [[tR]] [[tRR]](->dma Dout)->DF->
	  85->addr MP->[[tADL]](->dma Din)->1A->[[tWB]] [[tPCBSY]]->
	  DA->00->addr3->35->[[tWB]] [[tR]] [[tRR]](->dma Dout)->DF->
	  85->addr UP->[[tADL]](->dma Din)->10->[[tWB]] [[tPCBSY]]
- 另外也支援TLC to TLC和multi plane，詳見datasheet
- 用2311實作X1-9050的copyback
	- 從另一個page copyback read再copyback program到另一個page，功能正常
	- 把copyback read的0x35換成0x30，功能也正常
		- 一般read page把0x30換成0x35功能也還是正常
			- 0x30和0x35這兩個cmd有什麼差別？ #nand問題集
				- 一般的read page是00-30，換成00-35也能正常work，slc和tlc都一樣
				-
	- 把copyback program的0x85換成0x80的話，照datasheet的講法0x80會讓把page register全清為0xff，但我read回來觀察，上面的data並非都0xff，而是亂數(亂數的開頭如下：)
		- ```
		  7f 7a e5 ff fb 38 3b c2 b5 7f b4 23 bd de 09 37 
		  84 ac 96 c7 2e 92 cb a6 92 db 49 e9 bd 15 55 8a 
		  21 67 24 83 32 c5 0f 2b 47 60 9b cd be f9 a8 3f 
		  4a 95 3f 5b a7 7f 80 b4 86 b8 65 4b cc 3c 26 e8 
		  f5 b4 97 65 21 52 fe fc 6d 04 77 15 41 ed da 01 
		  97 44 bc 69 bc 37 6a f5 90 df 65 21 bd 05 45 f5 
		  85 c5 65 fa be fc fd 04 f7 ca 85 d0 5f 45 e1 c2 
		  7e 03 49 79 f2 51 48 90 a1 d3 a3 47 2f cf 0f 60 
		  64 26 87 41 b6 49 f8 2f 6a f5 20 9f 9a 85 c0 00 
		  01 fc f8 41 cc 88 81 6c e8 41 ec 17 ba d5 50 ff 
		  a5 de b9 c7 c4 8c f6 f8 d5 d4 88 5e 67 11 06 89
		  ```