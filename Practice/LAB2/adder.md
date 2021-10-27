# adder

這裡會使用一個逆向分析工具 - **Ghidra**。

**Ghidra** 是一逆向工程工具，其主要功能包含反組譯、組譯、反編譯及繪圖和腳本等等功能，此外 Ghidra 也支援各種不同的指令、架構及可執行檔(PE,ELF…)，在使用上則可以選擇使用者互動模式及自動模式。若預設的功能無法完成使用者想要的結果，也提供了公開的API允許使用者用以自行開發個人的插件及腳本。
Ghidra 有以下特點及功能:
* 專案管理: 使用者可以自行建立專案資料夾，在將欲分析的檔案匯入，所有分析得到的檔案都會存放在這層目錄下，在查詢相關資訊十分方便。
* 代碼瀏覽器: 在檔案匯入完成後，就會在視窗內展示可視化資訊。正面有反組譯成功的組譯碼、右方則是若能成功反編譯時對應指定的反組譯區塊的程式碼、左方則是一些可結構化呈現，包含了 program tree、symbol tree等。
* 符號樹(symbol tree): 這個視窗會列出所有程式中的符號，像是 Import、Export、類別、函數等。
* 反編譯器: Ghidra 當然也有逆向工具必備的反編譯器，當我們指定到反組譯表區塊時，反編譯器視窗將同步顯示出對應的程式碼，更能看出之間的相關性。
* 代碼修補及十六進位視窗: 就如同 IDA 一樣，Ghidra 也提供了編輯代碼的功能，可以更動一部份的數值，此外也可以從十六進位的視窗中選擇一部份的代碼複製起來並加入到另一個程式中，在載入額外的程式中甚至也能選擇要加入到那一個區塊或是哪個位址，甚至連長度都能自由指定，可說是十分有彈性的部份。
* 可視圖: 對於需要反組譯工具的使用者來說，在許多呼叫和返回的部份來回跳轉是十分辛苦的過程，因此對於將程式區塊之間彼此呼叫的關係以可視圖的方式呈現其之間的關係的功能是極其重要的，此外為了加強理解，也有提供其它選項像是變換起始區塊顏色或是選擇不同的條件來呈現可視圖。
* 特徵搜尋: Ghidra 提供了許多不同的特徵搜尋方式，例如以字串搜尋、以純量搜尋等，此外條件的追加搜尋也是可能的。
* 特徵腳本: Ghidra 以 Java 語言提供了超過 200 個用以搜尋特定條件的腳本，例如說 FindImagesScript.java 就能搜尋 PNG 和 GIF，若不喜歡使用 Java 語言，這些腳本都是以公開的 API 所撰寫的，使用者也能選擇使用 python 來撰寫腳本。
---
[開始解題]

首先，我們可以執行看看 adder 檔案，中間可能會遇到 **Permission Denied**，記得先 *_chmod +x adder_* 變更權限。許可完執行權限後，可以看到 adder 要求我們輸入三個數字。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/adder_run.jpg)
 
 輸入完成後，其回傳一個 nope 給我們。這樣我們可以開始對檔案進行分析，先打開 Ghidra 並創建一個 project 將 adder 檔案匯入，

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/adder_import.jpg)

匯入完成後，它會先問是否想將檔案進行分析，選擇 yes。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/adder_analyze.jpg)

等待工具分析完成後，就可以透過其代碼瀏覽器進行觀察，從中發現，在左側 symbol tree 中可見 main function，雙擊後會呈現下圖，

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/adder_main.jpg)

最右側會有反編譯的結果程式，如圖:

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB2/image/adder_maincode.jpg)

我們看到底色黃色的部分得知，三個 integer 相加起來等於 **0x539** 即可能找出我們想要的 flag 了。

因此，下一步就要解開 **0x539** 等於數字多少，我們使用 python 來幫我們計算即可以得到結果，如下圖。

![image]()

我們知道 **0x539** 等於數字 **1337**，最後就可以再執行一次 adder 檔案，輸入三個數總和為 1337，即找到我們想要的 flag 了。

![image]()
