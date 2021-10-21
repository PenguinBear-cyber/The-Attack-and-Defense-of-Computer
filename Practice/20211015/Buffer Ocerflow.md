# 緩衝區溢位(Buffer Overflow, BOF)

**緩衝區溢位**指的是根據程式設計的缺陷，對程式中的緩衝區寫入超過緩衝區能負荷儲存的巨量資料，使程式出現異常操作，從中破壞程式的執行，並趁程式中斷時取得資料或系統控制權等攻擊行為。

一般來說，正常使用的情況下，輸入的值(input data)會小於緩衝區的大小(buffer size)，如下圖:![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211015/normal%20mode%20of%20%20input.jpg)

但若程式設計者在設計讀取輸入值至緩衝區時，忽略了要檢查輸入值長度，攻擊者就可以透過這樣的疏失進行攻擊，可以對此程式輸入一筆較長的資料(超過緩衝區乘載的資料量)來造成程式癱瘓或改變執行流程，如下圖:![image]()
