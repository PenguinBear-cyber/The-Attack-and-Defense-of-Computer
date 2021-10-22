# Level 1 - pass

#### [題目]

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211015/PWN_CTF/image/pass_topic.png)

#### [解題]

首先，先下載題目提供的檔案進行查看，下圖為 pass.c 的檔案內容:
![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211015/PWN_CTF/image/pass_C.png)

由圖可見，只要 **token = 0xdeadbeef** 就有機會能找到flag了!

因此可以開始進行逆向分析，使用在 Linux 上的其中一個強大逆向工具 - **Radare2**。
```
Radare2(又稱r2)是一個用於逆向工程和分析二進製文件的完整框架。由一組可以一起使用或獨立於命令行使用的小實用程序組成。
圍繞著一個反彙編為計算機軟件，其生成彙編語言源代碼從機器可執行代碼，它支持多種可執行文件格式用於不同的處理器體系結構和操作系統。
```

在終端機上下達指令執行 radare2 (r2 + 執行檔)。
> r2 pass

進入 radare2 後，下 **aa** 和 **afl** 指令。
* aa 是用來進行分析的指令
* afl 用來列出所有函式 
![image]()
