# gpt-encrypt (crypto)

This is a very simple encryption scheme, first choosing 16 random key bytes and generating 4 round keys.
These round keys are simply generated by placing the key bytes in a matrix, transposing, and taking the four rows as round keys.

The same transformation is done to the plaintext, which is then encrypted over 4 rounds with a new round key for each iteration.
The round encryption is simply adding the round key row to each row in the plaintext. Then the resulting state is shifted (rolled) one row upwards and then one column to the left.

This means that the items in a given row and column will always stay together in the same row and column throughout the encryption.
In practice, all values in a given column are encrypted by adding the same four values to it, and each element in the matrix ends up in the same position in the end:
```
| a, e, i, m |
| b, f, j, n |
| c, g, k, o |
| d, h, l, p |

        |
        V

| a + w, e + x, i + y, m + z |
| b + w, f + x, j + y, n + z |
| c + w, g + x, k + y, o + z |
| d + w, h + x, l + y, p + z |
```
This makes it simple to decrypt a given block: for each column, just bruteforce all the 255 possible subtractions until one makes sense (easiest for first column due to flag format).

The same key is used for each block, so in practice we only need to find 4 single key values.

The only other hurdle is the mode, which is CBC. We have the IV, so the process is now fairly simple:
For each of the four columns try all possible subtractions from 1 to 255 (modulo 256) and XOR the result with the IV.
If the result looks right, note down the subtraction value and the decrypted column.

The subtraction values found are 162, 74, 165, and 137, resulting in the first plaintext block `dam{1iN34R-B1O<K`.

For the next block, we already know the four subtraction values from the first block, and we just repeat the process using the previous ciphertext block as the IV.
