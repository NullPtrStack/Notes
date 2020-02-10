
## Template 1
```
from pwn import *
context.terminal = ['tmux', 'splitw', '-v']
context.update(arch='amd64', os='linux')
p = process(./app)
#p = remote('10.10.10.10' , 1234)
```

## Template 2
```
from pwn import *
context.terminal = ['tmux', 'splitw', '-v']
context.update(arch='amd64', os='linux')
p = gdb.debug('./app' , 'source script1')
p.recvuntil('enter')
p.sendline('DEBUG')

```
