- read retry table的前8個byte是header
  id:: 646872ed-c2e6-4397-b34a-a1196ed35154
  定義如下：
	- ```
	  typedef struct
	  {
	      uint8_t xlcRegCnt: 4;
	      uint8_t dataCntForOneReg: 4;
	      uint8_t xlcTotalRRCnt;
	      uint8_t slcTotalRRCnt; //if slcTotalRRCnt = 0, the Nand has not slc mode, the retry data for slc will not exit.
	      uint8_t xlcStartReg;
	      uint8_t slcStartReg;
	      uint8_t isNeedTrmt : 1; // maybe need set mode to default
	      uint8_t cmdMode : 1;// 0:  hynix mode, 1: toshiba/micron
	      uint8_t isPrexCmd :   1;  // is need pre cmd?
	      uint8_t isSuffixCmd: 1; //is need suffix  cmd?
	      uint8_t isClassify: 1; //Grouping by efficacy?
	      uint8_t groupCnt: 3; // if isClassify = true, groupCnt Make sense. 0 : two group, 1: three group.. 7: nine
	      uint8_t preCmd; // set feature cle cmd //if cmdPosition is 0, preCmd and suffixCmd  such as hynix. 
	      uint8_t suffixCmd;
	  }READ_RETRY_TAB_HEADER;
	  ```