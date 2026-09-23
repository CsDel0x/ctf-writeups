# Csaw-quals-2025
## Shadow Protocol
## Description
Author: jackhax

Space explorers have recovered a strange encryption oracle drifting in deep space, originating from an alien civilization in a distant galaxy.
The oracle emits encrypted transmissions using an unfamiliar cosmic protocol.

nc chals.ctf.csaw.io 21002

## Files

- shadow_protocol (64-bit ELF binary)-
- Size: -16.6 Kb-

## Analysis
When we connect, the server provides us with two pieces of information:

An encrypted message in HEX format and a seed based on a timestamp; these two pieces of information change if you reconnect to the server.

<img width="558" height="163" alt="Captura de pantalla 2026-09-23 112329" src="https://github.com/user-attachments/assets/4acffaee-a0d1-4344-8bd5-3be9be0b12c0" />

Analyzing the binary, we found interesting things, such as a fake flag in the main: CSAW{f4kH3_fl4g...}. It was also discovered that the file has a vulnerability when using an 8-bit XOR cipher and repeating the key. The initial hypothesis was to use a rand() to replicate the keystream, but it didn't work because my local rand() wasn't the same as the server's.

## Theory
- XOR encryption is a symmetric bitwise operation that depends only on a single key.
- A Repeating-Key XOR is its weaker version, as it reuses the key for each bit.
- Computers do not generate random numbers, but rather predictable sequences based on a seed.
- Known-Plaintext Attacks are based on deducing the keystream of a HEX using only a portion of the ciphertext, such as: csawctf{}

## Solution
1. The binary was analyzed in ghidra. Upon analyzing the main file, it was discovered that the file was an 8-bit repeating-key XOR. It was also confirmed that the seed given by the server was (time(NULL) / 60) * 60, the key was generated from a rand() and a srand().
2. We knew the flag started with: csawctf{ so we connected to the server and copied the seed and the HEX. Without disconnecting from the server, we executed this Python script:

<img width="425" height="234" alt="Captura de pantalla 2026-09-23 112340" src="https://github.com/user-attachments/assets/57361b9b-9970-4360-8f2e-14e2ee495d2f" />

## Flag
> csawctf{r3v3r51ng_5h4d0wy_pr070c015_15_c3741n1y_n07_34sy}

## How to avoid
- Avoid key reuse
- Don't rely on rand() for cryptography
