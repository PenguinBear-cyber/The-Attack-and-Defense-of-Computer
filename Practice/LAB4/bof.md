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

