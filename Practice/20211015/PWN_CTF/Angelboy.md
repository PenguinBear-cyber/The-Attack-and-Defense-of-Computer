# Level 1 - Angelboy_Pwn-2

[題目]

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211015/PWN_CTF/image/Angelboy_topic.png)

[解題]

首先，題目中有提供一個檔案 **ret2sc** ，可以先打下列指令來執行看看此檔案輸出的內容。
> ./ret2sc
```
若產生 Permission denied 的錯誤訊息，即表示未獲得許可/取用的權限。因此需要改變檔案型態，將檔案變更為可執行檔，輸入下列指令即表示給執行權限。
> chmod +x ret2sc
跑完此指令回到上一步即可完成執行檔案。
```
![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211015/PWN_CTF/image/Angelboy_run.png)

由上圖可看見執行此檔案後的結果，需要我們輸入兩個變數。

再下下列指令來檢查檔案格式。
> file ret2sc

Output:
```
ret2sc: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=b83f13f0a84fdae7a5bc54d828d0e4ea15575d6d, not stripped
```
由結果可見，此檔案為 ELF 64位元格式。

接下來，可以開始對檔案進行逆向分析，首先使用逆向工程的靜態分析工具 - **radare2**。
```
Radare2(又稱r2)是一個用於逆向工程和分析二進製文件的完整框架。由一組可以一起使用或獨立於命令行使用的小實用程序組成。
圍繞著一個反彙編為計算機軟件，其生成彙編語言源代碼從機器可執行代碼，它支持多種可執行文件格式用於不同的處理器體系結構和操作系統。
```

在終端機上下達指令執行 radare2 (r2 + 執行檔)。
> r2 ret2sc

進入 radare2 後，下 **aa** 和 **afl** 指令。
* aa 是用來進行分析的指令
* afl 用來列出所有函式

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211015/PWN_CTF/image/Angelboy_r2.png)

看完所列出來的函式後，可以進階觀察程式執行的流程圖，下 **s main** 和 **VV** 指令。
* s main:定位到 main 的記憶體位置。這裡的位置是 0x400636。
* VV:用來顯示畫面，使用 p 可以切換不同表現方式。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211015/PWN_CTF/image/Angelboy_VV.png)

下圖為程式執行的流程圖:

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211015/PWN_CTF/image/Angelboy_process.png)

觀察完畢程式執行的靜態流程圖後，可以使用動態分析 **gdb-peda** 來進行分析，使用 gdb 啟動程式(gdb + 執行檔)。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211015/PWN_CTF/image/Angelboy_gdb.png)

接著使用下面指令來找到 main 記憶體位址的斷點。
> b main

![image]()

