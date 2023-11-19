- 在2312上看到CBYTE這個型別
  title:: 什麼是CBYTE
- 詢問bing，問答如下：
	- 為什麼在c語言裡，有些人會自己定義CBYTE這個型別？開頭的C是什麼意思？
	- 您好，有些人會自己定義CBYTE這個型別，是為了方便表示一個位元組（byte）的資料，並且指定它的記憶體位置和存取方式。開頭的C是代表code的意思，表示這個資料是儲存在程式碼區域（code segment）的，也就是只能讀取不能修改的區域。這樣做的好處是可以節省記憶體空間，並且提高程式的效率和安全性https://blog.csdn.net/Hongwei_1990/article/details/90183706https://blog.csdn.net/wjy320/article/details/84532034。
	- CBYTE的定義通常是這樣的：
	  \#define CBYTE ((unsigned char volatile code *) 0)
	- 這個定義的意思是，CBYTE是一個指向無符號字元（unsigned char）的指標（pointer），這個字元是不穩定的（volatile），也就是可能會被外部因素改變的，所以不能被編譯器優化。這個指標指向的記憶體位置是0，也就是程式碼區域的起始位置。使用CBYTE時，可以在後面加上一個位址偏移量（offset），來存取不同位置的資料。例如：
	- CBYTE[0x100] //表示存取程式碼區域0x100位置的資料