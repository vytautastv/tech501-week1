# 🐧 Linux Commands

## File and Directory Commands
| Command | Description |
|---------|-------------|
| `ls` | List files and directories in the current directory. |
| `ls -l` | List files with details like permissions, size, and modification date. |
| `cd <directory>` | Change to a specific directory. |
| `pwd` | Print the current working directory. |
| `mkdir <directory>` | Create a new directory. |
| `rm <file>` | Delete a file. |
| `rm -r <directory>` | Delete a directory and its contents. |
| `cp <source> <destination>` | Copy files or directories. |
| `mv <source> <destination>` | Move or rename files or directories. |
| `find <path> -name <filename>` | Search for files by name. |

---

## File Operations
| Command | Description |
|---------|-------------|
| `cat <file>` | View the contents of a file. |
| `less <file>` | View file contents page by page. |
| `head <file>` | Show the first 10 lines of a file. |
| `tail <file>` | Show the last 10 lines of a file. |
| `nano <file>` | Open a file in the nano text editor. |
| `touch <file>` | Create an empty file. |
| `chmod <permissions> <file>` | Change file permissions. |
| `chown <user>:<group> <file>` | Change file owner and group. |
| `stat <file>` | Show detailed file information. |

---

## User and Permissions Management
| Command | Description |
|---------|-------------|
| `whoami` | Show the current user. |
| `id` | Display user ID and group ID. |
| `sudo <command>` | Run a command as the superuser. |
| `adduser <username>` | Add a new user. |
| `passwd <username>` | Change a user's password. |
| `usermod -aG <group> <user>` | Add a user to a group. |
| `groups <username>` | Show groups a user belongs to. |

---

## Process Management
| Command | Description |
|---------|-------------|
| `ps aux` | Show all running processes. |
| `top` | Monitor system processes in real-time. |
| `kill <PID>` | Kill a process by PID. |
| `pkill <name>` | Kill processes by name. |
| `kill -9 <PID>` | Forcefully Kill processes by ID |
| `jobs` | List active background jobs. |
| `fg <job>` | Bring a background job to the foreground. |

---

## Networking Commands
| Command | Description |
|---------|-------------|
| `ip a` | Show IP address and network interfaces. |
| `ping <hostname>` | Send ICMP requests to check connectivity. |
| `curl <url>` | Download content from a URL. |
| `scp <source> <user>@<host>:<destination>` | Securely copy files between systems. |
| `ssh <user>@<host>` | Connect to a remote system via SSH. |

---

## System Monitoring
| Command | Description |
|---------|-------------|
| `df -h` | Show disk space usage. |
| `du -sh <directory>` | Show directory size. |
| `free -h` | Display memory usage. |
| `uptime` | Show how long the system has been running. |
| `uname -a` | Show system information. |

---

## Package Management
### **Ubuntu/Debian (apt)**
| Command | Description |
|---------|-------------|
| `sudo apt update` | Update package information. |
| `sudo apt upgrade` | Install available updates. |
| `sudo apt install <package>` | Install a package. |
| `sudo apt remove <package>` | Remove a package. |
| `sudo apt search <keyword>` | Search for a package. |


Parent and Child Processes
	•	Parent Process: The process that spawns or creates another process.
	•	Child Process: A process created by a parent process.

Killing a Parent Process:
  kill -9 <parent_PID>

Killing a Child Process:
  kill -9 <child_PID>

What is a Zombie Process?
A zombie process is a process that has completed execution but remains in the process table because its parent has not read its exit status using the wait() system call.

ps aux | grep 'Z' - identify zombie processes

ps -o ppid,pid,stat,cmd | grep 'Z' - find parent to zombie

kill -9 <parent_PID> - kill parent of zombie
