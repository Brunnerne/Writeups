# crack-the-key (crypto)

We get a public key in PEM format and have a hint to factordb.
So simply parsing the pubkey and entering `n` into factordb.com, we get the two prime factors `p` and `q`.

With these, we just find the private key trivially and decrypt the flag:

```py
from base64 import b64decode
from Crypto.PublicKey import RSA
from Crypto.Util.number import *

key = RSA.import_key(open('pub.pem').read())

# Lookup p and q on factorb
p = 106824314365456746562761668584927045312727977773444260463553547734415788806571
q = 109380489566403719014973591337211389488057388775161611283670009403393352513149

phi = (p - 1) * (q - 1)
n = key.n
e = key.e
d = pow(e, -1, phi)

ct = bytes_to_long(b64decode(open('flag.enc').read().encode()))
flag = pow(ct, d, n)
print(long_to_bytes(flag).decode())
```