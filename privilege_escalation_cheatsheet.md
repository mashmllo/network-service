# Privilege Escalation cheatsheet

## Windows

### Automation scripts
[WinPeas.sh](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite)

### Kernel 
```
systeminfo -> look up missing kb's
systeminfo | findstr /B /C:"OS Name" /C:"OS * Version"`
sherlock -> Find-AllVulns powershell
```
#### Config files 

#### Group policy 
Only applicable for devices connected to a domain
```
Groups.xml`stored in SYSVOL -> DC
  encrypted with AES, but key got leaked
 \\dc2018.lab\SYSVOL\dc2008.lab\Policies\{id}\MACHINE\Preferences\Groups`
```

#### other files 
```
Services\Services.xml
ScheduldedTasks\ScheduledTasks.xml
Printers\Printers.xml
Drives\Drives.xml
DataSources\DataSources.xml
```
#### Other Misc Passwords 
```
dir /s *pass* == *cred* == *vnc* == *.config*
 findstr /si password *.xml *.ini *.txt
reg query HKLM /f password /t REG_SZ /s
reg query HKCU /f password /t REG_SZ /s
web.config
php.ini
httpd.conf
access.log
```

##### Always Install Elvated:
```
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer\AlwaysInstalledElevated 
reg query HKCU\SOFTWARE\Policies\Micorosft\Windows\Installer\AlwaysInstalledElevated
both values = 1, created a malicious .msi file with msfvenom for example
execute it with msiexec /quiet /qn /i <filename>
```
### Unquoted Services Paths (trusted service paths)
each space in a file path, windows will attempt to look for and execute programs with a name that matches the word in front of the space
<br> Example: <br>
* C:\Program Files\Some Folder\Service.exe
* C:\Program.exe
* C:\Program Files\Some.exe
* C:\Program Files\Some Folder\Service.exe
```
Command to look for such service: wmic service get name,displayname,pathname,startmode | findstr /i "Auto" | findstr /i /v "C:\Windows\\" | findstr /i /v """
```
### Insecure Service Permissions
Check write access to a service as authenticated user
```
Commands: 
1. whoami 
2. net user <username>
3. accesschk.exe` -> part of sysinternals
4. accesschk.exe -ucqv <service>
5. accesschk.exe -uwcqv "Authenticated Users" * /accepteula
```

### DLL Hijacking
Requires user interaction / reboot.
<br> Search order: 
```
1. The directory from which the application is loaded
2. 32-bit System directory (C:\Windows\System32)
3. 16-bit System directory (C:\Windows\System)
4. Windows directory (C:\Windows)
5. The current working directory
6. Directories in the PATH environment variable
```
## Linux 

### Automation scripts 
[linpeas.sh](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite) <br>
[linenum.sh](https://github.com/rebootuser/LinEnum)
### Kernel Exploit 
```
Command to look for system kernel version: uname -a
tools used: searchsploit
```
### Services running as root 
any shell escape sequences?
```
Command: ps -aux | grep root 
```
### SUID executables 
runs with permissions of the owner
```
command: find / -perm -u=s -type f 2>/dev/null
```
### Sudo rights 
what can we execute -> any shell escape sequences

### Wildcard 
* often combined with user interaction / cronjobs
* wild cards can be utilized to inject arbitrary command by creating files that are seen as commands
Example: 
```
--checkpoint=<number> and --checkpoint-action=<command>
--checkpoint=1 and --checkpoint-actionexec=sh rshell.sh
```
### PATH abuse 
Requires user interaction

```
* `$PATH:.:${PATH}`
* `export $PATH`
* `echo $PATH`
* replace executable files with a malicious one
```

# References
https://0xsp.com/offensive/privilege-escalation-cheatsheet
https://www.fuzzysecurity.com/tutorials/16.html
https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite
https://github.com/rebootuser/LinEnum
