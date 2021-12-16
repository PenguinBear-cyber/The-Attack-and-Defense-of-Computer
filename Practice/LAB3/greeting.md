# greeting

[執行程式]

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB3/image/greeting_topic.jpg)

[解題]

首先執行程式後，會顯示一些訊息並請我們輸入我們的姓名，然後印出問候的訊息。接著使用 ghidra 工具進行程式解析，以下為其 main function 的程式內容:

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB3/image/greeting_main.jpg)

我們可以看到 main 函數中存在一個格式化字符串漏洞：
