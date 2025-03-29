# Cipher's Secret Message

## Challenge Description
> ![image](https://github.com/user-attachments/assets/2ff2c833-cdbf-448b-bd01-0cc13fd97255)

## Solution
The encryption algorithm performs a shifting operation on each alphabetical character:
```python
def enc(plaintext):
    return "".join(
        chr((ord(c) - (base := ord('A') if c.isupper() else ord('a')) + i) % 26 + base) 
        if c.isalpha() else c
        for i, c in enumerate(plaintext)
    )
```
Key characteristics:
1. Case-sensitive: Maintains original case (uppercase/lowercase)
2. Position-dependent: Shift amount depends on character position (index i)
3. Non-alphabetical: Leaves numbers and symbols unchanged
4. Modulo 26: Wraps around within the alphabet

To reverse this, we need to:
1. Subtract the position index (i) from each character
2. Handle case sensitivity
3. Maintain modulo 26 wrapping
4. Preserve non-alphabetical characters

Here's the decryption function:   
```python
def dec(ciphertext):
    return "".join(
        chr((ord(c) - (base := ord('A') if c.isupper() else ord('a')) - i) % 26 + base)
        if c.isalpha() else c
        for i, c in enumerate(ciphertext)
    )

ciphertext = "a_up4qr_kaiaf0_bujktaz_qm_su4ux_cpbq_ETZ_rhrudm"

decoded_message = dec(ciphertext)

print("Decoded Message: THM{" + decoded_message + "}")
```

Running the script reveals the flag: `THM{a_sm4ll_crypt0_message_to_st4rt_with_THM_cracks}`

## Flag: 
THM{a_sm4ll_crypt0_message_to_st4rt_with_THM_cracks}

