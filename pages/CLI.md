- #2311 #HY_V4R4C010H01B03 #svn26873
- 修改檔案：
	- `\src\cli\cliMgr.c`
		- 在cliCmdTable[]裡新增cli的macro
			- 例：`CLI_NFC_6TH_UID`
	- `\src\cli\CliCore0.c`
		- 新增cli的function
			- 要設定為MINOR_CODE
			- 回傳值固定是int
			- 參數固定有兩個：`(int argc, char** argv)`
	- `\inc\cli\CliCore0.h`
		- 定義macro的內容
			- ```
			  #define CLI_NFC_6TH_UID\
			      {"nfc6thUid", _HELP("read nand 6th uid"), NfcCliRead6thUid}
			  ```
		- 宣告function
			- `int NfcCliRead6thUid(int argc, char** argv);`