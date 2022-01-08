# collision

[題目]

![image]()

[解題]

根據題目的提示，進行 ssh 的連線並輸入密碼進入題目環境，如圖。

![image]()

接著，輸入 **ls -al** 指令來查看裡面有甚麼文件以及屬性，如圖。

![image]()

根據上圖發現 **col.c 檔案是可讀的、col 檔案是可執行的**，其他檔案目前都沒辦法操作。因此，就先查看 col.c 程式裡面的內容吧，內容如下。

![image]()

看到裡面的 main function 可以發現只要 system("/bin/cat flag"); 這句一執行，就可以得到我們想要的 flag 了。因此，想要執行這一語句，就需要前面兩個 if 語句不能執行，並且第三個 if 語句要保證執行。

必須滿足下列三個條件:
1. argc >= 2 : 要求至少輸入一個參數
2. argv[1] == 20 : 要求第一個參數要 20 個字節
3. check_password 的返回值為 0x21DD09EC : 將一個 20 個字節的字符串拆分為 5 個整型指針並將其所對應的值相加

我們得到 0x21DD09EC = 0x02020202 * 4 + 0x19D501E4

所以寫一個小 python 腳本將值給求出來，並輸入即可。

![image]()

daddy! I just managed to create a hash collision :)

[參考資料]
* https://blog.csdn.net/Y_peak/article/details/112785138
