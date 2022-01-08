# simple login

[題目] pwnable.kr (https://pwnable.kr/play.php)

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/simple%20login_topic.jpg)

[執行程式]

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/simple%20login_excute.jpg)

[解題]

透過執行此程式後，可以看到題目要我們輸入 Authenticate，當隨意輸入完成後，會顯示一串 hash value 即結束程式。


* main()函數分析

首先，使用 ghidra 來進行靜態分析，觀察 main 的程式碼及架構並將其對照，如圖。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/simple%20login_maincode.jpg)

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
這裡得到 **setvbuf(0, 2,0)**

```
        08049357 a1 64 b8        MOV        EAX,[->_IO_2_1_stdin_]                           = 0811b580
                 11 08
        0804935c c7 44 24        MOV        dword ptr [ESP + local_44],0x0
                 0c 00 00 
                 00 00
        08049364 c7 44 24        MOV        dword ptr [ESP + local_48],0x1
                 08 01 00 
                 00 00
        0804936c c7 44 24        MOV        dword ptr [ESP + local_4c],0x0
                 04 00 00 
                 00 00
        08049374 89 04 24        MOV        dword ptr [ESP]=>local_50,EAX=>_IO_2_1_stdin_    = 
        08049377 e8 04 33        CALL       setvbuf                                          int setvbuf(FILE * __stream, cha
```
這裡得到 **setvbuf(stdin, 0, 1, 0)**

```
        0804937c c7 04 24        MOV        dword ptr [ESP]=>local_50,s_Authenticate_:_080   = "Authenticate : "
                 a5 a6 0d 08
        08049383 e8 a8 22        CALL       printf                                           int printf(char * __format, ...)
```
這裡得到 **printf( "Authenticate :" )**

```
        0804939c c7 44 24        MOV        dword ptr [ESP + local_48],0xc
                 08 0c 00 
                 00 00
        080493a4 c7 44 24        MOV        dword ptr [ESP + local_4c],0x0
                 04 00 00 
                 00 00
        080493ac c7 04 24        MOV        dword ptr [ESP]=>local_50,input                  = 
                 40 eb 11 08
        080493b3 e8 28 ef        CALL       memset                                           void * memset(void * __s, int __
```
這裡可以得到 **memset( &input, 0, 12 );**

input 就是程式要我們輸入的地方!!

```
        080493c0 8d 44 24 18     LEA        EAX=>local_38,[ESP + 0x18]
        080493c4 89 44 24 04     MOV        dword ptr [ESP + local_4c],EAX
        080493c8 8d 44 24 1e     LEA        EAX=>local_32,[ESP + 0x1e]
        080493cc 89 04 24        MOV        dword ptr [ESP]=>local_50,EAX
        080493cf e8 c1 fc        CALL       Base64Decode                                     undefined Base64Decode(undefined
```
這裡的 **ESP + 0x18** 可以設成 **var2**

結果得到 **Base64Decode( &var1, &var2 );**

eax 為 Base64Decode 解碼後返回的長度，因此**若輸入長度大於 12(0xd)** 的話則跳轉到 Wrong Length，否則會進到 auth() function 中。

* auth()函數分析

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/simple%20login_authcode.jpg)

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/simple%20login_auth.jpg)

根據上圖開始進行 auth() 分析。

```
        080492a2 8b 45 08        MOV        EAX,dword ptr [EBP + param_1]
        080492a5 89 44 24 08     MOV        dword ptr [ESP + local_24],EAX
        080492a9 c7 44 24        MOV        dword ptr [ESP + local_28],input                 = 
                 04 40 eb 
                 11 08
        080492b1 8d 45 ec        LEA        EAX=>local_18,[EBP + -0x14]
        080492b4 83 c0 0c        ADD        EAX,0xc
        080492b7 89 04 24        MOV        dword ptr [ESP]=>local_2c,EAX
        080492ba e8 a1 03        CALL       memcpy                                           void * memcpy(void * __dest, voi
```
這裡得到 **memcpy( &local1, &input, var3 ); # var3就是main傳過來的參數**

因此，var3 也就是代表 input，其長度最大不可以超過 12 byte。

綜合上面分析來看，程式碼可以看作為:
```
 if( strcmp( "f87cd601aa7fedca99018a8be88eda34", local3 ) == 0 ){
   return 1;
 } // if
 else return 0;
```


* 撰寫 exploit code

觀察分析完以上程式碼後，可以進入攻擊漏洞的環節。要達成三個條件:
1.覆蓋的 ebp 要距離 correct() 函數的起始位址 4 BYTES。
2.執行 correct() 函數時 input 的值要是 0xdeadbeef。
3.只有 12 個 bytes 可以利用。(大於12則不會進行到後面的漏洞)

開始撰寫 exploit code。

首先，找到 input 值。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/simple%20login_input.jpg)

找到繞過correct()驗證後的位址。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/simple%20login_correct.jpg)

exploit.py

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/simple%20login_exploit.jpg)

執行後的畫面

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB4/image/simple%20login_final.jpg)


[參考資料]
* https://ithelp.ithome.com.tw/articles/10221566
* https://ithelp.ithome.com.tw/articles/10221639
