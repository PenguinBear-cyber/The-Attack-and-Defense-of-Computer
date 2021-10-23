# Level 1 - gohome

[題目]

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211015/PWN_CTF/image/gohome_topic.png)

[解題]

首先，先下載題目提供的檔案進行查看，下圖為 gohome.c 的檔案內容:

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211015/PWN_CTF/image/gohome_c.png)

由上圖來看，名為 **Billyshouse** 的 function 可能存有目標 flag，但在 main function 中，並無回傳 Billyshouse 此 function，若能取得 Billyshouse function 中的資料，即可能找出所要的 flag。

因此，可以開始將此檔案進行逆向分析，這裡使用 radare2 逆向分析工具進行。
```
Radare2(又稱r2)是一個用於逆向工程和分析二進製文件的完整框架。由一組可以一起使用或獨立於命令行使用的小實用程序組成。
圍繞著一個反彙編為計算機軟件，其生成彙編語言源代碼從機器可執行代碼，它支持多種可執行文件格式用於不同的處理器體系結構和操作系統。
```

在終端機上下達指令執行 radare2 (r2 + 執行檔)。
> r2 gohome

進入 radare2 後，下 **aa** 和 **afl** 指令。
* aa 是用來進行分析的指令
* afl 用來列出所有函式

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211015/PWN_CTF/image/gohome_r2.png)

看完所列出來的函式後，可以進階觀察程式執行的流程圖，下 **s main** 和 **VV** 指令。
* s main:定位到 main 的記憶體位置。這裡的位置是 0x4006d7。
* VV:用來顯示畫面，使用 p 可以切換不同表現方式。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211015/PWN_CTF/image/gohome_VV.png)

下圖為程式執行的流程圖:

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211015/PWN_CTF/image/gohome_process.png)

接下來，開始撰寫 exploit code。

> gedit exploit.py

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211015/PWN_CTF/image/gohome_exploit.png)

> python3 exploit.py

![image]()

