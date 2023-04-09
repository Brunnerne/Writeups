# sha-mac (crypto)

Padding oracle with hash length extension - highly scuffed due to issues with first block and major timeout issues:

```py
from pwn import *
import hashlib
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad

from hashlib import sha256
import os
import string

context.log_level = "error"

KEY = os.urandom(32)
MAC_KEY = os.urandom(32)
FLAG = b"DAM{YOU_GOT_IT!}" * 2


class FlagFound(Exception):
    pass


def encrypt_and_sign(msg):
    iv = os.urandom(16)

    cipher = AES.new(KEY, AES.MODE_CBC, iv)
    padded_msg = pad(msg, 16)
    encrypted_msg = cipher.encrypt(padded_msg)
    ciphertext = iv + encrypted_msg
    auth = sha256(MAC_KEY + ciphertext)
    return ciphertext + auth.digest()


def decrypt_signed(ct):
    if len(ct) < 64:
        raise ValueError("Ciphertext too short")

    iv, encrypted, auth = ct[:16], ct[16:-32], ct[-32:]
    assert sha256(MAC_KEY + iv + encrypted).digest() == auth

    cipher = AES.new(KEY, AES.MODE_CBC, iv)
    decrypted = cipher.decrypt(encrypted)
    #print('dec:')
    #print(decrypted[:-16])
    #print(decrypted[-16:])

    unpadded = unpad(decrypted, 16)
    #print(decrypted[-16:])
    if FLAG in unpadded:
        raise FlagFound()

    return unpadded


def hashextender(known, length, toappend):
    # ./hash_extender -d "" -s "<known hash>" -f "<format>" -l "<message length>" -a "<to append>"
    p = process(
        [
            "./hash_extender/hash_extender",
            "-d",
            "",
            "-s",
            known,
            "-f",
            "sha256",
            "-l",
            str(length),
            "-a",
            toappend.hex(),
            '--append-format=hex'
        ]
    )
    p.recvuntil(b"New signature: ")
    newsig = p.recvline().decode().strip()
    p.recvuntil(b"New string: ")
    newappend = p.recvline().decode().strip()
    p.close()
    return newsig, newappend


def oracle(msg, flagiv, flagct, flagauth, length):
    try:
        authtag, new = hashextender(flagauth.hex(), length, msg)
        #authtag, new = hashextender(flagauth.hex(), length, msg[:-32] + xor(msg[-32:-16],flagiv) + msg[-16:])
        msg2 = flagiv + flagct + bytes.fromhex(new) + bytes.fromhex(authtag)
        
        decrypt_signed(msg2)
        return True
    except Exception as e:
        if "is incorrect" in str(e) or 'Inappropriate' in str(e):
            return False
        return True



def attack2(flagiv, flagct, flagauth, length):
    message = flagct + flagct[:16]
    print('iv:')
    print(flagiv)
    print(len(message))
    reconstructed = b""
    k1 = string.printable
    k2 = [ord(x) for x in k1]
    while len(message) >= 32:
        block = [0] * 16

        for i in range(1,17):
            pad = [0] * (16 - i) + [i] * i
            for x in range(1,256):
                block[-i] = (x)
                if x == i:
                    continue

                test = bytearray(message)
                for j in range(16):
                    test[-32 + j] ^= block[j] ^ pad[j] ^ flagct[(-16)+j] ^ flagiv[j]
                test = bytes(test)
                
                responsecode = oracle(test, flagiv, flagct, flagauth, length)

                if responsecode:
                    break
            else:
                print('fuckloop')
                block[-i] = i
            print('block:')

            print(x)
            print(xor(xor(block, flagct[-16:]), flagiv))
        reconstructed = bytes(block) + reconstructed
        message = message[:-16]
        
        print("reconstructed:")
        print(reconstructed)
        print(xor(reconstructed, flagct[-16:]))
        print(xor(xor(reconstructed, flagct[-16:]), flagiv))
        exit()
    return reconstructed
            

def main():
    global flagxoriv
    flag_enc = encrypt_and_sign(FLAG)
    
    
    print('this is flag xor iv:')
    

    flagiv, flagct, flagauth = flag_enc[:16], flag_enc[16:-32], flag_enc[-32:]
    flagxoriv = xor(FLAG[:16],flagiv)
    print(flagxoriv)
    
    print('this is flag ecb:')
    cipher = AES.new(KEY, AES.MODE_ECB)
    pttt = cipher.decrypt(flagct)

    print(pttt)
    
    print('flagiv:' + flagiv.hex())
    print('flagct:' + flagct.hex())
    #print(flagct[-32:-16])
    print('flagauth:' + flagauth.hex())
    

    wasenced = flagiv + flagct
    length = 32 + len(wasenced)

    attack2(flagiv, flagct, flagauth, length)
    
main()
```