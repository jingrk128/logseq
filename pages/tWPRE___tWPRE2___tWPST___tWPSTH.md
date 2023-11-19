- X3-9070 Package Datasheet ClientPlus Rev1.1
- tWPRE
  id:: 651a94d2-3a89-477a-a253-7f753ecc5c32
	- DQS write preamble
	- 用途：controller告知nand準備傳輸資料
	- X3-9070: 15ns
		- DDR4只有tWPRE2，沒有tWPRE
- tWPRE2
	- DQS write preamble when ODT enabled
	- X3-9070: 25ns
		- DDR3和DDR4都一樣
- tWPST
  id:: 651a905f-72a8-4e7f-a071-8f7e21d69e61
	- DQS write postamble
	- 用途：controller告知nand傳輸停止
	- X3-9070: 6.5ns
		- DDR3和DDR4都一樣
- tWPSTH
  id:: 651a9059-9de9-4953-89a4-3fc21bdfa066
	- DQS write postamble hold time
	- 用途：[[postamble hold的功能]]
		- 從圖來看，是postamble結束後，若接著還要再傳data，需要先等待的一段delay時間，確保信號穩定
	- X3-9070: 25ns
		- DDR3和DDR4都一樣