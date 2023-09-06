- #2311 #trunk #svn25943 #DL_Flashdisk_ASIC_YMTC_X1_9050_CLIENT
- ```
  static void Read(uint32_t die, uint32_t plane, uint32_t block, uint32_t page, uint32_t isSlc, uint32_t ishighPriority)
  {
      uint32_t result;
      NFC_CMD_T *cmd = NFC_Cmd_Aquire();
      
      readCtx->cmd = cmd;
      readCtx->metaAddr[0] = NfcMeta_GetMeta(gNrMetaNvml, 0);
      cmd->dataBuf.metaAddr = readCtx->metaAddr;
      cmd->dataBuf.auStatusList = &(readCtx->auStatusArray[0]);
  
      cmd->ops.raw = 0;
      cmd->ops.nfcOpOption.isSlcMode = isSlc;
      cmd->ops.nfcOpOption.highPriority = ishighPriority;
      
      cmd->request.rdReq.die = die;
      cmd->request.rdReq.block = block;
      cmd->request.rdReq.page = page;
      cmd->request.rdReq.pageOffset = 0;
      cmd->request.rdReq.reqLen = NFC_CONFIG_GET_PLANE_CNT() * NFC_CONFIG_GET_USER_SECTOR_CNT();
  #ifdef NFC_TEST_DUMP_RAW_DATA
      cmd->request.rdReq.reqLen = 8;
  #else
      if(plane == 0xf)
          cmd->request.rdReq.reqLen = NFC_CONFIG_GET_PLANE_CNT() * NFC_CONFIG_GET_USER_SECTOR_CNT();
      else
          cmd->request.rdReq.reqLen = NFC_CONFIG_GET_USER_SECTOR_CNT();
  //    if(!isSlc)
  //        cmd->request.rdReq.reqLen *= 3;
  //    cmd->dataBuf.dataAddr = (uint32_t *)((uint32_t)readCtx->pBuf);
  #endif //NFC_TEST_DUMP_RAW_DATA
      DBG_ASSERT( (BUFFER_READ/512) >= (NFC_CONFIG_GET_PLANE_CNT() * NFC_CONFIG_GET_USER_SECTOR_CNT()));
      readCtx->seqNo = lastSendSeq[die]++;
  
      cmd->opType = OP_TYPE_READ;
      cmd->callback = IssueReadCallback;
      cmd->context = CAST_PTR(void, readCtx);
      
      uint32_t buf_len = (plane==0xf)? 0x8000:0x4000;
      uint32_t temp = cmd->request.rdReq.page;
      uint32_t loop = isSlc ? 1 : 3;
      for(uint32_t i=0; i<loop; i++)
      {
          cmd->dataBuf.dataAddr = (uint32_t *)((uint32_t)readCtx->pBuf + (buf_len*i));
          MemUtil_MemSet32(cmd->dataBuf.dataAddr, 0, buf_len);
          cmd->request.rdReq.page += i;
  #if 0
          NFCInf_Req_Submit(cmd);
  #else
          result = NFCInf_Sync_Req(cmd);
  #endif
          cmd->request.rdReq.page = temp;
      }
      
      IssueReadCallback(result, CAST_PTR(void, readCtx), 0);
  }
  ```
- cmd queue的內容可以參考[[cmd_q]]裡面read的部份
- tlc read時跟tlc program的做法不同(single plane)
	- program是下一次cmd即可，reqLen為slc的三倍
	- read是要下三次cmd，且每一次的reqLen和slc一樣
		- 如果用成下一次cmd且reqLen為slc的三倍的話，function內部會判斷錯誤，single plane會判斷為multi plane
			- 弄成上述的話，cmd queue會長這樣：
			- ```
			  00-[0x66000097]
			  01-[0x00000012]
			  02-[0x00000064]
			  03-[0x00001710]
			  04-[0x00003212]
			  05-[0x00003218]
			  06-[0x00007812]
			  07-[0x17100044]
			  08-[0x00000000]
			  09-[0x0060e037]
			  10-[0x66000097]
			  11-[0x00000012]
			  12-[0x00000064]
			  13-[0x00001718]
			  14-[0x00003212]
			  15-[0x00003218]
			  16-[0x00007812]
			  17-[0x17180044]
			  18-[0x00000000]
			  19-[0x0060e037]
			  20-[0x660000a7]
			  21-[0x00000012]
			  22-[0x00000064]
			  23-[0x00001710]
			  24-[0x00003012]
			  25-[0x00003218]
			  26-[0x0000000d]
			  27-[0x00007812]
			  28-[0x17100044]
			  29-[0x00000000]
			  30-[0x0060e037]
			  31-[0x66000087]
			  32-[0x00000612]
			  33-[0x00000064]
			  34-[0x00001710]
			  35-[0x0000e012]
			  36-[0x0000c818]
			  37-[0xf707ffb5]
			  38-[0x420000df]
			  39-[0x00000000]
			  40-[0x66000087]
			  41-[0x00000612]
			  42-[0x00000064]
			  43-[0x00001718]
			  44-[0x0000e012]
			  45-[0x0000c818]
			  46-[0xf747ffb5]
			  47-[0x400000df]
			  48-[0x00000000]
			  49-[0x66000097]
			  50-[0x00000612]
			  51-[0x00000064]
			  52-[0x00001710]
			  53-[0x0000e012]
			  54-[0x0000c818]
			  55-[0xf787ffb5]
			  56-[0x410000df]
			  57-[0x00000000]
			  58-[0x0000000f]
			  ```