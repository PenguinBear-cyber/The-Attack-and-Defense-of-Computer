# 緩衝區溢位(Buffer Overflow, BOF)

**緩衝區溢位**指的是根據程式設計的缺陷，對程式中的緩衝區寫入超過緩衝區能負荷儲存的巨量資料，使程式出現異常操作，從中破壞程式的執行，並趁程式中斷時取得資料或系統控制權等攻擊行為。

一般來說，正常使用的情況下，輸入的值(input data)會小於緩衝區的大小(buffer size)，如下圖:![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211015/normal%20mode%20of%20%20input.jpg)

但若程式設計者在設計讀取輸入值至緩衝區時，忽略了要檢查輸入值長度，攻擊者就可以透過這樣的疏失進行攻擊，可以對此程式輸入一筆較長的資料(超過緩衝區乘載的資料量)來造成程式癱瘓或改變執行流程，如下圖:![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211015/buffer%20overflow_pic.jpg)

因此，當輸入值過長又沒檢查是否超過緩衝區長度時，就會使輸入值覆寫掉一些存在 stack 中的訊息，當這個存放程式 Call back 位址的 Return address 被修改後，即可能因為寫入的值而導致系統癱瘓或被導向攻擊者寫入 Shellcode 的位址。

但在進行 Buffer Overflow 攻擊前，攻擊者必須精準知道 Buffer size、Return address、各個暫存器的值，但這些值通常會根據不同電腦架構或作業系統而有所不同，所以不見得會攻擊成功。

通常會造成此現象的原因有:
* 存在缺陷的程式設計
* C語言較其他高階語言有更高的風險(因為高階語言會自動進行陣列或指標的堆疊區塊邊界檢查，而C沒有)
* C語言中的C標準庫具有一些非常危險的操作函式，若使用不當也會造成溢位發生

因此，想要避免成為被攻擊的目標，可以使用以下方式：
* 使用較安全的函式(eg. strncpy 來取代 strcpy)
* 加上輸入值與緩衝區長度檢查機制
* 用較高階的程式語言(eg. Java)
* 使用工具檢查程式是否有安全性漏洞

最後，Buffer Overflow 為較舊式的攻擊方式，已經很久沒在 OWASP Top 10 名單中，但在 Common Vulnerabilities and Exposures（CVE）所公布的漏洞報告，依然屬於常見的攻擊手法。

參考資料:
* 緩衝區溢位。維基百科。檢自https://zh.wikipedia.org/wiki/%E7%BC%93%E5%86%B2%E5%8C%BA%E6%BA%A2%E5%87%BA
* 攻擊行為－緩衝區溢位 Buffer Overflow。iT邦幫忙。檢自https://ithelp.ithome.com.tw/articles/10188599
