
## Debug methods
For binary that does not fork/server listen
run this from tmux temrinal, if `terminal=['tmux' , 'new-window']` is not set, then it will launch new window
```
bin = ELF('./heap')
libc = ELF('./libc.so.6')

context(terminal=['tmux' , 'new-window'])
p = gdb.debug( './heap' ) 
```

or listen with nc or socat
```
# as root or victim
socat TCP-LISTEN:1234,reuseaddr,fork EXEC:./heap

# from attacker:
from pwn import *
p = remote('127.0.0.1', 1234)

# find pid
sudo netstat -pantu | grep 1234

```

Ref:
[https://parzelsec.de/speed-up-your-exploits/](https://parzelsec.de/speed-up-your-exploits/)