- 第二講
	- ANSI C標準只有定義char是8 bit
		- 沒有明確定義short、int、long
			- 只有要求long>=int>=short
		- ANSI C標準是美國國家標準化協會制定的，1989年確定最終版。國際標準化組織(ISO)接受該標準為國際標準。
	- printf不是c語言的關鍵字
	- c語言是高階語言中效能最高的
		- c語言的效率比組合語言低30-40%
-
- 第三講
	- 26:27的練習題，在書上的章節3.3
	- using variable a to define
	- `a.an integer`
	  `b.a pointer to an integer`
	  `c.a pointer to a pointer to an integer`
	  `d.an array of 10 integers`
	  `e.an array of 10 pointers to integers`
	  `f.a pointer to a function that takes an integer as an argument and returns an integer`
	  `g.an array of ten pointers to functions that take an integer argument and return an integer`
	- 解答如下
	  `a.int a;`
	  `b.int *a;`
	  `c.int **a;`
	  `d.int a[10];`
	  `e.int *a[10];`
	  `f.int (*a)(int b);`
	  `g.int (*a[10])(int b);`
-
- 第四講
	- big/little endian
		- 0x12345678
		- big endian會把12放在低位址
		- little endian會把78放在低位址
	- function宣告
		- 宣告function時，沒有指定function的回傳類型，在ansi c中是合法的，例：
			- func();
			- 這樣在離開function時，會回傳一個隨機的整數
				- 如果是arm，就會回傳R0暫存器的值
				- 一般寫main()的時候前面不加回傳類型也能編譯過，應該就是因為這個特性
		- 宣告function時，不需傳入參數時，可以括號內加上void，例：
			- int func(void);
			- 如果有加void，實際呼叫時若加了參數，compiler會報error
			- 如果沒加void，實際呼叫時若加了參數，compiler不會報error，只是這些參數都會被忽略