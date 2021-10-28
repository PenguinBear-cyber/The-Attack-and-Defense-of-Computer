# LuckyGuess

[解題開始]

首先，先執行看看 LuckyGuess 這個檔案，如圖。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/LuckyGuess_run.jpg)

由圖可知，此程式要我們猜數字，目前我們所能做的事就是盲猜，最後會顯示 **no dice.** 而結束程式。

因此，我們可以開始分析程式，這裡使用到一個逆向工程的工具 - **Ghidra**，創建一個新的 project 再將 LuckyGuess 檔案匯入，匯入完成後會出現下圖提示，詢問是否要分析檔案。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/LuckyGuess_analyze.jpg)

按下 yes 後，會呈現下圖。

![image]()

接下來我們可以開始使用 ghidra 工具進行程式的觀察及分析，這裡發現在 symbol tree 底下的 main function 有玄機，如圖。

![image]()

我們看到在最右邊的反編譯畫面中，可以知道程式的運作情形，

![image]()

呈上圖，底色綠色的地方說明若 **if ( local_a8 == local_9c ) 則 **break** 表示會跳出 while 迴圈，進入 for 迴圈得到 flag，亦表示執行成功。
