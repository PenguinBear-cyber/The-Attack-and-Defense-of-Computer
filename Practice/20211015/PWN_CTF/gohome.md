# Level 1 - gohome

[題目]

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211015/PWN_CTF/image/gohome_topic.png)

[解題]

首先，先下載題目提供的檔案進行查看，下圖為 gohome.c 的檔案內容:

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211015/PWN_CTF/image/gohome_c.png)

由上圖來看，名為 **Billyshouse** 的 function 可能存有目標 flag，但在 main function 中，並無回傳 Billyshouse 此 function，若能取得 Billyshouse function 中的資料，即可能找出所要的 flag。

因此，可以開始將此檔案進行逆向分析，這裡使用 radare2 逆向分析工具進行。

首先，
