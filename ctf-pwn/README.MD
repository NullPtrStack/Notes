
### Format string bug
````
printf '%1$hhx %1$hx %1$x %1$lx'

b'3 fa03 f7fafa03 7ffff7fafa03\n'
``` </br>
need to setup stack/buffer then write to 64bit address (0x007ffff7fffa030) due to the 0x00 in most significant bits = multi stage payload `fgets(buff, 0x100, stdin)`</br>
hijack execution via overwriting plt.got, or </br>
hijack __free_hook, __malloc_hook from printf, then printf(%100000c)

### Find pid, then pwn-tool attach
```
gdb> info inferior
gdb> python print(gdb.selected_inferior().pid)

# pwn tool
gdb.attach(1234)
```
http://docs.pwntools.com/en/stable/gdb.html

### get ELF pd
```
e = ELF('./paycalc')
r = e.process()
print util.proc.pidof(r) #an array of pid--> [1234] 
```

## gef,pwngdb commands
```
info proc map
info inferior
x/20x $rsp
vmmap
```

### ROP

[https://blog.techorganic.com/2016/03/18/64-bit-linux-stack-smashing-tutorial-part-3/](https://blog.techorganic.com/2016/03/18/64-bit-linux-stack-smashing-tutorial-part-3/) </br>
[https://ropemporium.com/guide.html](https://ropemporium.com/guide.html) </br>
[https://www.ret2rop.com/2018/08/format-string-defeating-stack-canary-nx-aslr-remote.html](https://www.ret2rop.com/2018/08/format-string-defeating-stack-canary-nx-aslr-remote.html) </br>
[ROP and pwn tool corefile debug](https://medium.com/@iseethieves/intro-to-rop-rop-emporium-split-9b2ec6d4db08) </br>


### static analysis
```
use ghidra , IDA pro free disassembl

```

## Setup :
### 1. socat/nc
```
socat TCP-LISTEN:1234,reuseaddr,fork EXEC:./vuln
nc 1234

nc -l 9999 --exec ./vuln
nc 9999

socat tcp-l:1235,fork exec:./vuln
nc 1235
``` 
https://stackoverflow.com/questions/46941045/netcat-for-execute-remote-elf-file-like-ctfs


### objdump
Check plt GOT
```objdump -d -s -j .plt -j .got.plt```

### Tools
```
gef
# via the install script
$ wget -q -O- https://github.com/hugsy/gef/raw/master/scripts/gef.sh | sh

# manually
$ wget -O ~/.gdbinit-gef.py -q https://github.com/hugsy/gef/raw/master/gef.py
$ echo source ~/.gdbinit-gef.py >> ~/.gdbinit


gdb-peda
git clone https://github.com/longld/peda.git ~/peda
echo "source ~/peda/peda.py" >> ~/.gdbinit
echo "DONE! debug your program with gdb and enjoy"
```
### x86 32bit function call
To pass parameters to the subroutine, push them onto the stack before the call. The parameters
should be pushed in inverted order (i.e. last parameter first)

https://en.wikibooks.org/wiki/X86_Disassembly/Calling_Conventions </br>
https://aaronbloomfield.github.io/pdr/book/x86-32bit-ccc-chapter.pdf </br>

### x86 AMD64 function call
function ( rdi, rsi, rdx, rcx, r8, r9 )
To pass parameters to the subroutine, we put up to six of them into registers (in order: rdi, rsi,
rdx, rcx, r8, r9). If there are more than six parameters to the subroutine, then push the rest onto
the stack in reverse order (i.e. last parameter first) – since the stack grows down, the first of the
extra parameters (really the seventh parameter) parameter will be stored at the lowest address (this
inversion of parameters was historically used to allow functions to be passed a variable number of
parameters). </br>
https://stackoverflow.com/questions/18024672/what-registers-are-preserved-through-a-linux-x86-64-function-call </br>
https://aaronbloomfield.github.io/pdr/book/x86-64bit-ccc-chapter.pdf </br>

### Canary
![alt text](canary.jpg "Canary") </br>