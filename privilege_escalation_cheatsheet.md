# Privilege Escalation cheatsheet

## Windows

### Kernel 

#### Common kernel exploit 

#### Config files 

#### Group policy 

#### other files 

#### Other Misc Passwords 

##### powerup

##### putty 

##### Tight VNC

##### Always Install Elvated:

### Unquoted Services Paths (trusted service paths)

### Insecure Service Permissions

### DLL Hijacking

## Linux 

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
