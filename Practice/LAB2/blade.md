# blade

[解題開始]

首先，先執行看看 blade 這個檔案，如圖。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/blade_run.jpg)

由圖可知，其有個問題給我們回答 **why did you save me?**，隨意輸入回答後即結束程式。

因此，可以開始對檔案進行逆向分析，這裡使用到 Ghidra 來進行分析，開啟 Ghidra 後，創建 project 並將檔案匯入進去，匯入後會先詢問是否要進行分析，按下 yes 後即會呈現下圖。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/blade_show.jpg)

接下來我們可以開始使用 ghidra 工具進行程式的觀察及分析，這裡發現在 symbol tree 底下的 main function 有玄機，如圖。

![image]()

