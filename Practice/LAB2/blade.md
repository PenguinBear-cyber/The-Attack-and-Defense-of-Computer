# blade

[解題開始]

首先，先執行看看 blade 這個檔案，如圖。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/blade_run.jpg)

由圖可知，其有個問題給我們回答 **why did you save me?**，隨意輸入回答後即結束程式。

因此，可以開始對檔案進行逆向分析，這裡使用到 Ghidra 來進行分析，開啟 Ghidra 後，創建 project 並將檔案匯入進去，匯入後會先詢問是否要進行分析，按下 yes 後即會呈現下圖。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/blade_show.jpg)

接下來我們可以開始使用 ghidra 工具進行程式的觀察及分析，這裡發現在 symbol tree 底下的 main function 有玄機，如圖。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/blade_main.jpg)

我們看到在最右邊的反編譯畫面中，可以知道程式的運作情形，

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/blade_maincode.jpg)

呈上圖，底色綠色的地方說明若 **if (iVar1 == 0)** 則會進入 **print_flag()** 中，這裡非常有可能是我們想要找的地方。另外，更直接且明確的提示為上述的上一行程式， **iVar1 = strcmp(local_64,"to_say_something");** ，這裡有一個對 **strcmp** 函數的調用。
```
strcmp() 是一個C語言的函式庫。
其用法為 int strcmp(const char *str1, const char *str2)
函數 strcmp 將兩個字符串作為參數，並根據字符串的比較返回一個整數值。

返回值為:
1. 如果 str1 小於 str2，則返回值 < 0
2. 如果 str2 小於 str1，則返回值 > 0
3. 如果 str1 等於 str2，則返回值 = 0
```
透過上面的說明，我們可以很明確的知道只要將 strcmp() 中的兩個參數值相等，就可以得到 strcmp 回傳值 = 0，也就表示 iVar1 = 0，如此一來就滿足 if 裡面的條件，也就可以跳到 print_flag() 中，亦可以印出我們要的 flag 了。

看到上圖程式圖，我們可知輸入值為 **to_say_something** 就可以滿足所有要求，因此再次執行檔案並輸入正確關鍵字，如圖。

![image]()


