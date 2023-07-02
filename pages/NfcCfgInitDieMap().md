- ### 根據以下參數畫出die map
- ### nfcCfg->flashParam.lunPerTargetNum
- ### nfcCfg->volPerCe
- ### NFC_MAX_CE_NUM_PER_CH
- ### NFC_MAX_CH_NUM
- ### nfcCfg->validChMap
- ### nfcCfg->ceMapPerCh
- ### nfcCfg->validVolMap
- ---
- 把gDieMap清為0
- 設die=0
- lun設0，nfcCfg->flashParam.lunPerTargetNum是多少，以下就重覆幾次，每重覆1次lun就+1
	- vol設0，nfcCfg->volPerCe是多少，以下就重覆幾次，每重覆1次vol就+1
		- ce設0，NFC_MAX_CE_NUM_PER_CH是多少，以下就重覆幾次，每重覆1次ce就+1
			- ch設0，NFC_MAX_CH_NUM是多少，以下就重覆幾次，每重覆1次ch就+1
				- 以下條件都符合嗎？
					- nfcCfg->validChMap的第ch個bit為1
					- nfcCfg->ceMapPerCh[ch]的第ce個bit為1
					- nfcCfg->validVolMap的第vol個bit為1
						- 若都符合：
							- gDieMap[die].ch = ch;
							  gDieMap[die].ce = ce;
							  gDieMap[die].vol = vol;
							  gDieMap[die].volCnt = nfcCfg->volPerCe;
							  gDieMap[die].lun = lun ;
							  die++;
				-