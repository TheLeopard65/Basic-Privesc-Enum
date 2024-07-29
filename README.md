# BASIC PRIVILEGE ESCALATION ENUMERATION
This document contains a list of useful commands for privilege escalation in Unix-like operating systems. These commands can help identify potential vectors for escalating privileges on a system.

## Spawing a TTY Shell
A TTY (teletypewriter) shell is a command-line interface that allows users to interact with the operating system through a terminal, enabling input and output of text commands and responses in a real-time, interactive session. It simulates the behavior of traditional teletypes, providing a way to execute commands and scripts in a Unix-like environment. There are a total 3 steps to spawning a successful TTY Shell:
- STEP-1: Run the command: `python3 -c 'import pty;pty.spawn("/bin/bash")'` to spawn a better-featured bash shell. At this point, our shell will look a bit prettier, but we still won’t be able to use tab autocomplete or the arrow keys and Ctrl + C will still kill the shell.
- STEP-2: Run the command: `export TERM=xterm` to get access to term commands such as `clear`.
- STEP-3: Background the shell using `Ctrl + Z`.
- STEP-4: Back in our terminal, Run the command: `stty raw -echo; fg`. which does two things: first, it turns off our terminal echo (giving us access to tab autocompletes, the arrow keys, and `Ctrl + C` to kill processes). It then foregrounds the shell, thus completing the process.

## Table of Contents
- [Finding SUID Files](#finding-suid-files)
- [Finding SGID Files](#finding-sgid-files)
- [Checking Sudo Permissions](#checking-sudo-permissions)
- [Finding Writable Files and Directories](#finding-writable-files-and-directories)
- [Finding Executables in PATH](#finding-executables-in-path)
- [Listing Scheduled Cron Jobs](#listing-scheduled-cron-jobs)
- [Checking User and Group Information](#checking-user-and-group-information)

### Finding SUID Files
- Finding the files with SUID (Set User ID) execution permissions of the file Owner which could be used to escalate privileges.
```
find / -perm -4000 2>/dev/null
```

### Finding SGID Files
- Finding the files with SGID (Set Group ID) execution permissions of the file Group which can also be a vector for privilege escalation.
```
find / -perm -2000 2>/dev/null
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
crontab -l && ls /var/spool/cron/crontabs && ls /etc/cron.*
```

### Checking User and Group Information
- Checking user and group details can provide insight into potential privilege escalation opportunities.

```
id
groups
```

## Notes
- Ensure you have proper authorization to run these commands on any system.
- Misuse of these commands can be illegal and unethical.
- Use these commands responsibly and in a controlled environment.

Feel free to modify or expand upon this template based on your needs. If you have any specific commands or sections you’d like to add, let me know!
