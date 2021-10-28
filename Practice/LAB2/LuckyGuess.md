# LuckyGuess

[解題開始]

首先，先執行看看 LuckyGuess 這個檔案，如圖。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/LuckyGuess_run.jpg)

由圖可知，此程式要我們猜數字，目前我們所能做的事就是盲猜，最後會顯示 **no dice.** 而結束程式。

因此，我們可以開始分析程式，這裡使用到一個逆向工程的工具 - **Ghidra**，創建一個新的 project 再將 LuckyGuess 檔案匯入，匯入完成後會出現下圖提示，詢問是否要分析檔案。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/LuckyGuess_analyze.jpg)

按下 yes 後，會呈現下圖。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/LuckyGuess_show.jpg)

接下來我們可以開始使用 ghidra 工具進行程式的觀察及分析，這裡發現在 symbol tree 底下的 main function 有玄機，如圖。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/LuckyGuess_main.jpg)

我們看到在最右邊的反編譯畫面中，可以知道程式的運作情形，

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/LuckyGuess_maincode.jpg)

呈上圖，底色綠色的地方說明若 **if ( local_a8 == local_9c )** 則 **break**，此表示若滿足 if 條件就會跳出 while 迴圈，進入 for 迴圈得到 flag，亦表示執行成功。

為滿足上述條件，下一階段我們接著使用一個逆向工程的動態分析工具 - **gdb-peda**，可以追蹤每⼀行組語，也可在中途改變流程。

所以接著就進入 gdb 工具開起 LuckyGuess 檔案，如圖。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/LuckyGuess_gdb.jpg)

 下一步反組譯 main function，觀察裡面的執行過程，如圖。
 
 ![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/LuckyGuess_disas.jpg)
 
 從中我們找到 main 中有 jnz 指令，如下圖，因此最簡單的方式就是將 jnz 指令修改為 nop 指令，表示不做任何有效操作的命令。
 
 ![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/LuckyGuess_jnz.jpg)

先在此記憶體位置設置斷點。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/LuckyGuess_breakpoint.jpg)

則可以下 **r** 指令執行檔案，執行到上圖斷點位置就會停止，下圖為程式執行過程呈現。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/LuckyGuess_r1.jpg)

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/LuckyGuess_r2.jpg)

最後，重新設置 **0x400b93** 位置，然後繼續執行，即可以看到我們尋找的 flag。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/LuckyGuess_final.jpg)

這裡可以看到 flag 了!!

easyctf{aaA_tOucH_0f_luccK_47ca4e}

[END]
