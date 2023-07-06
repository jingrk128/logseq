- ### NO_CPU_IDX_DBG_PRINTF()
	- 如只單純只想印自己的東西，什麼資訊都不想加，可以用這個function
	- 範例：
		- ```
		  NO_CPU_IDX_DBG_PRINTF(LOG_LVL_INFO, "writePaa = 0x%08x\n", writePaa);
		  ```