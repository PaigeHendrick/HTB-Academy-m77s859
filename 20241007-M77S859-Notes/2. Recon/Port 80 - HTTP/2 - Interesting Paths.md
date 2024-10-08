

| Path                                | Response | Value          |
| ----------------------------------- | -------- | -------------- |
| http://10.129.201.192/server-status | 403      | Sensitive Info |
| http://10.129.201.192/admin         | 301      | Admin Info     |
| http://10.129.201.192/data          | 301      |                |
| http://10.129.201.192/backups       | 301      |                |
| http://10.129.201.192/plugins       | 301      |                |

### Files

| Path                                       | Data                                                                                               |
| ------------------------------------------ | -------------------------------------------------------------------------------------------------- |
| http://10.129.201.192/data/users/admin.xml |   <PWD>d033e22ae348aeb5660fc2140aec35850c4da997</PWD><br>  <EMAIL>admin@gettingstarted.com</EMAIL> |



```
┌──(paige㉿PH-LT02)-[~]
└─$ curl -s  http://10.129.201.192/data/users/admin.xml | xmllint --format - 
<?xml version="1.0" encoding="UTF-8"?>
<item>
  <USR>admin</USR>
  <NAME/>
  <PWD>d033e22ae348aeb5660fc2140aec35850c4da997</PWD>
  <EMAIL>admin@gettingstarted.com</EMAIL>
  <HTMLEDITOR>1</HTMLEDITOR>
  <TIMEZONE/>
  <LANG>en_US</LANG>
</item>
```

After using Password Reset using admin, another file was placed on the lan.

```console
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
</item>
```

Password may be encoded hash

Used Chat GPT to Validate and the example was oddly the same hash:

Yes, that's correct! The hash `d033e22ae348aeb5660fc2140aec35850c4da997` is the **SHA-1 hash** of the string **`admin`**.
![[../Images/Pasted image 20241007162537.png]]