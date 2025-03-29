# Order

## Challenge Description
> ![image](https://github.com/user-attachments/assets/080c3393-3621-4b09-84c5-1b62597bb050)


## Approach
Since the XOR cipher uses a repeating key, we can extract the key by XORing the ciphertext with the known plaintext `ORDER:`. Once we obtain the key, we can decrypt the rest of the message.

The following script performs the decryption:

```python
import binascii
from itertools import cycle

def xor_decrypt(ciphertext, key):
    key_cycle = cycle(key)
    plaintext = bytes([c ^ next(key_cycle) for c in ciphertext])
    return plaintext.decode(errors='ignore')

def find_xor_key(ciphertext, known_plaintext):
    key = []
    for i in range(len(known_plaintext)):
        key.append(ciphertext[i] ^ ord(known_plaintext[i]))
    return bytes(key)

def main():
    hex_ciphertext = """
    1c1c01041963730f31352a3a386e24356b3d32392b6f6b0d323c22243f6373
    1a0d0c302d3b2b1a292a3a38282c2f222d2a112d282c31202d2d2e24352e60
    """.replace("\n", "").replace(" ", "")
    
    ciphertext = binascii.unhexlify(hex_ciphertext)
    known_plaintext = "ORDER:"
    
    key = find_xor_key(ciphertext, known_plaintext)
    decrypted_text = xor_decrypt(ciphertext, key)
    
    print("Decryption Key:", key.decode(errors='ignore'))
    print("Decrypted Message:", decrypted_text)

if __name__ == "__main__":
    main()
```

### Explanation
1. Convert the hex-encoded ciphertext into raw bytes.
2. Extract the XOR key by comparing the ciphertext with the known plaintext.
3. Use the recovered key to decrypt the entire message.
4. Print the decryption key and the fully decrypted message.

After running the script, we obtain the decrypted message, which reveals the flag: `THM{the_hackfinity_highschool}`

## Conclusion
This challenge demonstrated the weakness of using a predictable header in a repeating-key XOR cipher. By exploiting this flaw, we successfully extracted the encryption key and decrypted the message.

## Flag: 
THM{the_hackfinity_highschool}






   
