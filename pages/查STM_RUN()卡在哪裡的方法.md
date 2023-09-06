- ```
  uint32_t STM_Roll(STM_CTX_T *stmCtx, DLIST_T *queue)
  {
      STM_RET_T ret;
  
      do
      {
          ret = STM_RUN(stmCtx);
          STM_SHOW_TIMEOUT_MSG(stmCtx);
      } while (STM_RET_CONT == ret);
  ```
- [下午 04:23] Jing Xian
- 有什麼情況下  會跑不出這個do while嗎
- [下午 04:24] John Lin
- 一直STM_RET_CONT ?
- [下午 04:24] Jing Xian
- 嗯嗯!
- [下午 04:24] John Lin
- 不過我猜應該是因為一直STM_RET_WAIT
- [下午 04:24] John Lin
- 然後又被重新排到queue裡面
- [下午 04:25] Jing Xian
- STM_RET_WAIT會是因為下cmd一直等不到結果嗎
- [下午 04:25] John Lin
- 有可能
- [下午 04:25] John Lin
- polling等不到結果就又再wait下一次又polling
- [下午 04:26] John Lin
- 一直wait
- [下午 04:27] John Lin
- 要把ENABLE_STM_TIME_CHECK給define成1
- [下午 04:27] John Lin
- 看看就知道卡哪state
- [下午 04:33] John Lin
- 有印啥嗎
- [下午 04:34] Jing Xian
- 0-16246-0-26894-0-0-0-0-c7: fTbl 0x0000f334, st 1, 0
- [下午 04:35] John Lin
- 用iar然後打開debug infomation
- [下午 04:35] John Lin
- build core 0
- 會找到HY_V4R4C010H01B03DFT\build\Flashdisk\core0\DL_Flashdisk_ASIC_YMTC_X1_9050_CLIENT_RAID\List\FlashdiskASICCore0.map
- [下午 04:36] John Lin
- 然後查f334
- [下午 04:36] John Lin
- 是哪個function table
- [下午 04:37] Jing Xian
- debug infomation要在哪裡開
- [下午 04:38] John Lin
- options->linker->output->勾選"Include debug information in output"
- [下午 04:38] John Lin
- 之後要再關掉 不然腳本build不過