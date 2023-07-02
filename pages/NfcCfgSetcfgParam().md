- ### 設定nand flash相關參數
- gNfcCfg.magic = 0xcfaa;
      gNfcCfg.validChMap = 0x0f;
      gNfcCfg.validVolMap = 0x1;
      gNfcCfg.volPerCe = 1;
- 把每一個channel的gNfcCfg.ceMapPerCh[]都設為0xff
- gNfcCfg.flashParam.bitsPerCellNum = 3; 
      gNfcCfg.flashParam.wlPerBlockNum = 384;
      gNfcCfg.flashParam.poCntPerBlockNum = 384;
      gNfcCfg.flashParam.pageUserSectorCnt = 32; // 16KB page size
      gNfcCfg.flashParam.pageSpareSize = 2048;//
- gNfcCfg.flashParam.commonCodecPageUserSectorCnt = gNfcCfg.flashParam.pageUserSectorCnt-8;
	- 為什麼這兩個要差相8? #問題集
- gNfcCfg.flashParam.pagePerBlockNum = gNfcCfg.flashParam.wlPerBlockNum*3;
	- 這邊反了吧?應該是wl是page的3倍才對 #問題集
- gNfcCfg.flashParam.planePerLunNum = 2; // 2plane
- gNfcCfg.flashParam.blockPerPlaneNum = 1006;
      gNfcCfg.flashParam.lunPerTargetNum = 1;
- gNfcCfg.flashParam.interfaceEnum = 0; // TODO:
	- 沒用到
- gNfcCfg.flashParam.tR = 750; // 30us, use for both SLC/TLC.
- gNfcCfg.flashParam.tProg = 4625;//185us  (client spec: slc typ prog 190us)
- gNfcCfg.flashParam.tBers = 100000;// 4ms (client spec: slc 4ms, tls 9ms)
- gNfcCfg.flashParam.isSixAddr = TRUE;
      gNfcCfg.xlc.pageStartBit = 0;
      gNfcCfg.xlc.planeStartBit= 11;
      gNfcCfg.xlc.blockStartBit = 12;
      gNfcCfg.xlc.lunStartBit = 23;
      gNfcCfg.slc = gNfcCfg.xlc;
- gNfcCfg.curInterfaceType = 0;
	- 沒用到
- gNfcCfg.scrambleMode = 2;
	- ```
	  For user data:
	      2'b00: scrambling seed index come from configurable command.
	      2'b01: scrambling seed index come from the first byte of meta data.
	      2'b10: scrambling seed come from the first 4-byte of meta data.
	      2'b11: scrambling seed index come from the first byte of meta data, 
	              and final seed is xor between seed the first 4-byte of meta data and 
	              lookup table result.
	  ```
	- ```
	  For meta data: 
	      2'b00: scrambling seed index come from configurable command, and final seed will invert the value fetched from seed table.
	      2'b01,2'b11: scrambling seed index come from the first byte of meta data, and skip scrambling of first byte meta data. The fianl seed will invert the value fetched from seed table. 
	      2'b10: scrambling seed come from register.
	  ```
- gNfcCfg.totalDieCnt = DieCnt;
      gNfcCfg.readRetryTable = rrt; //TODO: temporary
- iodriving = BoardDriving[NFC_CFG_BOARD_TYPE_M2][NFC_CFG_DRIVING_TYPE_IO];
      odtdriving = BoardDriving[NFC_CFG_BOARD_TYPE_M2][NFC_CFG_DRIVING_TYPE_ODT];
      gNfcCfg.ifcfg[NfcCfgIf_Default].clk = NFC_FREQ_33M;
  gNfcCfg.ifcfg[NfcCfgIf_PS0].clk = NFC_FREQ_400M;
  gNfcCfg.ifcfg[NfcCfgIf_PS1].clk = NFC_FREQ_133M;
  gNfcCfg.ifcfg[NfcCfgIf_PS2].clk = NFC_FREQ_50M;
      把全部的gNfcCfg.ifcfg[].IOdriving 都設為 iodriving;
      把全部的gNfcCfg.ifcfg[].ODTdriving 都設為 odtdriving;