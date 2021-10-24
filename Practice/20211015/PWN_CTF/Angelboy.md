# Level 1 - Angelboy_Pwn-2

[題目]

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211015/PWN_CTF/image/Angelboy_topic.png)

[解題]

首先，題目中有提供一個檔案 **ret2sc** ，可以先打下列指令來執行看看此檔案輸出的內容，
> ./ret2sc
```
若產生 Permission denied 的錯誤訊息，即表示未獲得許可/取用的權限。因此需要改變檔案型態，將檔案變更為可執行檔，輸入下列指令即表示給執行權限。
> chmod +x ret2sc
跑完此指令回到上一步即可完成執行檔案。
```
![image]()

由上圖可看見執行此檔案後的結果，需要我們輸入兩個值。

