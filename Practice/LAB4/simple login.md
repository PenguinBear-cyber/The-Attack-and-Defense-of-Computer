# simple login

[題目] pwnable.kr (https://pwnable.kr/play.php)

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/simple%20login_topic.jpg)

[執行程式]

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/simple%20login_excute.jpg)

[解題]

透過執行此程式後，可以看到題目要我們輸入 Authenticate，當隨意輸入完成後，會顯示一串 hash value 即結束程式。

首先，使用 ghidra 來進行靜態分析，觀察 main 的架構，如圖。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/simple%20login_main.jpg)

