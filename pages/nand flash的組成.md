- onfi 5.0
- device
	- nand flash chip
	- 一個device由一個或多個target組成
- nand target
  id:: 6541c95c-dc04-42a9-b81d-6be3f5e83cd8
	- nand裡面共享一條ce pin的所有lun，統稱為一個nand target
- host target
  id:: 654c9596-0f55-42f8-a82d-1a4296b1e89e
	- 共用一條controller ce pin的所有nand target，統稱為一個host target
	- 如果沒有用ce reduction的話，host target就等於nand target
- lun
- plane
- block
- page