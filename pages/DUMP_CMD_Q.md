- ```
  #define	DUMP_CMD_Q
  #ifdef	DUMP_CMD_Q
      DBG_PRINTF(LOG_LVL_WARN, "Command queue:\n");
      for(uint32_t i=0; i<seqDwCnt; i++)
          DBG_PRINTF(LOG_LVL_WARN, "%02d-[0x%08x]\n", i, orgStart[i]);
  	
      DBG_PRINTF(LOG_LVL_WARN, "\n");
  #endif
  ```
-