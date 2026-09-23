# SunShine
## Palatine Pack
## Description
Author: Uvuv

Caesar is raiding the Roman treasury to pay off his debts to his Gallic allies, and to you and his army. Help him find the password to make this Lucius Caecilius Metellus guy give up the money! >:) (he is sacrosanct so no violence!)

currently has nonstandard flag format sunshine{}

## Files

- palatinepack (64-bit ELF binary) - 16.4 KB -
- flag.txt (not readable as text) - 296 bytes -

## Analysis

Analyzing flag.txt, we can see that the file does not display any readable text.
<img width="1633" height="169" alt="Captura de pantalla 2025-09-30 200046" src="https://github.com/user-attachments/assets/93d00d34-016a-4e97-a2cd-6f3ec4f6e3b8" />

My initial hypothesis is that the flag.txt file is the obfuscated result of the Palatine pack. When decompiling the binary, we can see these two main functions:

- flipBits: This function uses a Boolean "b = False" that alternates with each byte.
A key "k = 0x69" is declared.

  When b == False: the bytes are inverted with a NOT.

  When b == True: an XOR is performed with k, and then 0x20 is added to k.

  It is invertible because NOT is its own inverse, and XOR uses the same key.
- expand: Each byte is split into Nibbies (high and low) with a Boolean "b = False" that alternates.

  When b == False: two new bytes are created, each with half the original and half the key.

  When b == True: The order is reversed.

  It is invertible because the original nibbies remain intact.

The program applies this sequence:

    flipBits(buf) → expand(buf) → expand(buf) → expand(buf) → write(flag.txt).

To find the flag, we will need to reverse these two functions and apply them in the same sequence as the program.

## Theory
- XOR encryption: is a symmetric bitwise operation that depends only on a single key.
- NOT Inverts all the bits of a byte.
- Nibbie is equal to 4 bits.
- Byte is equal to 8 bits.

## Solution
1. Analyze the binary with ghidra and identify the sequence.
2. Invert the two main functions
3. Apply the same sequence as the program with those functions
4. View the output
   

<img width="459" height="631" alt="Captura de pantalla 2025-09-30 194922" src="https://github.com/user-attachments/assets/78f7d612-db84-4077-9b45-d1881cda5bbf" />

## Flag
>sunshine{C3A5ER_CR055ED_TH3_RUB1C0N}

## How to avoid
- Use standard cryptography (well-made keys)
