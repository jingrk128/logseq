- X3-9060 Datasheet Auto 1.0
- 如果erase原廠壞塊，它不會真的執行erase動作
- 流程
	- single plane
		- CLE 0x60->row addr->CLE 0xD0->[[tWB]] [[tBERS]]
	- multi plane
		- YMTC版本
		  CLE 0x60->row addr->CLE 0xD1->[[tWB]] [[tPLEBSY]]->CLE 0x60->row addr->CLE 0xD0->[[tWB]] [[tBERS]]
		- ONFI-JEDEC Joint Taskgroup版本
		  CLE 0x60->row addr->CLE 0x60->row addr->CLE 0xD0->[[tWB]] [[tBERS]]
			- 2311用的是這個版本