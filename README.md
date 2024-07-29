# BASIC PRIVILEGE ESCALATION ENUMERATION

This document contains a list of useful commands for privilege escalation in Unix-like operating systems. These commands can help identify potential vectors for escalating privileges on a system.

## Table of Contents

- [Finding SUID Files](#finding-suid-files)
- [Finding SGID Files](#finding-sgid-files)
- [Checking Sudo Permissions](#checking-sudo-permissions)
- [Finding Writable Files and Directories](#finding-writable-files-and-directories)
- [Finding Executables in PATH](#finding-executables-in-path)
- [Listing Scheduled Cron Jobs](#listing-scheduled-cron-jobs)
- [Checking User and Group Information](#checking-user-and-group-information)

### Finding SUID Files

Finding the files with SUID (Set User ID) execution permissions of the file Owner which could be used to escalate privileges.

```bash
find / -perm -4000 2>/dev/null
```

### Finding SGID Files

Finding the files with SGID (Set Group ID) execution permissions of the file Group which can also be a vector for privilege escalation.

```bash
find / -perm -2000 2>/dev/null
```

### Checking Sudo Permissions

Finding out what SUDO privileges the current has (If any):

```bash
sudo -l
```

### Finding Writable Files and Directories

Identifying files and directories writable by the user can help in various privilege escalation scenarios.

#### Writable Files
```bash
find / -writable -type f 2>/dev/null
```
#### Writable Directories
```bash
find / -writable -type d 2>/dev/null
```


### Finding Executables in PATH
  The first thing to do is use `python3 -c 'import pty;pty.spawn("/bin/bash")'`, which uses Python to spawn a better-featured bash shell. At this point, our shell will look a bit prettier, but we still won’t be able to use tab autocomplete or the arrow keys and Ctrl + C will still kill the shell.
Step two is: `export TERM=xterm` – this will give us access to term commands such as clear.

Finally (and most importantly) we will background the shell using `Ctrl + Z`. Back in our own terminal, we use `stty raw -echo; fg`. This does two things: first, it turns off our terminal echo (which gives us access to tab autocompletes, the arrow keys, and `Ctrl + C` to kill processes). It then foregrounds the shell, thus completing the process.

Finding executables in the PATH environment variable can help identify potentially exploitable scripts or binaries.

```bash
echo $PATH | tr ':' '\n' | xargs -I {} find {} -type f -executable 2>/dev/null
```

### Spawing a TTY Shell
### Listing Scheduled Cron Jobs

Viewing cron jobs can reveal scheduled tasks that may be leveraged for privilege escalation.

```bash
crontab -l && ls /var/spool/cron/crontabs && ls /etc/cron.*
```

### Checking User and Group Information

Checking user and group details can provide insight into potential privilege escalation opportunities.

```bash
id
groups
```

## Notes

- Ensure you have proper authorization to run these commands on any system.
- Misuse of these commands can be illegal and unethical.
- Use these commands responsibly and in a controlled environment.

Feel free to modify or expand upon this template based on your needs. If you have any specific commands or sections you’d like to add, let me know!
