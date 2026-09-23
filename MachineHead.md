# Machine Head

## 1. CTF Name
CSAW

## 2. Challenge/Type
**Challenge:** Machine Head

**Type:** Reversing

**Points:** 50

**Author:** WubberDuckkie

## 3. Description
> Every lock in the building answers to one key. We burned the checker into a little machine of our own design — it speaks a language you won't find in any disassembler's opcode table.
> Give it the master key and it'll tell you.

## 4. Files
A single file is provided:

- `masterkey`
This is a 64-bit ELF binary for Linux, although the original variable and function names have been lost.

## 5. Analysis
First, the binary was run to observe its behavior:

<img width="251" height="122" alt="Captura de pantalla 2026-09-23 103935" src="https://github.com/user-attachments/assets/8df31473-ba9f-4566-8d04-fc551af2ef1d" />

This confirms that the program expects exactly one argument and compares it against an internal condition to check if it is the flag.

The binary was loaded into **Ghidra**, and the function processing the flag was found to be `FUN_001010c0`. The function's pseudocode reveals the following:

- `strlen(argv[1]) == 0x35`  the key must be exactly **53 characters** long.
- Instead of comparing the string directly against something, the program enters a `do { ... } while(true)` loop that reads data **3 bytes at a time**.

Each 3-byte group is interpreted as `(opcode, arg1, arg2)`, and depending on the `opcode` value, the program performs a different operation on an 8-byte buffer (`local_18[8]`). Finally, if a boolean flag does not fail any of the internal comparisons, the program prints `"Correct! That's the master key."`.

This pattern is characteristic of a **custom bytecode virtual machine (VM)**: the author designed their own instruction set, wrote a "program" in that invented language, and embedded it as data within the binary alongside its own interpreter. This is a deliberate obfuscation technique: there is no obvious comparison to be found using `strings` or `grep` for constants one must first understand the invented language.


It is common in reverse engineering challenges to use a custom bytecode interpreter to hide a program's sensitive logic and protect the code using a standard disassembler reveals only a generic, repetitive loop rather than the actual program. To analyze it, one must manually reconstruct the semantics of each opcode the following opcode dictionary was reconstructed based on the pseudocode:

`0x91` = `reg[a1] = input[a2]`  loads a key character into a register

`0x7a` = `reg[a1] ^= a2` XOR with a literal constant

`0x4e` = `reg[a1] ^= reg[a2]` XOR with another register

`0x1d` = `reg[a1] *= a2` multiplication (mod 256) by a constant

`0x24` = `reg[a1] += a2` addition with a constant

`0x6d` = `reg[a1] += reg[a2]` addition with another register

`0xb2` = circular left rotation of `reg[a1]` by `a2 & 7` bits

`0xa7` = compares `reg[a1]` against literal `a2` if they differ, marks a failure (but execution continues)

`0xe0` = end of program: if no failures occurred, prints the success message

Each of the 53 key characters undergoes a sequence of operations (load, XOR, addition, rotation, XOR, multiplication, comparison), making a brute-force solution practically impossible.


The solution involves translating each instruction into an equivalent operation on bitvectors. Each comparison (`0xa7`) becomes a constraint that the set of final values ​​must satisfy. Then, using an **SMT solver** (Microsoft's **Z3** was used), you can automatically and within milliseconds find the unique combination of 53 values ​​that satisfies all the constraints simultaneously.

## 6. Solution

The pseudocode points to the address where the instruction array begins. That address falls within the `.rodata` section.
The bytes were grouped in sets of three (`opcode, arg1, arg2`) up to the `0xe0` instruction, resulting in a total of **638 instructions**. 

<img width="407" height="40" alt="Captura de pantalla 2026-09-23 103943" src="https://github.com/user-attachments/assets/222dd96a-6c95-4204-aa30-db257060f1fb" />


```python
import z3

instrs = [...]  # all 638 instructions
FLAG_LEN = 0x35  # 53 characters

solver = z3.Solver()
inp = [z3.BitVec(f'c{i}', 8) for i in range(FLAG_LEN)]
for c in inp:
    solver.add(c >= 0x20, c <= 0x7e)

regs = [z3.BitVecVal(0, 8) for _ in range(8)]

for op, a1, a2 in instrs:
    if op == 0x91:
        regs[a1] = inp[a2]
    elif op == 0x7a:
        regs[a1] = regs[a1] ^ z3.BitVecVal(a2, 8)
    elif op == 0x4e:
        regs[a1] = regs[a1] ^ regs[a2]
    elif op == 0x1d:
        regs[a1] = regs[a1] * z3.BitVecVal(a2, 8)
    elif op == 0x24:
        regs[a1] = regs[a1] + z3.BitVecVal(a2, 8)
    elif op == 0x6d:
        regs[a1] = regs[a1] + regs[a2]
    elif op == 0xb2:
        regs[a1] = z3.RotateLeft(regs[a1], a2 & 7)
    elif op == 0xa7:
        solver.add(regs[a1] == z3.BitVecVal(a2, 8))
    elif op == 0xe0:
        break

if solver.check() == z3.sat:
    m = solver.model()
    flag = bytes(m[c].as_long() for c in inp)
    print(flag)
```
**Execute and verify.**

<img width="461" height="108" alt="Captura de pantalla 2026-09-23 103955" src="https://github.com/user-attachments/assets/28f47655-3526-4b81-a6c0-9c18ce9069cc" />

 ---
<img width="410" height="132" alt="Captura de pantalla 2026-09-23 104003" src="https://github.com/user-attachments/assets/a65ad226-357e-4aca-85d3-ca0e9d232900" />


## 7. Flag
```
csaw{cl1mb1ng_th3_v1rtu4l_st4ck_0n3_0pc0d3_4t_4_t1m3}
```

## 8. Lessons / How to make this harder
Although this is not a real-world malware or incident response challenge, it is worth noting what would make this scheme more resistant to analysis (useful both for designing future challenges and for understanding actual anti-reverse engineering protections):

- **Vary instruction order and size** (variable-length opcodes, interleaved junk code) to hinder automatic parsing of the bytecode table.
- **Use non-linear operations or operations dependent on external memory** so that symbolic execution suffers from path explosion, rather than a linear sequence of 638 instructions that is easily translated into Z3 constraints.
- **Anti-debugging / anti-emulation** at the binary level, to make even the initial extraction of the bytecode from `.rodata` difficult.
