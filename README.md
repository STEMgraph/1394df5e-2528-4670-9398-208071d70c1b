<!---
{
  "id": "1394df5e-2528-4670-9398-208071d70c1b",
  "teaches": "From `printf` to `scanf`: Reading Input from Streams",
  "depends_on": ["279a01d6-7696-46b7-9cb7-2c44773ad06b"],
  "author": "Stephan Bökelmann",
  "first_used": "2025-04-01",
  "keywords": ["scanf", "streams", "C", "input"]
}
--->

# From `printf` to `scanf`: Reading Input from Streams

## 1) Introduction

In the [previous exercise](https://github.com/STEMgraph/279a01d6-7696-46b7-9cb7-2c44773ad06b), we used `fprintf` to write simple messages to the terminal. Now that we can send data **out**, it's time to learn how to get data **in**.

In C, the most common function for reading input is `scanf`. It reads formatted data from `stdin` — the standard input stream, usually the keyboard.

To really understand what `scanf` does, we’ll take a look at what happens behind the scenes on a lower level. Specifically, we’ll look at how Assembly and system calls perform the same kind of input operation.

```asm
01  section .bss
02      buffer resb 32            ; reserve 32 bytes
03
04  section .text
05      global _start
06
07  _start:
08      mov     rax, 0            ; syscall: read
09      mov     rdi, 0            ; file descriptor 0 = stdin
10      mov     rsi, buffer       ; pointer to memory buffer
11      mov     rdx, 32           ; max number of bytes to read
12      syscall
13
14      ; syscall: exit
15      mov     rax, 60
16      xor     rdi, rdi
17      syscall
```

This program reads up to 32 bytes from standard input and stores them in a buffer.

- Lines 01–02 define a memory area where input can be stored.
- Lines 08–12 prepare and invoke the `read` system call.
- Line 09 uses file descriptor `0`, which corresponds to `stdin`.

---

As you already know, in POSIX systems, three standard streams are available to every process by default:

| File Descriptor | Symbolic Name | Description |
|-----------------|----------------|-------------|
| `0`             | `stdin`        | Standard input (keyboard, file, or pipe) |
| `1`             | `stdout`       | Standard output (terminal screen) |
| `2`             | `stderr`       | Standard error output (also terminal) |

These correspond directly to how `printf` and `scanf` interact with the outside world.

Now let’s move from Assembly back to C. Here’s a minimal example that uses `scanf`:

```c
#include <stdio.h>

int main(void) {
    char name[32];

    printf("Enter your name: ");
    scanf("%31s", name);  // safe bounded input
    printf("Hello, %s!\n", name);

    return 0;
}
```

This reads a single word (up to 31 characters) from the keyboard and prints it back to the screen.

- `scanf("%31s", name)` ensures that the input won’t overflow the `name` buffer.
- Input is taken from `stdin`, which typically means the keyboard.


## 2) Tasks

1. **Use scanf**: Create a small program that asks for the user's name and age, then prints both.
2. **Explore fgets**: Replace `scanf` with `fgets` and read a full line instead.
3. **Compare to ASM**: Match the C code to the Assembly syscall behavior.


## 3) Questions

1. What does `scanf` do, and from where does it read by default?

2. What are the risks of using `scanf("%s", ...)` without a length specifier?

3. How is the `read` system call used in Assembly to receive input from `stdin`?

4. What is the purpose of file descriptor `0`?

## 4) Advice

Don’t stop at writing output — reading and processing input is what gives your programs real interactivity. Practice reading both from the terminal and from files. Always guard against buffer overflows by specifying maximum lengths in `scanf`, and use `fgets` when reading entire lines. Getting comfortable with streams and input handling is an essential skill in systems programming.
