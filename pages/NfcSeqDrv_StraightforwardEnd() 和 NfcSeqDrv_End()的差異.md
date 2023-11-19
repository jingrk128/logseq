- 兩個都是為cmd作結尾
  差別在於：
	- NfcSeqDrv_StraightforwardEnd()必定會填eos和丟進cmd q memory
	- NfcSeqDrv_End()可以透過參數選擇要不要做
		- 而什麼時候會不做？目前看到都是read的時候，推測因為read的cmd較長，容易超過32個dw，所以需要接力