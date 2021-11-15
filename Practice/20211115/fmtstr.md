# Level 2 - fmtstr

[題目]

![image](https://github.com/PenguinBear-cyber/The-Attack-and-Defense-of-Computer/blob/main/Practice/20211115/image/fmtstr_topic.png)

[解題]

首先，我們可以看到題目給我們兩個檔案 **fmtstr** 和 **fmtstr.c**，打開 fmtstr.c 進行原始碼分析。

![image]()

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

![image]()

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
