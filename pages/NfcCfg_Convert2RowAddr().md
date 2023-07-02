- ### 把lun、block、plane、page資訊整合成一個dw
- 是否為xlc?
	- 是 - 用xlc的bitfield
	- 否 - 用slc的bitfield
- retVal = ((page) |
                ((plane) << addrBitField->planeStartBit) | 
                ((block) << addrBitField->blockStartBit) | 
                ((lun) << addrBitField->lunStartBit));
- 回傳retVal