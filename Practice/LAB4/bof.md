# bof

[題目]pwnable.kr (https://pwnable.kr/play.php)

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/bof_topic.jpg)

[執行程式]

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/bof_run.jpg)

執行程式後，會出現 overflow me:，然後要我們輸入內容，輸入完成後即結束程式。

[解題]

首先，可以用 ghidra 來進行靜態分析，查看程式執行流程及其程式碼。這裡找到兩個核心的程式碼:main()、func()，如圖。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/bof_func.jpg)

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/bof_main.jpg)

看完原始碼後，很容易就發現，這道題目是在比對函式呼叫傳入的值和 -0x35014542 的大小!!! 如果相等，flag 就能找出來了。但是，看到 main function 發現這個傳入的值卻已經被程式寫死了(=0xdeadbeef)。

回到 func() 再繼續往下看，會發現有一個函式 gets()，這個函式是很明顯的一個漏洞函數，因為它讀入資料的時候，不會檢查緩衝區的界限，很容易造成緩衝區溢位的漏洞，所以一般會用 fgets 函式來代替它。

因此，可以透過從 gets 函式中輸入足夠多的資料來使緩衝區溢位，用我們輸入的 -0x35014542 來覆蓋之前壓進棧的數，就可以 get 到 flag 啦! 所以問題是:需要輸入多少的資料才能覆蓋到之前壓入棧中的數呢？

接下來，可以用 gdb 來除錯一下。開啟 gdb 後，來檢視一下被呼叫函式 func() 的反彙編程式碼。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/bof_disassemble.jpg)

這裡可以發現這樣一段反彙編程式碼 **0x56555654 <+40>: cmp DWORD PTR [ebp+0x8],0xcafebabe**，可以知道我們要的比較語句地址在 **0x56555654**，於是先在這位置打一個斷點(b *0x56555654)。然後執行(run)程式。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/bof_br.jpg)

這裡我們用輸入垃圾值來試探出偏移量。我們知道 A 將在記憶體中顯示為 \ x41，而且緩衝區長度為 32。因此不如就輸入 33 個 A 來讓其溢位，然後通過檢查記憶體，來檢視偏移量。輸入完成後會呈現下圖:

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/bof_b.jpg)

程式會停在我們設定的斷點處，此時接著輸入 **x /40xw $esp** (x:以十六進位制顯示 w:以 4 位元組為一個單位顯示)來檢視從斷點處起的 40 位元組的記憶體值，由於 esp 是我們的程式流指標，其裡面儲存了程式在 func 中執行時記憶體的變化。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/bof_x.jpg)

從圖發現從第一個出現 0x41 的地方到 0xdeadbeef 的距離是 13 個單位，而一個單位是 4 位元組，也就是說偏移量為 52 個位元組。

所以，只要建造出 52 個位元組然後加上 0xcafebabe，用它們來覆蓋 0xdeadbeef 即可。

* exploit.py
![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/bof_exploit.jpg)

最後，執行 exploit.py 即可找到答案。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/bof_final.jpg)

daddy, I just pwned a buFFer :)

[參考資料]
* https://www.itread01.com/content/1545128766.html
