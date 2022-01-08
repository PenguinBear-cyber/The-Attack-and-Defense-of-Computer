# bof

[題目]pwnable.kr (https://pwnable.kr/play.php)

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/bof_topic.jpg)

[執行程式]

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/bof_run.jpg)

執行程式後，會出現 overflow me:，然後要我們輸入內容，輸入完成後即結束程式。

[解題]

首先，可以用 ghidra 來進行靜態分析，查看程式執行流程及其程式碼。這裡找到兩個核心的程式碼:main()、func()，如圖。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/bof_main.jpg)

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/bof_func.jpg)
