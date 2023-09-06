- #2311 #trunk #svn25943 #DL_Flashdisk_ASIC_YMTC_X1_9050_CLIENT
- ```
  static void Program(uint32_t die, uint32_t plane, uint32_t block, uint32_t wl, uint32_t isSlc)
  {
      MemUtil_MemSet32(writeCtx->pBuf, 0, BUFFER_WRITE);
      uint32_t *pBuf;
      NFC_CMD_T *cmd = NFC_Cmd_Aquire();
      
      writeCtx->cmd = cmd;   
      pBuf = writeCtx->pBuf;
      uint32_t dataGened = CAST_UINT32(0x0AA << 24) + CAST_UINT32((wl+block) << 16) + die;
      for (uint32_t iterator=0; iterator < (BUFFER_WRITE/4) ; iterator++)
      {
  //        if(iterator > 0x1fff) break;
          pBuf[iterator] = (dataGened++);
      }
      
      cmd->request.wrReq.block = block;
      cmd->request.wrReq.page = wl;
      cmd->request.wrReq.die = die;
      cmd->request.wrReq.plane = plane;
      cmd->request.wrReq.ioSliceCnt = 0;
      cmd->ops.raw = 0;
      
      cmd->ops.nfcOpOption.isSlcMode = isSlc;
      cmd->request.wrReq.progRowCnt = isSlc ? 1 : TLC_ROW_COUNT;
      
      for(uint16_t i=0; i < TLC_ROW_COUNT; i++)
      {
          writeCtx->metaAddr[i] = NfcMeta_GetMeta(gNrMetaNvml, 0);
          for (uint32_t iterator = 0; iterator < gNrMetaNvml; iterator++)
          {
              writeCtx->metaAddr[i][iterator] = (wl&1)? 
                                    ((0x055 << 24) + (die << 20) + ((wl+block) << 8) + (iterator+die))
                                    : ~((0x055 << 24) + (die << 20) + ((wl+block) << 8) + (iterator+die));
          }
      }
      cmd->dataBuf.metaAddr = writeCtx->metaAddr;
      cmd->dataBuf.dataAddr = writeCtx->pBuf;
      
      cmd->opType = OP_TYPE_PROG;
      cmd->callback = IssueWriteCallback;
      cmd->context = CAST_PTR(void, writeCtx);
      
  #if 0
      NFCInf_Req_Submit(cmd);
  #else
      uint32_t result = NFCInf_Sync_Req(cmd);
      if (NF_RET_OP_OK != result)
      {
          DBG_PRINTF(LOG_LVL_WARN, "program die%d block%d fail, ret=%x\n", die, block, result);
      }
      else
      {
          DBG_PRINTF(LOG_LVL_WARN, "program die%d block%d OK, ret=%x\n", die, block, result);
      }
  #endif
  }
  ```
- cmd_queue的內容可以參考[[cmd_q]]裡面program的部份