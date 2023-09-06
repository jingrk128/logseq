- X3-9070 Package Datasheet ClientPlus Rev1.1
- CLE:
	- SLC: 80-10
	- TLC: 80-1A-80-1A-80-10
		- 80-1A可以把一個page的data先放進data register
- 流程：
	- SLC: 0xda->0x80->column addr->row addr->[[tADL]]->Din->0x10->[[tWB]]->[[tProg]]
	- TLC single plane:
		- `0x80->col addr->row addr->tADL-> lower page Din->0x1A->tWB tPCBSY1->`
		  `0x80->col addr->row addr->tADL->middle page Din->0x1A->tWB tPCBSY1->`
		  `0x80->col addr->row addr->tADL-> upper page Din->0x10->tWB tPROG->`
	- TLC multi plane(dummy busy enable):
		- `0x80->column row addr->tADL->Din (lower page plane a)->0x11->tMPP->`
		  `0x80->column row addr->tADL->Din (lower page plane b)->0x1A->tWB tPCBSY1->`
		  `0x80->column row addr->tADL->Din(middle page plane a)->0x11->tMPP->`
		  `0x80->column row addr->tADL->Din(middle page plane b)->0x1A->tWB tPCBSY1->`
		  `0x80->column row addr->tADL->Din (upper page plane a)->0x11->tMPP->`
		  `0x80->column row addr->tADL->Din (upper page plane b)->0x10->tWB tPROG`
	- TLC multi plane(dummy busy disable):
		- 大致跟dummy busy enable一樣，只要把[[tMPP]]換成[[tWB]] [[tPLPBSY]]就好了
		- 9070 multi plane，read在切換plane時不需要等待時間，但program需要等[[tMPP]]
- program cycle有兩個階段
	- 1.serial data loading period
		- 把16K+1984bytes的data載入到page register
	- 2.programming period
		- 把載入的資料推進對應的cell
- 發送0x80時，會清掉page register的內容(應該只有program sequence的第一次0x80才會清)
- 1A 11 10的分別
	- 1A什麼都不做
	- 11是切換plane
	- 10是把整條路上的東西全都推到cell
- [[tlc program的buffer流程]]
- 可以用SR[1]來觀察前一個cache program的page是否已成功寫入cell
- NOW `Cache Program command (15h) could be used as either a last page cache program confirm command or a Page Program Confirm command, same as 10h.` 
  :LOGBOOK:
  CLOCK: [2023-08-10 Thu 18:30:45]
  :END:
  不懂意思，為什麼0x15可以用來確認最後一個program？ #問題集
- NOW `If the Cache Program command (15h) is used to terminate the Cache Program flow, Status bit SR[5] must be polled to find out if the last programming is finished before starting any other operation.` 
  :LOGBOOK:
  CLOCK: [2023-08-10 Thu 18:32:24]
  :END:
  為什麼用80-15要特別確認SR[5]？ #問題集
- `Within the same set of cache program command sequences, the system can switch to block address (LUN addresses). If switching block or LUN is needed, the system should close the current cache program command sets with the Page Program Confirm command (10h) for the last program page within the same block or LUN, and then re-start the next set of cache program command sequences on different block or LUN.`
  :LOGBOOK:
  CLOCK: [2023-08-10 Thu 18:36:23]
  :END:
  這是取自X3-9060 Datasheet Client 1.2 
  為什麼第一段說在同一個cache program可以改變block，下一段卻說要改變的話要先結束當前的cache program？