
## python2 and python3 pwntools
python3 just have the `b'\xde\xad'` isntead of `'\xd\xad'` </br>
```
>>> from pwn import *
>>> p64(u64('\xde\xad\xbe\xef'.ljust(8, '\x00') )) 
'\xde\xad\xbe\xef\x00\x00\x00\x00'

>>> u64('\xde\xad\xbe\xef'.ljust(8, '\x00'))
4022250974

```

