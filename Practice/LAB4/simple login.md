# simple login

[題目] pwnable.kr (https://pwnable.kr/play.php)

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/simple%20login_topic.jpg)

[執行程式]

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/simple%20login_excute.jpg)

[解題]

透過執行此程式後，可以看到題目要我們輸入 Authenticate，當隨意輸入完成後，會顯示一串 hash value 即結束程式。

首先，使用 ghidra 來進行靜態分析，觀察 main 的架構，如圖。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/simple%20login_main.jpg)

根據上圖架構開始進行分析。

```
        08049316 c7 44 24        MOV        dword ptr [ESP + local_48],0x1e
                 08 1e 00 
                 00 00
        0804931e c7 44 24        MOV        dword ptr [ESP + local_4c],0x0
                 04 00 00 
                 00 00
        08049326 8d 44 24 1e     LEA        EAX=>local_32,[ESP + 0x1e]
        0804932a 89 04 24        MOV        dword ptr [ESP]=>local_50,EAX
        0804932d e8 ae ef        CALL       memset                                           void * memset(void * __s, int __
```
參數由右往左依序入列，注意到這行 **_LEA  EAX=>local_32,[ESP + 0x1e]_**，可以把 **ESP + 0x1e** 設成 **var1**，這樣得到 **memset(&var1, 0, 30)**

```
        08049332 a1 60 b8        MOV        EAX,[->_IO_2_1_stdout_]                          = 0811b4e0
                 11 08
        08049337 c7 44 24        MOV        dword ptr [ESP + local_44],0x0
                 0c 00 00 
                 00 00
        0804933f c7 44 24        MOV        dword ptr [ESP + local_48],0x2
                 08 02 00 
                 00 00
        08049347 c7 44 24        MOV        dword ptr [ESP + local_4c],0x0
                 04 00 00 
                 00 00
        0804934f 89 04 24        MOV        dword ptr [ESP]=>local_50,EAX=>_IO_2_1_stdout_   = 
        08049352 e8 29 33        CALL       setvbuf                                          int setvbuf(FILE * __stream, cha
```
這裡得到 **setvbuf(0, 2,0)

