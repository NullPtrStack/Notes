
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
`b *0x55555555555D` </br>
!!note!! might have to kill old process if gdb-peda or nc (if binary is a server) did not exit correctly, then restart.

`killall nc ; killall binary `