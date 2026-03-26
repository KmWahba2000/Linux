# Linux & Bash — Complete Reference Guide

## Table of Contents

### Part I — Bash CLI
1. [File Management](#1-file-management)
2. [Text File Operations](#2-text-file-operations)
3. [Redirection](#3-redirection)
4. [Pipes & Filters](#4-pipes--filters)
5. [Environment Variables](#5-environment-variables)
6. [Bash Prompt (PS1)](#6-bash-prompt-ps1)
7. [Shell Expansions](#7-shell-expansions)

### Part II — Linux System
8. [The Filesystem & Devices](#8-the-filesystem--devices)
9. [User & Group Management](#9-user--group-management)
10. [File Permissions & Ownership](#10-file-permissions--ownership)
11. [Processes](#11-processes)
12. [Job Control](#12-job-control)
13. [Archives & Compression](#13-archives--compression)
14. [Package Management](#14-package-management)
15. [Boot Process & Systemd](#15-boot-process--systemd)
16. [Storage — Partitions, Mounts & LVM](#16-storage--partitions-mounts--lvm)
17. [System Upgrade & Recovery](#17-system-upgrade--recovery)
18. [Cron Jobs](#18-cron-jobs)
19. [Networking](#19-networking)
20. [Firewall (firewalld)](#20-firewall-firewalld)
21. [SELinux](#21-selinux)

### Part III — Bash Scripting
22. [Variables & declare](#22-variables--declare)
23. [Input with read](#23-input-with-read)
24. [Script Structure & Examples](#24-script-structure--examples)
25. [Sourcing & Splitting Scripts](#25-sourcing--splitting-scripts)
26. [Numeric Variables & Arithmetic Operations](#26-numeric-variables--arithmetic-operations)
27. [Tests & the `if` Clause](#27-tests--the-if-clause)
28. [`while` Loops](#28-while-loops)
29. [`for` Loops](#29-for-loops)
30. [Accessing External Data — JSON & APIs](#30-accessing-external-data--json--apis)
31. [Functions](#31-functions)
32. [Arguments In Bash](#32-arguments-in-bash)
33. [Arrays in Bash](#33-arrays-in-bash)
34. [Effective Data Filtering with `grep` & Regular Expressions](#34-effective-data-filtering-with-grep--regular-expressions)
35. [File Handling — Reading, Writing & Parsing](#35-file-handling--reading-writing--parsing)
36. [Error Handling & Debugging](#36-error-handling--debugging)
37. [User Interaction — `select`, Menus & Input](#37-user-interaction--select-menus--input)
38. [Working with Processes, Logs & System Operations](#38-working-with-processes-logs--system-operations)
39. [Automation Tasks — DevOps Patterns](#39-automation-tasks--devops-patterns)
40. [Advanced Bash Features](#40-advanced-bash-features)

---

# Part I — Bash CLI

## 1. File Management

### Output & Printing — `echo`

```bash
echo -e 'Hello\nworld'     # -e: interpret escape sequences (\n = newline)
echo -n 'Hello'            # -n: suppress trailing newline
echo -en 'Hello\nworld'    # combine -e and -n (any order works)
echo -ne 'Hello\nworld'    # same result
```

| Flag | Effect |
|------|--------|
| `-e` | Enable escape sequences (`\n`, `\t`, etc.) |
| `-n` | No trailing newline at the end |
| `-E` | Disable escape sequences (default) |

---

### Navigation

```bash
cd ~/Desktop        # Navigate to Desktop (~ = home directory)
cd tmp_website      # Enter a directory
cd ..               # Go up one level
cd -                # Go back to the previous directory ($OLDPWD)
cd /                # Go to filesystem root
pwd                 # Print current working directory
```

---

### Directory Creation — `mkdir`

```bash
mkdir tmp_website                   # Create a single directory
mkdir styles scripts pages          # Create multiple directories at once
mkdir -p project/src/components     # Create nested directories (no error if exists)
```

---

### File Creation — `touch`

```bash
touch index.html style.css script.js    # Create one or more empty files
touch pages/page1.html                  # Create a file inside a subdirectory
touch ~/Desktop/new/{a..c}.txt          # Brace expansion: creates a.txt, b.txt, c.txt
```

> `touch` also updates the modification timestamp on an existing file without changing its content.

---

### Copying — `cp`

```bash
cp pages/page1.html pages/page2.html    # Copy file to a new name
cp file.txt ~/backup/                   # Copy file to another directory
cp -r folder/ backup/                   # Copy directory recursively
cp -a folder/ backup/                   # Archive copy: preserves permissions, timestamps, symlinks
cp -al Desktop/ restructured            # Copy using hard links (efficient, saves space)
```

---

### Moving & Renaming — `mv`

```bash
mv style.css styles/                    # Move file into a directory
mv script.js scripts/index.js           # Move and rename at the same time
mv pages/page2.html .                   # Move to current directory
mv page2.html index.html                # Rename in place
mv dir1/ dir2/                          # Rename a directory
```

---

### Deleting — `rm` / `rmdir`

```bash
rm index.html pages/page1.html          # Delete one or more files
rm -i file.txt                          # Interactive: ask for confirmation
rm -r tmp_website                       # Delete directory and all contents recursively
rm -rf folder/                          # Force delete without prompts (⚠️ irreversible)
rmdir pages                             # Delete an EMPTY directory only
```

> ⚠️ There is no Recycle Bin in the terminal. `rm` is permanent.

---

## 2. Text File Operations

### Viewing File Content

```bash
cat file.txt                # Print entire file to stdout
cat -n file.txt             # Print with line numbers
cat file1.txt file2.txt     # Concatenate and print multiple files
tac file.txt                # Print file in reverse (last line first)

head file.txt               # First 10 lines (default)
head -n 20 file.txt         # First 20 lines
tail file.txt               # Last 10 lines (default)
tail -n 20 file.txt         # Last 20 lines
tail -f file.txt            # Follow: stream new lines as they are appended (useful for logs)

less -N file.txt            # Open file with line numbers (scrollable pager)
```

**`less` keyboard shortcuts:**

| Key | Action |
|-----|--------|
| `Space` / `f` | Page down |
| `b` | Page back (page up) |
| `G` | Go to end of file |
| `g` | Go to beginning |
| `50p` | Jump to 50% of the file |
| `/text` | Search forward |
| `?text` | Search backward |
| `n` / `N` | Next / previous search match |
| `q` | Quit |

---

### File Statistics

```bash
wc file.txt             # Lines, words, bytes (all three)
wc -l file.txt          # Count lines only
wc -w file.txt          # Count words only
wc -c file.txt          # Count bytes
wc -m file.txt          # Count characters (differs from bytes for multi-byte chars)

du -h file.txt          # Human-readable size of a file
du -sh folder/          # Summary size of a directory
du -sh *                # Size of each item in current directory
ls -lh file.txt         # List file with human-readable size
```

---

## 3. Redirection

### Output — Write, Overwrite, Append

```bash
echo 'hello' > file.txt         # Overwrite file with 'hello' (creates if missing)
ls > file.txt                   # Redirect ls output to file.txt (overwrites)
echo 'more' >> file.txt         # Append 'more' to end of file
ls >> file.txt                  # Append ls output without erasing previous content
```

---

### Error Streams

```bash
du -h img1.jpg img2.jpg > out.txt 2> error.txt    # stdout → out.txt, stderr → error.txt
du -h img1.jpg img2.jpg 2> /dev/null              # Discard all errors
du -h img1.jpg img2.jpg > out.txt 2>&1            # Merge stderr into stdout → both go to out.txt
du -h img1.jpg img2.jpg &> all.txt                # Shorthand: merge both to one file
```

> `/dev/null` is a "black hole" — anything written there is discarded.

---

### Input Redirection

```bash
wc -l < file.txt            # Use file as stdin for wc
cat < file.txt              # Read from file (same as cat file.txt)
cat < in.txt > out.txt      # Redirect input from in.txt and output to out.txt (file copy)
```

---

## 4. Pipes & Filters

A **pipe** (`|`) connects the stdout of one command to the stdin of the next.

### Basic Pipes

```bash
ls | cat                    # Pipe ls output into cat
ls | wc -l                  # Count items in current directory
ls | sort                   # Alphabetically sort directory listing
ls | grep '.txt'            # Filter only .txt files from ls output
```

---

### `tee` — Duplicate Output

`tee` writes to **both** a file and stdout simultaneously.

```bash
echo 'hello world' | tee hello.txt             # Write to file AND print to screen
echo 'hello world' | tee hello.txt | wc -c     # Write to file AND count characters
echo "New log" | tee -a log.txt                # Append to file (instead of overwrite)
ping google.com 2>&1 | tee ping.txt            # Save and display ping results
```

---

### `sort` & `uniq`

```bash
sort users.txt              # Sort lines alphabetically (A–Z)
sort -r users.txt           # Sort in reverse (Z–A)
sort -n numbers.txt         # Numeric sort
sort -k2 users.txt          # Sort by the 2nd field (column)
sort -u users.txt           # Sort and remove duplicate lines

sort users.txt | uniq       # Remove adjacent duplicate lines (must sort first!)
sort users.txt | uniq -d    # Show only duplicate lines
sort users.txt | uniq -c    # Show each line with a count of occurrences
```

---

### `grep` — Search

```bash
grep 'error' file.txt               # Find lines containing 'error'
grep -i 'error' file.txt            # Case-insensitive search
grep -n 'error' file.txt            # Show line numbers
grep -v 'error' file.txt            # Invert match: lines NOT containing 'error'
grep -r 'error' ./logs/             # Recursive search through directory
grep -l 'error' ./logs/             # Print only filenames that match
grep -c 'error' file.txt            # Count matching lines
grep -F 'error' file.txt            # Fixed string match (no regex, faster)

ls | grep -F 'file'                 # Filter ls output
ip addr show | grep -F 'inet'       # Show only IP address lines
ps -ef | grep 'nginx' | grep -v grep   # Find process, exclude grep itself
```

---

### `tr` — Translate / Transform Characters

```bash
echo 'bash' | tr 'b' 'd'            # Replace 'b' with 'd' → dash
echo 'awesome' | tr 'a-z' 'A-Z'     # Convert to uppercase → AWESOME
echo 'hello   world' | tr -s ' '    # Squeeze multiple spaces to one
echo 'hello world' | tr -d ' '      # Delete all spaces → helloworld
echo 'hello' | tr 'a-z' 'A-Z'       # Uppercase translation
```

---

### `rev` — Reverse a String

```bash
echo 'Was it a cat I saw?' | rev    # → ?was I tac a ti saW
```

---

### `cut` — Extract Fields or Characters

```bash
uptime | cut -b 1-10        # First 10 bytes
uptime | cut -c 1-10        # First 10 characters
uptime | cut -c 2           # Only the 2nd character
uptime | cut -c 2-          # From 2nd character to end of line
uptime | cut -d ' ' -f1     # First word, using space as delimiter

cut -d: -f1 /etc/passwd     # Extract usernames (first field, colon-delimited)
cut -d: -f1,3 /etc/passwd   # Extract fields 1 and 3
```

> Use `-b` for bytes, `-c` for characters (matters for multi-byte encodings), `-f` for fields with a delimiter (`-d`).

---

### `sed` — Stream Editor

```bash
sed 's/old/new/' file.txt               # Replace first occurrence on each line (print to stdout, file unchanged)
sed 's/old/new/g' file.txt              # Replace ALL occurrences per line (global)
sed -i 's/old/new/g' file.txt           # In-place edit: modify the original file directly
sed 's/old/new/g' file.txt > new.txt    # Replace and save to a new file
sed -n '5,10p' file.txt                 # Print only lines 5 through 10
sed '/pattern/d' file.txt               # Delete lines matching pattern

echo 'hello world' | sed 's/world/bash/g'   # → hello bash
```

**`sed` substitution syntax:**  `s/search/replace/flags`

| Part | Meaning |
|------|---------|
| `s` | Substitute command |
| `old` | Pattern to find |
| `new` | Replacement text |
| `g` | Global: replace all matches on each line |
| `i` | Case-insensitive match |
| `-i` | Edit file in place (use with caution) |

---

## 5. Environment Variables

### Viewing Variables

```bash
env                         # List ALL environment variables
printenv HOME               # Print a single variable
echo "${USER}"              # Current logged-in username
echo "${HOME}"              # Home directory path
echo "${PWD}"               # Current working directory
echo "${OLDPWD}"            # Previous working directory
echo "${PATH}"              # Directories searched for commands
echo "${SHELL}"             # Path to the current shell
echo "${HOSTNAME}"          # Machine hostname
```

> ⚠️ `$USER` is the correct variable for the current username. `$USERS` is not a standard variable.

---

### Setting & Removing Variables

```bash
MY_VAR='hello'              # Assign shell variable (local to current shell)
export MY_VAR='hello'       # Export as environment variable (available to child processes)
export CITY='Cairo'         # Create and export in one step
CITY='Alexandria'           # Update value (already exported, stays exported)
unset CITY                  # Remove variable entirely
```

**Inline (one-command) assignment:**
```bash
MY_VAR='value' python3 script.py    # Set variable only for that one command
```

---

### PATH Management

```bash
echo "${PATH}"                          # View current PATH
PATH="${PATH}:/home/karim/bin"          # Append custom directory to PATH (current session only)
which cat                               # Show full path of a command (e.g. /usr/bin/cat)
type ls                                 # Show whether command is built-in or external
```

---

### Shell Configuration Files

| File | When it's loaded |
|------|-----------------|
| `~/.bashrc` | Every interactive non-login shell (most common for customizations) |
| `~/.bash_profile` | Login shells (terminal login, SSH) |
| `~/.profile` | Login shells (POSIX-compatible, used if `.bash_profile` absent) |
| `/etc/environment` | System-wide environment variables (not a shell script) |
| `/etc/profile` | System-wide login shell config |

```bash
nano ~/.bashrc              # Edit your personal shell config
source ~/.bashrc            # Reload without reopening terminal
. ~/.bashrc                 # Shorthand for source

# Example additions to ~/.bashrc:
export TOP_SECRET_TOKEN='secret'
PATH="${PATH}:/home/karim/bin"
alias gohome='cd ~'
```

---

### Aliases

```bash
alias gohome='cd ~'             # Create a temporary alias (no spaces around =)
alias ll='ls -alh'              # Shortcut for a long listing
alias ..='cd ..'                # Navigate up one level quickly

# Check what an alias expands to:
alias gohome

# Remove an alias (current session only):
unalias gohome

# Make permanent: add alias lines to ~/.bashrc, then:
source ~/.bashrc
```

---

### Shell Options

```bash
set -x              # Enable debug/trace mode — shows each command before executing
set +x              # Disable debug mode
set -e              # Exit immediately if any command fails
set +e              # Disable exit-on-error

shopt -s autocd     # Type a directory name to cd into it (no need to type 'cd')
shopt -u autocd     # Disable autocd
shopt -s cdspell    # Auto-correct small typos in directory names
shopt -u cdspell    # Disable typo correction

# Change default shell (takes effect on next login):
chsh -s "/bin/bash"
cat /etc/shells     # View all available shells installed on the system
```

---

## 6. Bash Prompt (PS1)

### Escape Sequences

```bash
PS1='\u@\h:\w\$ '       # user@short-hostname:/full/path$
```

| Sequence | Expands to |
|----------|------------|
| `\u` | Current username |
| `\h` | Short hostname |
| `\H` | Full hostname |
| `\w` | Full current working directory path |
| `\W` | Basename of current directory |
| `\t` | Current time (HH:MM:SS) |
| `\@` | Current time (AM/PM) |
| `\d` | Date (e.g. Mon Mar 21) |
| `\$` | `$` for regular user, `#` for root |
| `\n` | Newline |

**Examples:**
```bash
PS1='samora$ '
PS1='\u@\H$ '
PS1='\u@\H (\w:\W)$ '
PS1='\u@\H (\w:\W), (\t \@)$ '
PS1='\u@\h:\w$ '
```

---

### ANSI Color Codes

```bash
echo -e "\e[31m Red text \e[0m"         # Red foreground
echo -e "\e[32m Green text \e[0m"       # Green foreground
echo -e "\e[36;41m Cyan on red \e[0m"   # Cyan text, red background
echo -e "\e[1m Bold \e[0m"              # Bold text
echo -e "\e[0m"                          # Reset all formatting
```

**Common color codes** (use after `\e[`):

| Code | Effect |
|------|--------|
| `0m` | Reset |
| `1m` | Bold |
| `4m` | Underline |
| `30–37m` | Foreground colors (30=black … 37=white) |
| `40–47m` | Background colors |
| `90–97m` | Bright foreground colors |

---

### `tput` Styling

```bash
tput bold               # Enable bold
tput smul               # Enable underline
tput sgr0               # Reset all styles
tput setaf 2            # Set foreground color (2 = green)
tput setab 3            # Set background color (3 = yellow)
tput setaf 255          # White (on 256-color terminals)
tput cup 5 20           # Move cursor to row 5, column 20
tput lines              # Number of rows in terminal
tput cols               # Number of columns in terminal
infocmp                 # View all terminal capabilities
```

**Styled output example:**
```bash
tput bold; tput setaf 2; echo 'hello world'; tput sgr0
echo "$(tput bold)$(tput setab 3)$(tput setaf 255)TITLE$(tput sgr0)"
```

---

### Safe Colored PS1 (Correct Wrapping)

Wrap non-printing sequences in `\[` and `\]` to prevent cursor/wrapping bugs:

```bash
# ✅ Correct — Bash knows these sequences are invisible:
PS1="\[$(tput bold)\]\[$(tput setaf 6)\]→\[$(tput setaf 2)\] \W\[$(tput sgr0)\]\[$(tput setaf 3)\]\[$(tput bold)\]\$\[$(tput sgr0)\] "

# ❌ Wrong — no \[ \] wrapping → causes line wrapping and cursor alignment bugs:
PS1="$(tput bold)$(tput setaf 6)→$(tput setaf 2) \W$(tput sgr0)$(tput setaf 3)$(tput bold)\$$(tput sgr0) "
```

**To make it permanent:**
```bash
echo 'PS1="\[$(tput bold)\]\[$(tput setaf 2)\]\u@\h:\[$(tput setaf 6)\]\w\[$(tput setaf 2)\]\$ \[$(tput sgr0)\]"' >> ~/.bashrc
source ~/.bashrc
```

---

## 7. Shell Expansions

### Globbing & Path Expansion

```bash
ls *                            # All files/directories in current directory
echo *.txt                      # All .txt files
echo ?.txt                      # Single-character name + .txt (e.g. a.txt)
ls /home/karim/Documents/*.txt  # All .txt files in Documents
ls *[0-9]*                      # Files with a digit in their name

echo ~                          # Home directory (same as $HOME)
echo ~+                         # Current working directory (same as $PWD)
echo $PWD/*.txt                 # Absolute paths of .txt files in current dir
echo '$PWD/*.txt'               # Single quotes → literal string, no expansion
echo "$PWD/*.txt"               # Double quotes → $PWD expands, glob does NOT
```

---

### Variable Expansion

```bash
echo "${HOME}"                  # Value of HOME
echo "${#HOME}"                 # Length of HOME (character count)
echo "${HOME:1:10}"             # Substring: 10 characters starting at position 1
echo "${HOME//home/Users}"      # Replace all 'home' with 'Users'
echo "${HOME/#\/home/\/Users}"  # Replace prefix
echo "${VAR:-default}"          # Use 'default' if VAR is unset or empty
echo "${VAR:=default}"          # Assign 'default' to VAR if unset, then use it
```

---

### Quoting

```bash
echo "\""                       # Output: "
echo '"'                        # Output: "
echo a\ folder/                 # Escaped space — becomes 'a folder/'
echo '123'"'"'456'              # Embed single quote in string → 123'456
touch ./-al                     # Safer way to create a file starting with '-'
```

---

### Brace Expansion

```bash
echo data.{csv,txt}             # → data.csv data.txt
echo {1..5}                     # → 1 2 3 4 5
echo {a..e}                     # → a b c d e
touch {a..z}.txt                # Create 26 files: a.txt through z.txt
mkdir project/{src,tests,docs}  # Create multiple subdirectories at once
```

---

### Command Substitution

```bash
echo "Directory: $(pwd)"            # Embed command output into a string
files="$(ls ~)"                     # Store command output in a variable
count="$(ls | wc -l)"               # Store count of directory items
echo "$(date)"                      # Prints current date/time inline
```

---

### Process Substitution

Process substitution creates a temporary file-like descriptor from command output.

```bash
diff <(ls ./folder1) <(ls ./folder2)    # Compare directory listings without temp files
wc -l < <(ls)                           # Count lines of ls output (variables stay in scope)
echo "test" > >(cat)                    # Redirect output through cat via process substitution

# vs pipe — key difference:
ls | wc -l          # Works but runs in a subshell
wc -l < <(ls)       # Same result, but preserves shell variable scope
```

---

# Part II — Linux System

## 8. The Filesystem & Devices

### Symbolic & Hard Links

```bash
# Symbolic (soft) link — pointer to a path:
ln -s Desktop/ abc                          # Create symlink 'abc' pointing to Desktop/
ln -s /home/karim/myfile.txt /tmp/link.txt  # Absolute path symlink
readlink -f ./abc                           # Show the real absolute path a symlink points to

# Hard link — second name for the same inode (same file data):
ln file.txt hardlink.txt                    # Both names point to the same data on disk
```

**Differences:**

| | Symlink | Hard link |
|--|---------|-----------|
| Spans filesystems? | ✅ Yes | ❌ No |
| Works on directories? | ✅ Yes | ❌ No (usually) |
| Broken if target deleted? | ✅ Yes (dangling) | ❌ No (data persists) |
| Shows with `ls -l` | `l` type + `->` path | Regular file, `nlink` > 1 |

---

### Disk Usage & Inodes

```bash
df -h               # Disk space usage (human-readable)
df -Th              # Disk space with filesystem type
df -ih              # Inode usage (human-readable)

du -h file.txt      # Size of a single file
du -sh folder/      # Summary size of a directory
du -sh *            # Size of each item in current directory
```

---

### /proc Virtual Filesystem

`/proc` is not a real disk directory — it's a live window into the running kernel.

```bash
ls /proc                    # List all virtual kernel/process files
cat /proc/cpuinfo           # CPU model, cores, flags
cat /proc/meminfo           # RAM usage breakdown
cat /proc/uptime            # Uptime and idle time (in seconds)
cat /proc/loadavg           # Load averages (1, 5, 15 min)
cat /proc/version           # Kernel version and build info
cat /proc/mounts            # All currently mounted filesystems
cat /proc/cmdline           # Kernel boot command line
cat /proc/<PID>/status      # Status info for a specific process
```

---

### Device Files

```bash
tty                         # Show current terminal device (e.g. /dev/pts/0)
ls -l /dev                  # List all device files
ls -l /dev/sda              # Info about the sda disk device
lsblk                       # List block devices (disks, partitions)
lsblk -f                    # Include filesystem type and UUID

ping google.com > /dev/null             # Discard all output
cat /dev/urandom > ~/random.bin         # Non-blocking random data (fast)
cat /dev/zero | head -c 1M > zero.bin   # Write 1 MB of zeros to a file
```

---

### Root Directory Structure (`/`)

| Directory | Purpose |
|-----------|---------|
| `/bin` | Essential user binaries (`ls`, `cp`, `bash`). In modern systems, symlink to `/usr/bin` |
| `/sbin` | System admin binaries (`mount`, `shutdown`). Symlink to `/usr/sbin` on modern systems |
| `/boot` | Kernel images, initrd, GRUB bootloader files |
| `/dev` | Device files (not real files — interfaces to hardware) |
| `/etc` | System-wide configuration files |
| `/home` | User home directories (`/home/karim`) |
| `/lib` / `/lib64` | Shared libraries for `/bin` and `/sbin` |
| `/media` | Auto-mount point for removable media (USB, CDs) |
| `/mnt` | Temporary manual mount point |
| `/opt` | Optional third-party software packages |
| `/proc` | Virtual filesystem: kernel and process information |
| `/root` | Home directory of the root superuser |
| `/run` | Volatile runtime data (PID files, sockets); cleared on boot |
| `/snap` | Snap package data (Ubuntu) |
| `/srv` | Data served by the system (web, FTP) |
| `/sys` | Virtual filesystem: hardware/kernel interfaces |
| `/tmp` | Temporary files, cleared on reboot |
| `/usr` | Secondary hierarchy: user utilities, libraries, documentation |
| `/var` | Variable data: logs (`/var/log`), spool, cache |
| `/swap.img` | Swap file (virtual memory when RAM is full) |

> 📌 On modern Ubuntu/Debian, `/bin`, `/lib`, and `/sbin` are symlinks into `/usr/` (the "usrmerge").

---

## 9. User & Group Management

### Creating Users — `useradd`

```bash
sudo useradd -m samora                              # Create user with home directory (/home/samora)
sudo useradd -d /custom/home samora                 # Set a custom home directory path
sudo useradd -s /bin/bash samora                    # Set the login shell
sudo useradd -g users samora                        # Set primary group
sudo useradd -G lpadmin,admin samora                # Add to supplementary groups at creation
sudo useradd -c "Full Name" samora                  # Set display name (GECOS field)

# Full example with all options:
sudo useradd -m -d /home/samar -s /bin/bash -c "Samar Ayman" -G sudo,users,lpadmin samar

sudo passwd samar           # Set (or change) password for user 'samar'
```

> ⚠️ The `-aG` flag is for `usermod`, not `useradd`. Use `-G` with `useradd` to set groups at creation.

---

### Viewing User Information

```bash
cat /etc/passwd             # All user accounts (username:x:UID:GID:name:home:shell)
cat /etc/shadow             # Encrypted passwords — root only
cat /etc/group              # All groups and their members

id samar                    # UID, GID, and all group memberships for 'samar'
groups samar                # List groups user 'samar' belongs to
getent passwd               # All user accounts (includes LDAP/NIS entries)
getent group                # All groups

cut -d: -f1 /etc/passwd     # List all usernames
cut -d: -f1 /etc/group      # List all group names

passwd -S samar             # Short password status for 'samar'
chage -l samar              # Detailed password aging info for 'samar'
```

---

### Modifying Users — `usermod`

```bash
sudo usermod -l wahba samar                     # Rename user from 'samar' to 'wahba'
sudo mv /home/samar /home/wahba                 # Rename the home directory
sudo usermod -d /home/wahba -m wahba            # Update home dir path and move contents
sudo usermod -c "Wahba Mostafa" wahba           # Update full display name
sudo usermod -s /bin/bash wahba                 # Change default shell
sudo usermod -G sudo,lpadmin wahba              # Replace supplementary groups
sudo usermod -aG docker,admin wahba             # APPEND to supplementary groups (safe: -a preserves existing)
chsh -s /bin/zsh wahba                          # Change login shell (can be run by user themselves)
```

---

### Locking & Unlocking Accounts

```bash
sudo usermod -L ahmed       # Lock account (prepends ! to password hash)
sudo usermod -U ahmed       # Unlock account

# Alternative using passwd:
sudo passwd -l ahmed        # Lock account
sudo passwd -u ahmed        # Unlock account
```

---

### Deleting Users — `userdel`

```bash
sudo userdel wahba          # Delete user (home directory is NOT removed)
sudo userdel -r wahba       # Delete user AND remove home directory
sudo userdel -f wahba       # Force delete even if user is currently logged in
```

---

### Managing Groups

```bash
sudo groupadd -g 2500 developers        # Create group with a specific GID
sudo groupmod -n devteam developers     # Rename group
sudo groupmod -g 5000 devteam          # Change GID
sudo groupdel devteam                   # Delete group

# Ubuntu/Debian helper commands:
sudo adduser samar adm                  # Add user to group (high-level)
sudo deluser karim sudo                 # Remove user from group

# Universal approach:
sudo usermod -aG audio,video karim      # Add user to multiple groups
sudo gpasswd -d karim wheel             # Remove from group (works everywhere)

# Hostname:
sudo hostnamectl set-hostname my-host   # Change hostname
sudo nano /etc/hosts                    # Add: 127.0.1.1    my-host
```

---

### Password Aging Policy — `chage`

```bash
sudo chage -l samar                         # Show password aging details
sudo chage -m 7 -M 90 samar                 # Min 7 days, max 90 days before change required
sudo chage -W 14 samar                      # Warn 14 days before password expires
sudo chage -E 2026-12-31 samar             # Account expires on this date
sudo passwd -n 0 -x 99999 samar            # Remove aging restrictions (never expires)
```

---

### Login Monitoring

```bash
who             # Who is currently logged in
w               # Who is logged in + what they're doing
users           # List logged-in usernames
last            # Recent login history from /var/log/wtmp
last ahmed      # Login history for a specific user
sudo lastb      # Failed login attempts

ps -u ahmed     # Processes belonging to user 'ahmed'
top -u ahmed    # Monitor only ahmed's processes in top

# Log files:
cat /var/log/auth.log    # Login events, sudo usage (Ubuntu)
cat /var/log/secure      # Same — login/sudo events (CentOS)
cat /var/log/syslog      # General system activity
```

---

### Root Access & `sudo`

```bash
sudo passwd root                    # Set a password for the root account
su                                  # Switch to root shell (requires root password)
sudo -s                             # Open a root shell using YOUR sudo password
sudo -u samora touch /home/samora/f # Run a command as a different user
sudo -u samora -s                   # Open a shell as another user
sudo -k                             # Invalidate cached sudo credentials
```

**Sudoers file** (`sudo visudo` — always use this, never edit directly):
```
# Grant full sudo to a user:
samora ALL=(ALL:ALL) ALL

# Grant full sudo to all members of a group:
%developers ALL=(ALL:ALL) ALL

# Allow a user to run sudo without a password:
karim ALL=(ALL:ALL) NOPASSWD: ALL

# Allow only a specific command without password:
karim ALL=(ALL:ALL) NOPASSWD: /usr/bin/apt-get
```

---

## 10. File Permissions & Ownership

### Understanding Permission Notation

```bash
ls -al      # Long listing showing permissions
```

```
-rwxr-xr--  1 karim developers 4096 Mar 21 12:00 script.sh
│├──┤├──┤├──┤
││  │  │  └── Others: r-- (read only)
││  │  └───── Group: r-x (read + execute)
││  └──────── Owner: rwx (read + write + execute)
│└─────────── File type: - (file), d (dir), l (symlink)
```

---

### `chmod` — Change Permissions

**Symbolic mode:**
```bash
chmod u+x file.txt          # Add execute for owner (user)
chmod g-w file.txt          # Remove write for group
chmod o+r file.txt          # Add read for others
chmod a+x file.txt          # Add execute for ALL (user, group, others)
chmod u+x,g-w file.txt      # Multiple changes at once
chmod -R 755 folder/        # Recursively set permissions
```

**Numeric (octal) mode:**
```bash
chmod 755 file.txt          # rwxr-xr-x (owner all, group/others r+x)
chmod 644 file.txt          # rw-r--r-- (owner r+w, group/others r)
chmod 600 file.txt          # rw------- (owner only r+w; good for private keys)
chmod 777 file.txt          # rwxrwxrwx (full for everyone — ⚠️ use sparingly)
chmod 000 file.txt          # No permissions for anyone
```

| Number | Permissions |
|--------|------------|
| `0` | `---` none |
| `1` | `--x` execute |
| `2` | `-w-` write |
| `3` | `-wx` write + execute |
| `4` | `r--` read |
| `5` | `r-x` read + execute |
| `6` | `rw-` read + write |
| `7` | `rwx` read + write + execute |

---

### `chown` — Change Ownership

```bash
chown karim file.txt                # Change owner to karim
chown karim:developers file.txt     # Change owner AND group
chown :developers file.txt          # Change group only
chown -R karim:developers folder/   # Recursive ownership change
```

---

### `umask` — Default Permission Mask

```bash
umask                   # Show current umask (e.g. 0022)
umask 022               # Set umask: new files = 644, new dirs = 755
umask 027               # More restrictive: new files = 640, new dirs = 750
sudo nano /etc/login.defs   # System-wide umask setting (look for UMASK)
```

> umask subtracts permissions from the default (666 for files, 777 for dirs).  
> umask `022` → `666 - 022 = 644` for files.

---

### Sticky Bit

Prevents users from deleting files they don't own, even in a world-writable directory.

```bash
chmod +t /data              # Set sticky bit
chmod -t /data              # Remove sticky bit
chmod 1777 /data            # Full permissions + sticky bit (like /tmp)
ls -ld /tmp                 # Shows 't' in the others execute position: drwxrwxrwt
```

---

### SetUID & SetGID

**SetUID** — file runs with the **owner's** privileges (not the caller's):
```bash
sudo chown root:root myexec
sudo chmod u+s myexec           # or: chmod 4755 myexec
sudo chmod u-s myexec           # Remove SetUID
ls -l myexec
# → -rwsr-xr-x  (s in user position = SetUID active)
```

> Example: `/usr/bin/passwd` uses SetUID to allow users to change their own password (needs root to write `/etc/shadow`).

**SetGID** — new files in the directory **inherit** the directory's group:
```bash
sudo groupadd developers
sudo mkdir /shared
sudo chown root:developers /shared
sudo chmod 2775 /shared             # or: chmod g+s /shared
ls -ld /shared
# → drwxr-sr-x  (s in group position = SetGID active)
```

> Useful for team collaboration directories where everyone needs to share file ownership.

---

## 11. Processes

### Viewing Processes — `ps`

```bash
ps                          # Processes in the current terminal session only
ps -e                       # ALL processes (short format)
ps -ef                      # All processes, full detail (UID, PID, PPID, TTY, CMD)
ps aux                      # BSD style: includes %CPU and %MEM
ps -ef --forest             # Parent/child tree view (great for debugging)
ps -lf                      # Long format with flags
ps -p 1234 -f               # Details for a specific PID
ps -eo pid,comm,%cpu,nice --sort=-%cpu   # Custom columns, sorted by CPU

# Filter:
ps -ef | grep 'nginx' | grep -v grep    # Find nginx processes (exclude grep itself)
pgrep nginx                             # PIDs by name (cleaner)
pgrep -f firefox                        # Match against full command line
```

**Key columns in `ps -ef`:**

| Column | Meaning |
|--------|---------|
| `UID` | User owning the process |
| `PID` | Process ID |
| `PPID` | Parent process ID |
| `C` | CPU utilization |
| `STIME` | Start time |
| `TTY` | Terminal (`pts/N` for SSH, `?` for daemons) |
| `CMD` | Full command with arguments |

---

### Real-time Monitoring — `top`

```bash
top                             # Interactive monitor (sorted by CPU by default)
sudo top -u karim -d 1 -i -c    # Filter by user, 1s refresh, hide idle, show full commands
htop                            # Enhanced top (install: sudo apt install htop)
```

**`top` keyboard shortcuts:**

| Key | Action |
|-----|--------|
| `k` | Kill a process (enter PID) |
| `r` | Renice (change priority) |
| `f` | Add/remove columns |
| `z` | Toggle color mode |
| `Shift+Z` | Change color scheme |
| `T` | Sort by time |
| `H` | Show threads |
| `Shift+W` | Save configuration |
| `q` | Quit |

---

### Process Priority — `nice` / `renice`

Nice values range from **-20** (highest priority) to **19** (lowest). Default is 0.

```bash
nice -n 10 gedit                        # Start gedit with nice = 10 (lower priority)
nice -n -5 ./important_task.sh          # Higher priority (needs sudo for negative values)
sudo renice -n 10 -p 1234              # Change running process PID 1234 to nice = 10
sudo renice -n 19 $(pgrep firefox)     # Deprioritize all Firefox processes

cat /proc/49255/status | grep ctxt     # View voluntary/involuntary context switches
watch -n 5 grep ctxt /proc/49255/status  # Live monitoring every 5 seconds
```

---

### Signals

```bash
kill -l                         # List all signal names and numbers
kill -15 1234                   # Send SIGTERM (graceful termination request)
kill -9 1234                    # Send SIGKILL (force kill, no cleanup)
kill -s SIGTERM 1234            # Named signal format

pkill -SIGTERM firefox          # Kill by process name
pkill -15 firefox               # Same using signal number
killall firefox                 # Kill all processes named 'firefox' with SIGTERM
killall -SIGKILL firefox        # Force kill all firefox processes
```

**Common signals:**

| Signal | Number | Meaning |
|--------|--------|---------|
| `SIGHUP` | 1 | Hangup — reload config / graceful restart |
| `SIGINT` | 2 | Interrupt (same as Ctrl+C) |
| `SIGQUIT` | 3 | Quit with core dump |
| `SIGKILL` | 9 | Force kill — cannot be caught or ignored |
| `SIGTERM` | 15 | Graceful termination request (default `kill`) |
| `SIGCONT` | 18 | Continue a stopped process |
| `SIGSTOP` | 19 | Pause a process — cannot be caught or ignored |

---

### Pausing & Resuming Processes

```bash
kill -SIGSTOP 1234      # Pause a running process
kill -SIGCONT 1234      # Resume a paused process
Ctrl+Z                  # Suspend the foreground process (sends SIGTSTP)
fg                      # Resume suspended process in foreground
bg                      # Resume suspended process in background

nohup firefox &                      # Run in background, immune to terminal close
nohup firefox > /dev/null 2>&1 &     # Same, but discard all output
ps -elf | grep ' Z '                 # Find zombie processes (state Z)
```

---

## 12. Job Control

### Background & Foreground

```bash
ping -c 20 google.com &         # Run in background (still tied to terminal)
ls &                            # Background job (short task)
jobs                            # List all background jobs and their status
jobs -l                         # Include PIDs

fg                              # Bring last background job to foreground
fg %1                           # Bring job number 1 to foreground
bg %1                           # Resume stopped job 1 in background
kill %2                         # Kill job number 2

Ctrl+Z                          # Suspend the current foreground process
Ctrl+C                          # Terminate the current foreground process
```

---

### `nohup` — Survive Terminal Close

```bash
nohup firefox &                          # Background, immune to hangup
nohup ping -c 100 google.com &           # Runs even after terminal closes
nohup bash longtask.sh > out.txt 2>&1 &  # Redirect all output to a file
```

| Method | Background? | Survives terminal close? | Ctrl+C kills it? |
|--------|-------------|--------------------------|-------------------|
| `cmd &` | ✅ | ❌ | ❌ |
| `nohup cmd` | ❌ | ✅ | ✅ |
| `nohup cmd &` | ✅ | ✅ | ❌ |

---

### Terminal Settings — `stty`

```bash
stty                    # Show current settings (abbreviated)
stty -a                 # Show all settings in detail
stty sane               # Reset terminal to default sane state
stty -echo              # Disable input echo (invisible typing — useful for passwords)
stty echo               # Re-enable input echo
stty raw                # Raw mode (no line buffering)
stty cooked             # Return to normal canonical mode
stty intr ^C            # Set Ctrl+C as the interrupt key
stty tostop             # Stop background jobs that try to write to terminal
stty -tostop            # Allow background jobs to write to terminal
```

---

### Waiting for Jobs — `wait`

```bash
wait                    # Wait for ALL background jobs to complete
wait 1234               # Wait for process PID 1234
wait %1                 # Wait for job number 1
wait -n                 # Wait for the NEXT background job to finish

# Example: run three parallel jobs, then notify when all done
ping -c 30 google.com > /dev/null &
ping -c 30 8.8.8.8 > /dev/null &
ping -c 30 1.1.1.1 > /dev/null &
wait; tput bel; echo "All pings completed."
```

---

## 13. Archives & Compression

### `tar`

```bash
# Extract:
tar -xvzf file.tar.gz       # Extract .tar.gz
tar -xvzf file.tgz          # Extract .tgz (same format)
tar -xvjf file.tar.bz2      # Extract .tar.bz2
tar -xvJf file.tar.xz       # Extract .tar.xz
tar -xvf  file.tar          # Extract plain .tar

# Create:
tar -cvzf archive.tar.gz folder/    # Create .tar.gz from a folder
tar -cvjf archive.tar.bz2 folder/   # Create .tar.bz2

# List contents without extracting:
tar -tvzf file.tar.gz

# Flags reference:
# -x = extract, -c = create, -t = list
# -v = verbose, -f = filename follows
# -z = gzip, -j = bzip2, -J = xz
```

### Other Archive Formats

```bash
# Install tools:
sudo apt install unzip p7zip-full unrar    # Ubuntu
sudo dnf install unzip p7zip unrar         # CentOS

# Extract:
unzip file.zip              # Extract .zip
7z x file.7z               # Extract .7z
unrar x file.rar            # Extract .rar

# Create .zip:
zip -r archive.zip folder/
```

---

## 14. Package Management

### APT — Ubuntu / Debian

```bash
sudo apt update                             # Refresh package index
sudo apt upgrade                            # Upgrade installed packages
sudo apt full-upgrade                       # Upgrade + handle dependency changes
sudo apt install nginx                      # Install a package
sudo apt remove nginx                       # Remove package (keep config files)
sudo apt purge nginx                        # Remove package AND config files
sudo apt autoremove                         # Remove unneeded dependencies
sudo apt search nginx                       # Search for packages
apt show nginx                              # Show package details

sudo apt install -f                         # Fix broken dependencies
sudo apt-mark hold linux-image-6.8.0-31-generic    # Prevent a package from being upgraded
sudo apt-mark unhold linux-image-6.8.0-31-generic  # Allow upgrades again
sudo apt-mark manual linux-image-6.8.0-31-generic  # Protect from autoremove

dpkg -i package.deb                         # Manually install a .deb file
dpkg -r package-name                        # Remove a package
dpkg --list | grep nginx                    # List installed packages matching 'nginx'
dpkg -S /usr/bin/ls                         # Which package owns this file

# Integrity check:
debsums bash                # Check integrity of bash package files
debsums -c                  # Show only changed/modified files

# Package sources:
nano /etc/apt/sources.list.d/ubuntu.sources
add-apt-repository ppa:andykimpe/php        # Add a PPA
add-apt-repository --remove ppa:andykimpe/php
```

---

### DNF — CentOS / RHEL

```bash
sudo dnf upgrade --refresh                  # Refresh and upgrade all packages
sudo dnf install nginx                      # Install a package
sudo dnf remove nginx                       # Remove a package
sudo dnf autoremove                         # Remove unneeded dependencies
sudo dnf search nginx                       # Search
dnf info nginx                             # Package details
sudo dnf check                             # Check for problems

rpm -ivh package.rpm                        # Manually install .rpm
rpm -e package-name                         # Remove a package
rpm -qa | grep nginx                        # List installed packages
rpm -qf /usr/bin/ls                         # Which package owns this file
rpm -Va                                     # Verify all installed packages
rpm -V bash                                 # Verify specific package
rpm -K package.rpm                          # Check .rpm signature

# Repository management:
nano /etc/yum.repos.d/CentOS-Base.repo
sudo dnf config-manager --add-repo=https://example.repo/php.repo
sudo dnf config-manager --disable example-repo

sudo dnf mark install kernel-core-6.8.0-31.el9.x86_64   # Protect kernel from autoremove
sudo dnf versionlock add kernel                           # Lock kernel version
sudo dnf versionlock delete kernel                        # Unlock
```

---

### Snap Packages

```bash
# Ubuntu (snapd built-in):
snap install gimp               # Install GIMP via snap
snap list                       # List installed snap packages
snap remove gimp                # Remove snap package
ls /snap/                       # Mounted snap packages (read-only)

# CentOS (install snapd first):
sudo dnf install snapd
sudo systemctl enable --now snapd.socket
sudo ln -s /var/lib/snapd/snap /snap
snap install gimp
```

---

### System Info

```bash
lsb_release -a              # Ubuntu version details
cat /etc/redhat-release     # CentOS/RHEL version
uname -a                    # Full kernel and OS info
uname -r                    # Kernel version only
hostnamectl                 # System info including OS, hostname, kernel

# Localization:
dpkg-reconfigure locales            # Reconfigure locale settings (Ubuntu)
localedef -i ar_EG -f UTF-8 ar_EG.UTF-8   # Generate locale (CentOS)
timedatectl set-timezone Africa/Cairo      # Set timezone
date +%d/%m/%Y              # Date in day/month/year format
LC_ALL=ar_EG.UTF-8 date +%A # Day of week in Arabic
```

---

## 15. Boot Process & Systemd

### GRUB Configuration

```bash
nano /etc/default/grub          # Edit GRUB settings
# Key options:
# GRUB_TIMEOUT=5                → show menu for 5 seconds
# GRUB_TIMEOUT_STYLE=menu       → always show menu (comment out 'hidden')
# GRUB_DEFAULT=0                → default menu entry (0 = first)

# Apply changes:
sudo update-grub                            # Ubuntu
sudo grub2-mkconfig -o /boot/grub2/grub.cfg  # CentOS

sudo reboot
```

---

### Kernel Info & Modules

```bash
uname -r                    # Current running kernel version
uname -a                    # All kernel info (name, version, arch, etc.)
cat /proc/version           # Kernel version and build timestamp
ls /boot                    # Kernel images, initrd, grub files
dmesg | less                # Kernel boot messages

lsmod                       # List loaded kernel modules
modinfo bluetooth           # Info about a specific module
modprobe bluetooth          # Load a module
modprobe -r bluetooth       # Unload a module

cd /lib/modules/$(uname -r)/    # Modules for the current kernel

# Search/install kernels:
apt search linux-generic-hwe    # Ubuntu HWE kernels
dnf list kernel                 # CentOS available kernels
```

---

### systemd — Units & Services

```bash
ps 1                                        # Confirm systemd is PID 1 (init system)
systemd-analyze --system unit-paths         # Show all unit file search paths

# Unit file locations (in priority order):
# /etc/systemd/system     → custom overrides (highest priority)
# /run/systemd/system     → runtime-generated units
# /lib/systemd/system     → distribution-provided units

systemctl list-units                        # Active units
systemctl list-units --type=service         # Active services only
systemctl list-unit-files --type=service    # ALL services (including disabled)
```

**Service control:**
```bash
sudo systemctl start nginx          # Start service
sudo systemctl stop nginx           # Stop service
sudo systemctl restart nginx        # Stop and start
sudo systemctl reload nginx         # Reload config without full restart
sudo systemctl enable nginx         # Enable at boot
sudo systemctl disable nginx        # Disable at boot
sudo systemctl enable --now nginx   # Enable AND start immediately
sudo systemctl status nginx         # Show status and recent log lines
systemctl is-active nginx           # Output: active / inactive
systemctl is-enabled nginx          # Output: enabled / disabled

sudo systemctl mask nginx           # Prevent ALL starts (blocks manual and auto)
sudo systemctl unmask nginx         # Remove mask
sudo systemctl show nginx           # Low-level technical unit properties

# Apache2 equivalent (CentOS: replace apache2 with httpd):
sudo systemctl enable --now apache2
sudo systemctl --user daemon-reload     # Reload user-level units
```

---

### systemd Targets (Runlevels)

```bash
systemctl get-default                       # Show current default target
systemctl list-units --type=target --all    # All targets
systemctl set-default multi-user.target     # Boot to CLI (no GUI)
systemctl set-default graphical.target      # Boot to GUI
systemctl isolate multi-user.target         # Switch to CLI NOW
systemctl isolate graphical.target          # Switch to GUI NOW
systemctl cat graphical.target              # View target unit file
```

---

### Custom Service Unit File

```bash
sudo nano /etc/systemd/system/myapp.service
```

```ini
[Unit]
Description=My Application
After=network.target

[Service]
ExecStart=/usr/bin/myapp
Restart=on-failure
User=karim
WorkingDirectory=/home/karim

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload                # Always reload after editing unit files
sudo systemctl enable --now myapp           # Enable and start

# Drop-in override (preferred over copying unit file):
sudo systemctl edit myapp.service           # Creates /etc/systemd/system/myapp.service.d/override.conf
```

---

### Journalctl — System Logs

```bash
journalctl                              # All logs (oldest first)
journalctl -f                           # Follow: stream new logs live
journalctl -b                           # Logs from current boot only
journalctl --list-boots                 # All boot sessions with IDs
journalctl -b -1                        # Logs from the previous boot

journalctl -u apache2                   # Logs for apache2 service
journalctl -u apache2 -f                # Follow apache2 logs
journalctl -u apache2 -r                # Reverse (newest first)
journalctl -p err                       # Error level and above only
journalctl -p warning..err              # Range of priorities

journalctl --since "today"
journalctl --since "2 hours ago"
journalctl --since "today" --until "1 hour ago"
journalctl -u ssh --since "1 hour ago"

echo 'my message' | systemd-cat         # Write a custom log entry
journalctl | grep SELinux               # Filter for SELinux messages

# CentOS: replace apache2 with httpd
```

---

### systemd Timers

A systemd timer is a cron alternative with full logging and dependency support.

```bash
# Create the service to run:
sudo systemctl edit --force --full my-task.service
```
```ini
[Unit]
Description=My periodic task
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/bin/ping -c 4 google.com
StandardOutput=append:/var/log/my-task.txt
```

```bash
# Create the timer:
sudo systemctl edit --force --full my-task.timer
```
```ini
[Unit]
Description=Run my-task every 15 minutes

[Timer]
OnActiveSec=1min
OnUnitActiveSec=15min
Unit=my-task.service

[Install]
WantedBy=timers.target
```

```bash
sudo systemctl enable --now my-task.timer
sudo systemctl list-timers                              # List all timers

# Test calendar expressions:
systemd-analyze calendar '*:00/15'                      # Every 15 minutes
systemd-analyze calendar 'Mon..Fri 08:00'               # Weekdays at 8 AM
systemd-analyze calendar '2026-08-01 12:00'             # One-time future date
```

---

### cgroups — Resource Limits

**cgroups v2 (modern unified hierarchy):**
```bash
# Check cgroup version in use:
mount | grep cgroup
cat /proc/filesystems | grep cgroup

# Create a cgroup and apply limits:
sudo mkdir /sys/fs/cgroup/mygroup
echo "10000 100000" | sudo tee /sys/fs/cgroup/mygroup/cpu.max    # 10% CPU
echo $((50 * 1024 * 1024)) | sudo tee /sys/fs/cgroup/mygroup/memory.max  # 50 MB RAM

# Assign a process:
echo $$ | sudo tee /sys/fs/cgroup/mygroup/cgroup.procs   # Add current shell

# Monitor:
cat /sys/fs/cgroup/mygroup/cpu.stat
cat /sys/fs/cgroup/mygroup/memory.current

# Clean up (all processes must have left first):
sudo rmdir /sys/fs/cgroup/mygroup
```

**cgroups via systemd service unit (recommended):**
```ini
[Service]
MemoryMax=300M
CPUQuota=25%
```

**cgroups v1 (legacy):**
```bash
sudo apt install cgroup-tools               # Ubuntu
sudo dnf install libcgroup-tools            # CentOS

sudo cgcreate -g memory,cpu:/firefox
sudo cgset -r memory.limit_in_bytes=$((300*1024*1024)) firefox
sudo cgset -r cpu.shares=50 firefox
sudo cgexec -g memory,cpu:/firefox firefox
```

---

## 16. Storage — Partitions, Mounts & LVM

### Partitioning with `parted`

```bash
sudo parted                             # Interactive mode
sudo parted /dev/sdb                    # Work on a specific disk

# Inside parted:
    print devices                       # List all storage devices
    select /dev/sdb                     # Switch to disk
    print partitions                    # Show current partitions
    mklabel gpt                         # Create GPT partition table (⚠️ destroys data)
    mklabel msdos                       # MBR partition table (for BIOS/legacy)
    unit GiB                            # Set unit for sizes
    mkpart primary ext4 0% 50%          # First partition using first 50%
    mkpart primary ext4 50% 100%        # Second partition using remaining space
    name 1 backup                       # Name the first partition
    set 1 lvm on                        # Mark partition for LVM
    rm 1                                # Remove partition 1
    quit
```

---

### Formatting & Mounting

```bash
# Format:
sudo mkfs.ext4 /dev/sdb1                # Format as ext4
sudo mkfs.xfs /dev/sdb1                 # Format as XFS
sudo mkfs.exfat /dev/sdb1               # Format as exFAT
sudo mkfs.vfat /dev/sdb1                # Format as FAT32

# Label a partition:
sudo e2label /dev/sdb1 mydata           # Label ext4 filesystem
sudo fatlabel /dev/sdb1 mydata          # Label FAT filesystem
sudo ntfslabel /dev/sdb1 mydata         # Label NTFS filesystem

# Mount:
sudo mkdir /mnt/backup
sudo mount /dev/sdb1 /mnt/backup                            # Basic mount
sudo mount -o ro /dev/sdb1 /mnt/backup                      # Read-only
sudo mount -o ro,noexec,nosuid,noatime /dev/sdb1 /mnt/data  # Secure mount options

umount /dev/sdb1                        # Unmount by device
umount /mnt/backup                      # Unmount by mount point

# Check:
mount | grep /mnt/backup                # Show mount details
lsblk -f                                # Filesystem type, label, UUID
df -h                                   # Disk space usage
sudo blkid /dev/sdb1                    # UUID and filesystem type
```

---

### Persistent Mounts — `/etc/fstab`

```bash
blkid /dev/sdb1     # Get UUID first
nano /etc/fstab
```

```
# device/UUID              mountpoint   fstype  options     dump  pass
UUID=31b8c5a6-738d-4f3d   /mnt/data    ext4    defaults     0     2
/dev/sdb1                 /mnt/backup  ext4    ro,noexec    0     0
```

```bash
sudo mount -a       # Test: mount everything in fstab (catches errors before reboot)
```

---

### SMART Disk Health

```bash
sudo apt install smartmontools          # Ubuntu
sudo dnf install smartmontools          # CentOS

sudo smartctl -H /dev/sda               # Overall health: PASSED / FAILED
sudo smartctl -a /dev/sda               # Full SMART data
sudo smartctl -t short /dev/sda         # Run a short self-test
sudo smartctl --all /dev/sda            # Complete info
```

---

### Filesystem Check — `fsck`

```bash
# ⚠️ Never run fsck on a mounted filesystem
sudo umount /dev/sdb1
sudo fsck -n /dev/sda1              # Dry run: check without fixing
sudo fsck -y /dev/sda1              # Auto-fix all errors
fsck.ext4 /dev/sdb1                 # ext4-specific check

sudo e2fsck -f /dev/sdb1            # Force check even if marked clean
sudo journalctl -b | grep 'File System'    # Check logs from last boot
```

---

### `tune2fs` — Filesystem Parameters

```bash
sudo tune2fs -l /dev/sda1           # List all filesystem parameters
sudo tune2fs -L MyData /dev/sda1    # Set filesystem label
sudo tune2fs -i 30d /dev/sda1       # Set check interval to 30 days
tune2fs -i 0 /dev/sda2              # Disable time-based checks
tune2fs -c 0 /dev/sda2              # Disable mount-count-based checks
```

---

### LVM — Logical Volume Manager

```bash
sudo apt install lvm2               # Ubuntu
sudo dnf install lvm2               # CentOS
```

**Setup flow:**
```bash
# 1. Prepare disks with parted (mark as LVM):
sudo parted /dev/sdb mklabel gpt
sudo parted /dev/sdb mkpart primary 0% 100%
sudo parted /dev/sdb set 1 lvm on

# 2. Create Physical Volumes (PV):
sudo pvcreate /dev/sdb1 /dev/sdc1 /dev/sdd1
pvs; pvdisplay; pvscan

# 3. Create Volume Group (VG):
sudo vgcreate myvg /dev/sdb1 /dev/sdc1 /dev/sdd1
vgs; vgdisplay

# 4. Create Logical Volumes (LV):
sudo lvcreate -L 10G -n data myvg           # Fixed size
sudo lvcreate -l 100%FREE -n backups myvg   # Use all remaining space
lvs; lvdisplay

# 5. Format and mount:
sudo mkfs.ext4 /dev/myvg/data
sudo mkdir /mnt/data
sudo mount /dev/myvg/data /mnt/data
lsblk -f; df -h
```

**Managing LVM:**
```bash
# Extend:
sudo vgextend myvg /dev/sde1                    # Add a new disk to VG
sudo lvextend -L +20G --resizefs /dev/myvg/data # Extend LV and filesystem

# Shrink (careful!):
sudo e2fsck -f /dev/myvg/data                   # Check filesystem first
sudo resize2fs /dev/myvg/data 15G               # Shrink filesystem to 15G
sudo lvreduce -L 15G /dev/myvg/data             # Then shrink the LV

# Move and remove a PV:
sudo pvmove -v /dev/sdc1                        # Move data off /dev/sdc1
sudo vgreduce myvg /dev/sdc1                    # Remove from VG
sudo pvremove /dev/sdc1                         # Remove PV label

# Rename an LV:
sudo lvrename myvg oldname newname

# Clean up / destroy everything:
sudo umount /mnt/data
sudo lvchange -an /dev/myvg/data
sudo lvremove /dev/myvg/data
sudo vgremove myvg
sudo pvremove /dev/sdb1 /dev/sdc1 /dev/sdd1
sudo wipefs -a /dev/sdb /dev/sdc /dev/sdd
lsblk -f                                        # Confirm clean state
```

---

## 17. System Upgrade & Recovery

### Ubuntu — Full Upgrade

```bash
sudo apt update && sudo apt full-upgrade    # Step 1: upgrade all packages
sudo reboot

sudo apt install update-manager-core
sudo do-release-upgrade                     # Step 2: upgrade to next release (LTS → LTS)

# To upgrade to non-LTS / dev release:
nano /etc/update-manager/release-upgrades   # → Set: Prompt=normal
sudo do-release-upgrade

lsb_release -a                              # Confirm new version
```

---

### Ubuntu — Boot Recovery (after failed upgrade)

```bash
# 1. Boot from Ubuntu Live USB → "Try Ubuntu"
# 2. Mount your installed system (check disk with: lsblk)
sudo mount /dev/sda2 /mnt                   # Mount root partition
sudo mount --bind /dev  /mnt/dev
sudo mount --bind /proc /mnt/proc
sudo mount --bind /sys  /mnt/sys
sudo chroot /mnt                            # Enter installed system as root

# Inside chroot — fix GRUB:
nano /etc/default/grub
# → Comment: GRUB_TIMEOUT_STYLE=hidden
# → Set: GRUB_TIMEOUT=5
sudo update-grub

# Set a specific kernel as default:
nano /boot/grub/grub.cfg                    # Find the exact entry name
nano /etc/default/grub
# → GRUB_DEFAULT="1>Ubuntu, with Linux 6.8.0-31-generic"
sudo update-grub

# Protect working kernel from autoremove:
dpkg -S vmlinuz-6.8.0-31-generic            # Find package name
sudo apt-mark manual linux-image-6.8.0-31-generic
sudo apt-mark manual linux-headers-6.8.0-31-generic

# Remove broken kernel (optional — confirm uname -r first!):
dpkg --list | grep linux-image
sudo apt remove linux-image-6.8.0-35-generic
sudo apt autoremove
sudo update-grub
```

---

### CentOS — Full Upgrade

```bash
sudo dnf upgrade --refresh
sudo reboot

# Upgrade to CentOS Stream:
sudo dnf install centos-release-stream
sudo dnf swap centos-linux-repos centos-stream-repos
sudo dnf distro-sync
cat /etc/centos-release
```

**CentOS — Boot Recovery:**
```bash
# 1. Boot from CentOS Live USB → "Rescue a CentOS system"
sudo chroot /mnt/sysimage
sudo mount --bind /dev  /mnt/sysimage/dev
sudo mount --bind /proc /mnt/sysimage/proc
sudo mount --bind /sys  /mnt/sysimage/sys

# Fix GRUB:
sudo nano /etc/default/grub                 # Set GRUB_TIMEOUT=5
sudo grub2-mkconfig -o /boot/grub2/grub.cfg

# Set specific kernel:
sudo nano /etc/default/grub
# → GRUB_DEFAULT="CentOS Linux (6.8.0-31.el9.x86_64)"
sudo grub2-mkconfig -o /boot/grub2/grub.cfg

# Protect kernel:
sudo dnf mark install kernel-core-6.8.0-31.el9.x86_64
sudo dnf mark install kernel-modules-6.8.0-31.el9.x86_64

# Remove broken kernel:
rpm -qa | grep kernel
sudo dnf remove kernel-core-6.8.0-35.el9.x86_64
sudo grub2-mkconfig -o /boot/grub2/grub.cfg
```

---

## 18. Cron Jobs

### Cron Basics

```bash
crontab -e                  # Edit current user's crontab (opens in $EDITOR)
EDITOR=nano crontab -e      # Force nano as editor
crontab -l                  # List current crontab
crontab -r                  # Remove ALL cron jobs (use with caution!)
crontab -u ahmed -l         # List another user's crontab (root only)

cd /var/spool/cron/         # Crontab files stored here (root access required)

journalctl -u cron.service -f       # Follow cron logs (Ubuntu)
journalctl -u crond.service -r      # Recent cron logs (CentOS)
```

---

### Scheduling Syntax

```
┌───────────── minute (0–59)
│ ┌───────────── hour (0–23)
│ │ ┌───────────── day of month (1–31)
│ │ │ ┌───────────── month (1–12)
│ │ │ │ ┌───────────── day of week (0–7, 0 and 7 = Sunday)
│ │ │ │ │
* * * * * command
```

**Examples:**
```bash
# Inside crontab (set SHELL and PATH to avoid environment issues):
SHELL=/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin

*/5 * * * *       ping -c 10 google.com >> ~/ping.log 2>&1    # Every 5 minutes
0,15,30,45 * * * * command                                     # Every 15 minutes
* 8-20 * * *      command                                      # Every minute, 8 AM–8 PM
* 1-23/2 * * *    command                                      # Every minute, odd hours
0 0 * * 1         command                                      # Every Monday at midnight
0 2 * * *         command                                      # Daily at 2 AM
@reboot           command                                      # Once at system startup
```

**Special shortcuts:**

| Shortcut | Equivalent |
|----------|------------|
| `@reboot` | Run once at startup |
| `@hourly` | `0 * * * *` |
| `@daily` | `0 0 * * *` |
| `@weekly` | `0 0 * * 0` |
| `@monthly` | `0 0 1 * *` |

---

### Email Notifications

```bash
# Ubuntu:
sudo apt install mailutils postfix
# In crontab:
MAILTO=your@email.com
* * * * * ping -c 10 google.com
sudo cat /var/mail/karim               # View emails locally

# CentOS:
sudo dnf install sendmail               # Note: dnf, not apt
sudo systemctl enable --now sendmail
# In /etc/crontab:
MAILTO=your@email.com
* * * * * root ping -c 4 google.com
sudo cat /var/mail/root
```

---

### Prevent Overlapping Jobs — `flock`

```bash
# In crontab:
*/5 * * * * flock -n /tmp/myjob.lock /path/to/job.sh >> /var/log/job.log 2>&1
```

> `flock -n` skips execution if the lock is already held (previous run still going).

---

### System-wide Cron

```bash
sudo nano /etc/crontab
# Format: minute hour dom month dow USERNAME command
0 2 * * *  root  /usr/local/bin/backup.sh >> /var/log/backup.log 2>&1

# Drop-in directories (scripts placed here run automatically):
ls /etc/cron.daily/
ls /etc/cron.weekly/
ls /etc/cron.monthly/
ls /etc/cron.hourly/
```

---

### Anacron — For Systems That Aren't Always On

Anacron runs missed jobs when the system comes back up (unlike cron which simply skips them).

```bash
cat /etc/anacrontab     # Main anacron config
```

```
# period(days)  delay(min)  job-id       command
1               5           cron.daily   run-parts /etc/cron.daily
7               10          cron.weekly  run-parts /etc/cron.weekly
30              15          cron.monthly run-parts /etc/cron.monthly
```

> On Ubuntu, anacron is included in the `cron` package and runs automatically.

---

## 19. Networking

### IP Commands

```bash
ip addr show                            # Show all interfaces and IPs
ip -c a                                 # Show with color
ip link show                            # Show link-layer info (MAC, state, MTU)
ip link set dev ens33 up                # Bring interface up
ip link set dev ens33 down              # Bring interface down

ip addr add 192.168.1.10/24 dev ens33   # Add static IP
ip addr del 192.168.1.10/24 dev ens33   # Remove IP

ip route show                           # Show routing table
ip -c r                                 # Routing table with color
ip route get 8.8.8.8                    # Which route would be used for this IP
ip route add 9.9.9.9/32 via 192.168.1.1 dev ens33   # Add static route
ip route del 9.9.9.9/32 via 192.168.1.1 dev ens33   # Remove static route
ip route add default via 192.168.1.1    # Set default gateway

ss -4nap                                # All IPv4 sockets with process info
ss -tulnp                               # TCP/UDP listening ports with PIDs
ss -s                                   # Socket summary statistics
```

---

### Connectivity & Path Testing

```bash
ping google.com                         # Continuous ping (Ctrl+C to stop)
ping -c 4 google.com                    # Send exactly 4 packets
ping -i 0.2 google.com                  # Faster interval (0.2s between packets)

traceroute google.com                   # Show each hop to destination
traceroute -m 50 google.com             # Max 50 hops
traceroute -n google.com                # Skip DNS lookups (faster)

# mtr — ping + traceroute combined (far more useful than traceroute alone)
sudo apt install mtr                    # Ubuntu
sudo dnf install mtr                    # CentOS

mtr google.com                          # Live: packet loss and latency per hop
mtr --report google.com                 # Non-interactive report (10 packets, then exit)
mtr --report-cycles 30 google.com       # More packets = more accurate loss %
mtr -n google.com                       # No DNS resolution (faster)
```

> `mtr` is the best tool for diagnosing flaky connections because it shows **live packet loss at each hop**, not just the path.

---

### DNS Tools

```bash
# dig — the standard DNS query tool
dig google.com                      # A record (IPv4 address)
dig google.com AAAA                 # IPv6 address
dig google.com MX                   # Mail exchange records
dig google.com NS                   # Authoritative name servers
dig google.com TXT                  # TXT records (SPF, DKIM, domain verification)
dig google.com ANY                  # All record types
dig +short google.com               # Clean output: just the IP(s)
dig +trace google.com               # Full resolution path from root servers
dig @8.8.8.8 google.com             # Query a specific DNS server (Google's here)
dig @1.1.1.1 google.com             # Query Cloudflare's DNS
dig -x 8.8.8.8                      # Reverse lookup: IP → hostname (PTR record)

# nslookup — simpler alternative
nslookup google.com                 # Basic A record lookup
nslookup google.com 1.1.1.1         # Use a specific DNS server
nslookup -type=MX google.com        # Mail records
nslookup -type=TXT google.com       # TXT records

# host — quickest one-liner
host google.com                     # A + MX records in one shot
host 8.8.8.8                        # Reverse lookup

# resolvectl — manage systemd-resolved (Ubuntu 18.04+)
resolvectl status                   # Which DNS server each interface is using
resolvectl query google.com         # Resolve with cache hit/miss info
resolvectl flush-caches             # Clear the local DNS cache
cat /etc/resolv.conf                # See configured DNS servers
```

---

### Static IP — Netplan (Ubuntu)

```bash
ip -c a                                         # Find your interface name (e.g. ens33)
ip -c r                                         # Find your gateway IP

sudo nano /etc/netplan/50-cloud-init.yaml
```

```yaml
network:
  version: 2
  ethernets:
    ens33:
      dhcp4: no
      addresses:
        - 192.168.1.100/24
      routes:
        - to: 0.0.0.0/0
          via: 192.168.1.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1
```

```bash
sudo netplan apply
journalctl -u systemd-networkd -b       # Check for errors
```

---

### Static IP — nmcli (CentOS/RHEL)

```bash
nmcli device status                                         # List interfaces and their state
nmcli connection show                                       # List all connection profiles
sudo nmcli con modify ens33 ipv4.addresses 192.168.1.100/24
sudo nmcli con modify ens33 ipv4.gateway 192.168.1.1
sudo nmcli con modify ens33 ipv4.dns "8.8.8.8,1.1.1.1"
sudo nmcli con modify ens33 ipv4.method manual              # Disable DHCP
sudo nmcli con down ens33 && sudo nmcli con up ens33        # Apply changes

nmtui                                                       # Text-based UI — easier alternative to nmcli
journalctl -u NetworkManager -b
```

---

### File Transfer — `scp` / `rsync` / `sftp`

```bash
# scp — encrypted copy over SSH (simple, one-shot transfers)
scp file.txt karim@server:/home/karim/          # Local file → remote
scp karim@server:/var/log/syslog ~/Desktop/     # Remote file → local
scp -r folder/ karim@server:/home/karim/        # Recursive directory copy
scp -P 2222 file.txt karim@server:/tmp/         # Non-default SSH port (capital -P)
scp karim@server1:/file.txt karim@server2:/tmp/ # Remote to remote

# rsync — smart sync: only transfers what changed (preferred for directories)
rsync -av folder/ karim@server:/backup/         # Sync with verbose output
rsync -avz folder/ karim@server:/backup/        # Compress during transfer (faster on slow links)
rsync -av --delete folder/ karim@server:/backup/  # Mirror: delete files missing from source
rsync -av --dry-run folder/ karim@server:/backup/ # Preview what WOULD be transferred (safe test)
rsync -av --progress folder/ karim@server:/backup/ # Show per-file progress
rsync -av karim@server:/var/www/ ./local-backup/   # Pull from remote to local
rsync -av -e "ssh -p 2222" folder/ karim@server:/backup/  # Custom SSH port

# sftp — interactive FTP-like session over SSH
sftp karim@server
# Inside sftp:
#   ls / lls               → list remote / local directory
#   get remote_file.txt    → download file to current local directory
#   put local_file.txt     → upload file to current remote directory
#   get -r remote_folder/  → download entire folder
#   cd / lcd               → change remote / local directory
#   pwd / lpwd             → print remote / local working directory
#   rm remote_file.txt     → delete remote file
#   exit                   → quit
```

> Prefer `rsync` over `scp` for directories — on repeated runs it only transfers changed files, making it dramatically faster.

---

### HTTP Tools — `curl` / `wget`

```bash
# curl — transfer data to/from URLs (HTTP, HTTPS, FTP, and more)
curl https://example.com                    # GET request, print body to stdout
curl -o file.html https://example.com       # Save output to a named file
curl -O https://example.com/archive.zip     # Save with the server's original filename
curl -L https://example.com                 # Follow HTTP redirects
curl -I https://example.com                 # Headers only (HEAD request)
curl -v https://example.com                 # Verbose: full request + response headers
curl -s https://example.com                 # Silent: suppress progress and errors
curl -s https://example.com -o /dev/null -w "%{http_code}\n"  # Print only status code

# POST request with JSON body:
curl -X POST https://api.example.com/login \
     -H "Content-Type: application/json" \
     -d '{"user":"karim","pass":"1234"}'

# With Authorization header:
curl -H "Authorization: Bearer YOUR_TOKEN" https://api.example.com/data

# Upload a file (multipart form):
curl -F "file=@/path/to/file.txt" https://example.com/upload

# Resume an interrupted download:
curl -C - -O https://example.com/bigfile.zip

# wget — simpler download tool, great for mirroring
wget https://example.com/file.zip               # Download a file
wget -O myfile.zip https://example.com/f.zip    # Save with a custom name
wget -c https://example.com/bigfile.zip         # Continue/resume a download
wget -q https://example.com                     # Quiet mode (no output)
wget -r -np --no-parent https://example.com/docs/  # Recursive: mirror a directory
```

---

### Port Scanning — `nmap`

```bash
sudo apt install nmap               # Ubuntu
sudo dnf install nmap               # CentOS

nmap 192.168.1.1                    # Scan top 1000 most common ports
nmap -p 22,80,443 192.168.1.1       # Scan specific ports only
nmap -p 1-65535 192.168.1.1         # Scan ALL 65535 ports
nmap -sV 192.168.1.1                # Detect service name and version on open ports
nmap -O 192.168.1.1                 # Try to detect the OS (requires sudo)
nmap -A 192.168.1.1                 # Aggressive: OS + versions + scripts + traceroute
nmap 192.168.1.0/24                 # Scan an entire subnet
nmap -sn 192.168.1.0/24             # Ping sweep: find alive hosts WITHOUT scanning ports
nmap --open 192.168.1.0/24          # Only show hosts that have open ports
nmap -sU -p 53,123 192.168.1.1      # UDP scan (DNS port 53, NTP port 123)
```

> ⚠️ Only scan networks you own or have explicit written permission to test.

---

### Netcat — `nc`

The Swiss Army knife of networking — creates raw TCP/UDP connections.

```bash
# Test if a port is open (faster and cleaner than telnet):
nc -zv 192.168.1.1 22               # Check if SSH port is open
nc -zv 192.168.1.1 80               # Check if HTTP port is open
nc -zv 192.168.1.1 20-25            # Scan a port range

# Simple TCP listener (server side):
nc -l 4444                          # Listen on port 4444, print anything received

# Connect to the listener (client side):
nc 192.168.1.1 4444                 # Connect to the listening server

# Transfer a file over the network (no SSH needed):
# On the receiver:  nc -l 4444 > received_file.txt
# On the sender:    nc 192.168.1.1 4444 < file.txt

# Send a raw HTTP request manually:
echo -e "GET / HTTP/1.0\r\nHost: example.com\r\n\r\n" | nc example.com 80

# Quick port check in a script (exits 0 if open, 1 if closed):
nc -z -w 3 192.168.1.1 22 && echo "SSH open" || echo "SSH closed"
# -w 3 = timeout after 3 seconds
```

---

### SSH Tunnels

SSH can forward ports securely — extremely useful for accessing services that aren't directly exposed.

```bash
# Local port forwarding (-L):
# "Make the remote service available on MY local port"
# Syntax: ssh -L LOCAL_PORT:TARGET_HOST:TARGET_PORT user@jump_host

ssh -L 8080:localhost:80 karim@server
# → Access server's port 80 via http://localhost:8080 on your machine

ssh -L 5432:db-internal.lan:5432 karim@jumpserver
# → Connect local DB client to localhost:5432 → tunneled to db-internal (only reachable from jumpserver)

# Remote port forwarding (-R):
# "Expose MY local service on the remote server's port"
# Syntax: ssh -R REMOTE_PORT:localhost:LOCAL_PORT user@server

ssh -R 9000:localhost:3000 karim@server
# → Anyone on server can reach your local port 3000 via server:9000

# Dynamic SOCKS proxy (-D):
# "Route all traffic through the server" (like a simple VPN)
ssh -D 1080 karim@server
# → Set browser SOCKS5 proxy to localhost:1080 — traffic exits from server

# Run tunnel in the background (no shell, no command):
ssh -fNL 8080:localhost:80 karim@server
# -f = fork to background before executing
# -N = don't run a remote command (just forward ports)

# Keep alive + background + local forward in one:
ssh -fNL 8080:localhost:80 -o ServerAliveInterval=60 karim@server
```

**Quick reference:**

| Flag | Meaning |
|------|---------|
| `-L local:host:remote` | Forward local port to remote target |
| `-R remote:host:local` | Expose local port on the remote server |
| `-D port` | Dynamic SOCKS proxy through the server |
| `-f` | Fork to background |
| `-N` | No command — port forwarding only |

---

### SSH Setup

**Install SSH server:**
```bash
# Ubuntu:
sudo apt install -y openssh-server
sudo systemctl enable --now ssh

# CentOS:
sudo dnf install -y openssh-server
sudo systemctl enable --now sshd
```

**Connect:**
```bash
ssh karim@192.168.1.100             # Connect by IP
ssh karim@ubuntu                    # Connect by hostname (if /etc/hosts is set)
ssh -p 2222 karim@server            # Non-default port
ssh -i ~/.ssh/my_key karim@server   # Use a specific private key
ssh -v karim@server                 # Verbose: debug connection issues
```

**Set up passwordless SSH (key-based auth):**
```bash
# Generate a key pair (run on the CLIENT):
ssh-keygen -t ed25519               # Ed25519 — modern and recommended
ssh-keygen -t rsa -b 4096           # RSA 4096-bit — widely compatible alternative

ssh-copy-id karim@192.168.1.100     # Copy public key to server (easiest method)

# Manual alternative:
cat ~/.ssh/id_ed25519.pub | ssh karim@server \
  "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys && chmod 700 ~/.ssh"
```

**Windows → Linux:**
```powershell
type $env:USERPROFILE\.ssh\id_rsa.pub | ssh karim@192.168.1.100 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

---

### SSH Configuration

**Prevent disconnection — add to `/etc/ssh/sshd_config` on the server:**
```
ClientAliveInterval 60
ClientAliveCountMax 0
```

**Linux/Mac client config (`~/.ssh/config`) — shortcuts and keep-alive:**
```
Host myserver
    HostName 192.168.1.100
    User karim
    Port 22
    IdentityFile ~/.ssh/id_ed25519
    ServerAliveInterval 60
    ServerAliveCountMax 0

# Now: ssh myserver  (instead of: ssh -p 22 -i ~/.ssh/id_ed25519 karim@192.168.1.100)
```

**Windows client config (`%USERPROFILE%\.ssh\config`):**
```
Host *
    ServerAliveInterval 60
    ServerAliveCountMax 0
```

**Harden SSH (highly recommended on any internet-facing server):**
```bash
sudo nano /etc/ssh/sshd_config
```
```
PermitRootLogin no              # Never allow root login over SSH
PasswordAuthentication no       # Keys only — disable password login
AllowUsers karim                # Whitelist specific users
Port 2222                       # Non-default port reduces automated attacks
MaxAuthTries 3                  # Limit brute-force attempts
```
```bash
sudo systemctl restart ssh      # Ubuntu
sudo systemctl restart sshd     # CentOS
```

**Check SSH logs:**
```bash
cat /var/log/auth.log            # Ubuntu — logins, sudo, SSH activity
cat /var/log/secure              # CentOS — same
journalctl -u ssh --since "1 hour ago"   # Ubuntu: recent SSH logs via journald
journalctl -u sshd --since "1 hour ago"  # CentOS
```

**Verify server fingerprint (before connecting to a new server):**
```bash
# On the SERVER — get the true fingerprint:
sudo ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub
sudo ssh-keygen -lf /etc/ssh/ssh_host_ecdsa_key.pub
sudo ssh-keygen -lf /etc/ssh/ssh_host_rsa_key.pub

# On the CLIENT — check what you accepted:
ssh-keygen -lf ~/.ssh/known_hosts
```

---

### ARP & Neighbor Table

```bash
ip neigh show                       # ARP table: maps IP addresses → MAC addresses
ip neigh show dev ens33             # For a specific interface only
ip neigh flush all                  # Clear entire ARP cache
ip neigh add 192.168.1.50 lladdr aa:bb:cc:dd:ee:ff dev ens33   # Add static ARP entry

# Legacy arp command (still widely available):
arp -n                              # Show ARP table (numeric — no DNS lookups)
arp -d 192.168.1.1                  # Delete a specific ARP entry
```

---

### Bandwidth Monitoring

```bash
sudo apt install iftop nethogs vnstat    # Ubuntu
sudo dnf install iftop nethogs vnstat    # CentOS

iftop                           # Live bandwidth per connection (like top but for network)
sudo iftop -i ens33             # Specify interface
sudo iftop -n                   # Don't resolve hostnames (faster)

sudo nethogs                    # Bandwidth broken down by PROCESS (find what's consuming bandwidth)
sudo nethogs ens33              # Filter to one interface

vnstat                          # Traffic summary (uses historical data — must run a while first)
vnstat -l                       # Live traffic stats
vnstat -d                       # Daily breakdown
vnstat -m                       # Monthly breakdown
vnstat -i ens33                 # Stats for a specific interface
sudo systemctl enable --now vnstat  # Enable vnstat data collection service

# Built-in — no install needed:
watch -n 1 cat /proc/net/dev    # Raw RX/TX byte counters per interface
```

---

### Packet Capture — `tcpdump`

```bash
sudo apt install tcpdump        # Ubuntu (usually pre-installed)
sudo dnf install tcpdump        # CentOS

sudo tcpdump -i ens33                       # Capture all traffic on ens33
sudo tcpdump -i any                         # Capture on ALL interfaces
sudo tcpdump -i ens33 -n                    # No DNS resolution (show IPs only)
sudo tcpdump -i ens33 -c 50                 # Capture exactly 50 packets then stop
sudo tcpdump -i ens33 port 80               # Only HTTP traffic
sudo tcpdump -i ens33 port 443              # Only HTTPS traffic
sudo tcpdump -i ens33 host 192.168.1.1      # Traffic to/from a specific host
sudo tcpdump -i ens33 src 192.168.1.1       # Traffic FROM a specific host
sudo tcpdump -i ens33 dst 192.168.1.1       # Traffic TO a specific host

# Save to a file (open with Wireshark):
sudo tcpdump -i ens33 -w capture.pcap
sudo tcpdump -r capture.pcap                # Read back a saved capture

# Combine filters with and / or:
sudo tcpdump -i ens33 host 192.168.1.1 and port 22   # SSH traffic to/from a host
sudo tcpdump -i ens33 port 80 or port 443             # All web traffic
```

---

### NIC Information — `ethtool`

```bash
ethtool ens33               # NIC speed, duplex, link status, auto-negotiation
ethtool -i ens33            # Driver name, version, firmware version
ethtool -S ens33            # NIC statistics: errors, dropped packets, collisions
ethtool -a ens33            # Pause parameters (flow control)
```

---

### `/etc/hosts` — Hostname Mapping

```bash
sudo nano /etc/hosts
# Add entries like:
192.168.1.100    ubuntu
192.168.1.101    centos
10.0.0.5         db-server db

# Now you can use: ssh karim@ubuntu   or   ping db-server
```

---

### UFW — Ubuntu's Default Firewall

UFW (Uncomplicated Firewall) is what most Ubuntu servers use. It's a frontend for iptables.

```bash
sudo ufw status                     # Show status (active/inactive) and all rules
sudo ufw status verbose             # More detail including default policies
sudo ufw status numbered            # Rules with index numbers (for deleting)

sudo ufw enable                     # Turn on the firewall
sudo ufw disable                    # Turn off the firewall
sudo ufw reset                      # Remove ALL rules and disable (start fresh)

# Allow / deny ports:
sudo ufw allow 22                   # Allow SSH (TCP + UDP on port 22)
sudo ufw allow 22/tcp               # Allow SSH (TCP only — more precise)
sudo ufw allow 80/tcp               # Allow HTTP
sudo ufw allow 443/tcp              # Allow HTTPS
sudo ufw allow 8080:8090/tcp        # Allow a port range

sudo ufw deny 23/tcp                # Block Telnet
sudo ufw deny from 192.168.1.50     # Block all traffic from a specific IP

# Source-restricted rules (more specific):
sudo ufw allow from 192.168.1.0/24 to any port 22     # SSH only from local network
sudo ufw allow from 10.0.0.5 to any port 5432         # PostgreSQL from one IP only

# Delete rules:
sudo ufw delete allow 80/tcp                          # Delete by rule description
sudo ufw delete 3                                     # Delete rule number 3 (from 'status numbered')

# Application profiles (pre-defined service rules):
sudo ufw app list                   # Show available app profiles
sudo ufw allow OpenSSH              # Allow SSH by app profile name
sudo ufw allow 'Nginx Full'         # Allow both HTTP (80) and HTTPS (443) for Nginx
sudo ufw allow 'Nginx HTTP'         # HTTP only
sudo ufw allow 'Apache Full'        # HTTP + HTTPS for Apache

# Logging:
sudo ufw logging on                 # Enable firewall logging
sudo ufw logging medium             # Log level: low / medium / high / full
cat /var/log/ufw.log                # View UFW log
```

> UFW and firewalld conflict — only run one at a time. Disable UFW before enabling firewalld, and vice versa.

---

### Screen — Persistent Terminal Sessions

```bash
sudo apt install screen             # Install
sudo dnf install screen             # CentOS

screen                              # Start a new unnamed session
screen -S mysession                 # Start with a name
screen -list                        # List all sessions
screen -r mysession                 # Reattach to a session
screen -x mysession                 # Attach to a session (even if someone else is viewing it)

# Inside screen:
# Ctrl+A then D  → detach (session keeps running in the background)
# Ctrl+A then c  → create a new window
# Ctrl+A then n  → next window
# Ctrl+A then p  → previous window
# Ctrl+A then "  → list all windows
# Ctrl+A then K  → kill current window
# Ctrl+A then \  → kill all windows and exit screen
```

---

## 20. Firewall (firewalld)

```bash
firewall-cmd --state                        # Is firewalld running?
firewall-cmd --list-all                     # Show active rules

# Install on Ubuntu (disable UFW first):
sudo ufw disable
sudo apt install firewalld
sudo systemctl enable --now firewalld

# CentOS: firewalld is usually pre-installed
sudo systemctl enable --now firewalld
```

**Services & Ports:**
```bash
sudo firewall-cmd --get-services                            # All known services
sudo firewall-cmd --info-service http                       # Details about http service

# Temporary rules (lost on reload):
sudo firewall-cmd --add-service=http
sudo firewall-cmd --add-port=8080/tcp
sudo firewall-cmd --remove-service=http
sudo firewall-cmd --remove-port=8080/tcp

# Permanent rules (persist across reload/reboot):
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --permanent --add-service=mdns
sudo firewall-cmd --reload                                  # Apply permanent rules

# Add port to an existing service definition:
sudo firewall-cmd --service=http --add-port=8080/tcp --permanent
```

**Zones:**
```bash
sudo firewall-cmd --get-zones                               # All zone names
sudo firewall-cmd --get-default-zone                        # Active default zone
sudo firewall-cmd --set-default-zone=work                   # Change default
sudo firewall-cmd --zone=work --list-all                    # Rules in 'work' zone
sudo firewall-cmd --zone=public --change-interface=ens33    # Assign interface to zone
sudo firewall-cmd --zone=work --add-service=mdns --permanent

# Zone definitions:
ls /usr/lib/firewalld/zones/        # Default zone configs
ls /etc/firewalld/zones/            # Custom overrides
```

---

## 21. SELinux

> SELinux is primarily used on CentOS/RHEL. Ubuntu uses AppArmor by default.

```bash
# Install NGINX on CentOS for these examples:
sudo dnf install nginx -y
sudo systemctl enable --now nginx
```

**Check status:**
```bash
sudo getenforce                     # Enforcing / Permissive / Disabled
sudo sestatus                       # Detailed SELinux status

sudo setenforce 0                   # Temporarily set Permissive (no block, only log)
sudo setenforce 1                   # Temporarily set Enforcing (blocks and logs)

sudo nano /etc/selinux/config       # Permanent setting (requires reboot)
# SELINUX=enforcing | permissive | disabled
```

**Contexts:**
```bash
ls -Z                               # File SELinux contexts
ls -aZ                              # Include hidden files
ps -eZ                              # Process contexts
ps -efZ                             # Full process list with contexts
id -Z                               # Your current context
```

**Change contexts:**
```bash
sudo chcon -t httpd_sys_content_t file.html         # Temporary context change
sudo restorecon -v file.html                        # Restore to default context
sudo restorecon -R -v /mywebroot/                   # Recursive restore

# Permanent context rule:
sudo semanage fcontext -a -t httpd_sys_content_t '/mywebroot(/.*)?'
sudo restorecon -R -v -F /mywebroot/

# Remove a context rule:
sudo semanage fcontext -d -t httpd_sys_content_t '/mywebroot(/.*)?'

# Equivalency shortcut (inherits contexts from another path):
sudo semanage fcontext -a -e /usr/share/nginx/html /public
sudo semanage fcontext -l | grep public
```

**Booleans:**
```bash
getsebool -a                                            # List all booleans
getsebool -a | grep httpd                               # Filter for httpd
sudo setsebool httpd_can_network_connect on             # Temporary
sudo setsebool -P httpd_can_network_connect on          # Permanent
sudo setsebool -P httpd_can_network_connect off
sudo semanage boolean -l                                # Full boolean list
```

**Ports:**
```bash
sudo semanage port -l | grep http                       # Ports allowed for HTTP
sudo semanage port -a -t http_port_t -p tcp 8888        # Allow NGINX on port 8888
```

**Audit logs:**
```bash
sudo ausearch -ts recent                                # Recent audit events
sudo ausearch -m avc                                    # All SELinux AVC denials
sudo ausearch -m avc -c nginx                           # Denials related to nginx
sudo journalctl -t setroubleshoot --since "1 hour ago" # SELinux issues
```

**Emergency recovery (broken /etc context):**
```bash
# 1. Reboot → in GRUB menu press 'e' on kernel entry
# 2. On the 'linux' line append: selinux=1 enforcing=0
# 3. Press Ctrl+X to boot (permissive mode)
# 4. After boot:
sudo restorecon -v -R /etc           # Restore correct labels to /etc
```

---

# Part III — Bash Scripting

## 22. Variables & `declare`

### Basic Variables

> **Command substitution `$( )`:** The `$( )` syntax runs a command in a subshell and replaces itself with that command's stdout. This is the standard way to capture command output into a variable. The older backtick syntax `` `cmd` `` does the same thing but is harder to read, doesn't nest cleanly, and is considered deprecated — always use `$( )` in scripts.

```bash
#!/usr/bin/env bash

name="Karim Wahba"
echo "Hello ${name}"

# Command substitution into variable:
files="$(ls ~)"
count="$(ls | wc -l)"
echo "Items: ${count}"
```

---

### `declare` — Variable Attributes

> **Why use `declare`?** Regular Bash variables are untyped strings with no constraints. `declare` lets you attach **attributes** to a variable that enforce behaviour: `-i` means the shell evaluates assignments as arithmetic (so `n="hello"` silently becomes `0`); `-r` prevents any further assignment or unsetting; `-a` and `-A` explicitly mark indexed and associative arrays (required for `-A`); `-x` exports the variable to child processes just like the `export` command. The `-l` and `-u` flags are handy for normalising user input — anything assigned to the variable is automatically lowercased or uppercased on storage.

```bash
declare var='linux'               # Regular variable (same as just: var='linux')
declare -r CONST='immutable'      # Read-only: cannot be changed or unset
declare -i number=42              # Integer: arithmetic only, strings become 0
declare -a myarray                # Indexed array
declare -A mymap                  # Associative array (dictionary)
declare -x MY_VAR='exported'      # Export (same as export MY_VAR='exported')
declare -l lower='HELLO'          # Auto-lowercase: value stored as 'hello'
declare -u upper='hello'          # Auto-uppercase: value stored as 'HELLO'
```

**Integer arithmetic with `-i`:**
```bash
declare -i x=10
declare -i y=3
declare -i result
result=x*y
echo "${result}"    # → 30
result=x/y
echo "${result}"    # → 3 (integer division)
```

**Read-only:**
```bash
declare -r MAX_RETRIES=5
MAX_RETRIES=10    # ❌ Error: MAX_RETRIES: readonly variable
```

---

## 23. Input with `read`

```bash
read name                               # Read a line into $name
read -p "Enter name: " name             # With a prompt
read -s -p "Password: " pass           # Silent input (no echo)
read -t 10 -p "Enter (10s timeout): "  # Timeout after 10 seconds
read -n 1 key                          # Read exactly 1 character
read -r line                           # Raw: don't interpret backslashes
read -d ':' field                      # Read until ':' delimiter

# Split input into multiple variables:
read first last                         # Split by whitespace
read -p "Enter date (d/m/y): " d m y   # Multiple fields

# Read from command output (process substitution):
read first last < <(echo "Karim Wahba")
echo "First: $first, Last: $last"

read os kernel arch < <(uname -srm)
echo "OS: $os | Kernel: $kernel | Arch: $arch"

# Read with prompt from process substitution:
read -p "Uptime → " a b c < <(uptime)
echo "Uptime parts: $a | $b | $c"
```

> ⚠️ **Pipe vs process substitution:**  
> `echo "a b" | read x y` → `$x` and `$y` are lost (pipe creates a subshell).  
> `read x y < <(echo "a b")` → `$x` and `$y` are available in current shell.

---

## 24. Script Structure & Examples

### Creating & Running Scripts

```bash
nano myscript               # Create the script file
chmod +x myscript           # Make it executable
./myscript                  # Run with explicit path
bash myscript               # Run without execute permission

# Make accessible system-wide via PATH:
mkdir -p ~/bin
mv myscript ~/bin/
PATH="$PATH:$HOME/bin"      # Add to PATH (put in ~/.bashrc for permanence)
myscript                    # Now runs from anywhere
```

---

### Script Template

> **`#!/usr/bin/env bash` — the shebang:** The first line of every script tells the OS which interpreter to use. `#!/usr/bin/env bash` is preferred over `#!/bin/bash` because `env` searches `$PATH` for `bash`, making the script work on systems where Bash isn't at `/bin/bash` (macOS with Homebrew, NixOS, etc.). Always use `env` for portability.

> **`tput` — terminal control:** `tput` queries the terminal capabilities database and returns the escape codes for colour and formatting. `tput setaf N` sets the foreground colour (1=red, 2=green, 3=yellow, 4=blue), `tput bold` enables bold text, and `tput sgr0` resets all formatting. Storing these in variables at the top of the script is cleaner than hardcoding raw escape sequences like `\033[0;31m` everywhere.

```bash
#!/usr/bin/env bash
# Description: What this script does
# Usage: ./myscript.sh [args]

set -euo pipefail           # Exit on error, unset variable, or pipe failure

# ---------- Colors ----------
red="$(tput setaf 1)"
green="$(tput setaf 2)"
blue="$(tput setaf 4)"
bold="$(tput bold)"
reset="$(tput sgr0)"
line="${blue}${bold}==============================${reset}"

# ---------- Variables ----------
declare -r SCRIPT_DIR="$(dirname "$0")"

# ---------- Main ----------
read -p "Enter your name: " username
echo "Welcome, ${green}${bold}${username}${reset}!"
echo "${line}"

echo "${red}System Uptime:${reset}"
uptime
echo "${line}"

echo "${red}Top CPU Processes:${reset}"
ps -eo pid,comm,%cpu --sort=-%cpu | head -n 6
echo "${line}"

echo "${red}Disk Usage:${reset}"
df -h | grep '^/dev/'
echo "${line}"

echo "${red}Available Memory:${reset}"
grep 'MemAvailable:' /proc/meminfo
echo "${line}"

declare -i num_files
num_files="$(ls ~/Desktop 2>/dev/null | wc -l)"
echo "Files on Desktop: ${num_files}"
```

---

## 25. Sourcing & Splitting Scripts

### `source` vs `bash` (subshell)

> **Why this matters in practice:** When you run a script with `bash script.sh` or `./script.sh`, it spawns a child process. That child gets a copy of the parent's environment, but any changes it makes (setting variables, changing directory, exporting values) exist only in the child and are lost when it exits. `source` (or its shorthand `.`) doesn't spawn anything — it reads and executes the file's lines directly in your current shell, so all side effects persist. This is essential for config files, `.env` loaders, and shared library scripts that define functions you want to call later.

```bash
# Running as subshell — variables don't come back:
bash other.sh
echo "$myvar"      # ❌ Empty — other.sh ran in its own shell

# Sourcing — runs in the CURRENT shell:
source other.sh
echo "$myvar"      # ✅ Available — same shell environment
. other.sh         # Dot is a shorthand for source
```

**Example: config file pattern:**
```bash
# ---------- config.sh ----------
DB_HOST="localhost"
DB_USER="admin"
DB_PASS="secret"

# ---------- app.sh ----------
#!/usr/bin/env bash
source config.sh
echo "Connecting to $DB_HOST as $DB_USER"
```

---

### Script PATH Installation

> **Why `~/bin` and `$PATH`?** The shell only runs commands it can find in the directories listed in `$PATH`. By convention, `~/bin` (short for `$HOME/bin`) is the standard personal scripts directory. Adding it to `$PATH` in `~/.bashrc` means the shell checks it on every new terminal session. Once your script is there and executable, you can type its name from any directory without needing `./` or a full path. The `echo '...' >> ~/.bashrc` line appends the PATH extension permanently — single quotes are used so `$HOME` is written literally into the file and expanded at runtime, not at write time.

```bash
# ---------- hello ----------
#!/usr/bin/env bash
echo "Hello, world!"
# ----------------------------

chmod +x hello
mkdir -p ~/bin
mv hello ~/bin/

# Add to PATH permanently in ~/.bashrc:
echo 'PATH="$PATH:$HOME/bin"' >> ~/.bashrc
source ~/.bashrc

hello       # Now works from anywhere
```

---

## 26. Numeric Variables & Arithmetic Operations

Bash treats all variables as strings by default. To do math, you need one of these approaches.

### Arithmetic Expansion `$(( ))`

> **How it works:** `$(( expression ))` is pure Bash — no subprocesses, no external tools. Inside the double parentheses, variable names don't need a `$` prefix (though adding one still works), and the shell evaluates the expression as integer arithmetic and substitutes the result. The `%` operator is modulo (remainder after division), which is useful for things like "every N iterations" inside loops. Remember that all division is integer — `$(( 7 / 2 ))` gives `3`, not `3.5`.

```bash
x=10
y=3

echo $(( x + y ))     # 13
echo $(( x - y ))     # 7
echo $(( x * y ))     # 30
echo $(( x / y ))     # 3  (integer division — no decimals)
echo $(( x % y ))     # 1  (modulo / remainder)
echo $(( x ** 2 ))    # 100 (exponentiation)

# Store result in a variable:
result=$(( x * y + 5 ))
echo "$result"        # 35

# Increment / Decrement:
count=0
(( count++ ))         # post-increment: count becomes 1
(( count += 5 ))      # count becomes 6
(( count-- ))         # count becomes 5
```

### `let` and `(( ))` as Commands

> **`let` vs `(( ))`:** Both evaluate arithmetic. `let` is the older form; `(( ))` is cleaner and preferred in modern scripts. The key difference from `$(( ))` is that `(( ))` is a **command** (used for its side effects and exit code), while `$(( ))` is an **expansion** (used to get the computed value into a string or variable). Use `$(( ))` when you need the result, and `(( ))` when you want a condition or side effect like incrementing.

```bash
let result=10*5
echo "$result"        # 50

(( result = 100 / 4 ))
echo "$result"        # 25

# (( )) as a condition — returns 0 (true) if result != 0:
if (( 5 > 3 )); then echo "yes"; fi
```

### Floating-Point Arithmetic with `bc`

> **What is `bc`?** `bc` (Basic Calculator) is a standalone calculator language. Since Bash only handles integers natively, you pipe a math expression as a string into `bc` and capture its output. `scale=N` sets how many decimal places to use. The `-l` flag loads `bc`'s standard math library, which adds functions like `sqrt()`, `sin()`, `cos()`, and `l()` (natural log).

```bash
echo "scale=2; 10 / 3" | bc          # 3.33
echo "scale=4; sqrt(2)" | bc -l      # 1.4142  (-l loads math library)

# Store in variable:
area=$(echo "scale=2; 3.14159 * 5 * 5" | bc)
echo "Circle area: $area"

# Real-world: calculate disk usage percentage
used=45
total=100
pct=$(echo "scale=1; $used / $total * 100" | bc)
echo "Used: ${pct}%"
```

### Numeric Variables with `declare -i`

> **`declare -i` vs `$(( ))`:** The `-i` attribute tells Bash to automatically evaluate any assignment to this variable as arithmetic — so `count+=1` works without wrapping in `$(( ))`. The trade-off is that you lose the ability to store non-numeric strings in it (they silently become `0`). In practice, `$(( ))` is more commonly used because it works on any variable without requiring pre-declaration. Use `declare -i` when you want the variable to *enforce* being numeric and benefit from the cleaner `+=` syntax in loops.

```bash
declare -i count=0
count+=1       # Works without $(( ))
count+=1
echo "$count"  # 2

# Strings assigned to -i variables become 0:
declare -i n
n="hello"
echo "$n"      # 0
```

### Real-World Example — Countdown Timer

> **The `\r` carriage-return trick:** `\r` moves the cursor back to the start of the current line without moving to the next line. Combined with `echo -ne` (no newline, interpret escapes), this overwrites the same line on every iteration — creating an in-place updating counter. The trailing spaces after `${seconds}s` erase any leftover characters from a longer previous print (e.g., `10s` → `9s ` needs to erase the old `0`). This pattern is used in progress bars and any output that should update in place rather than scroll.

```bash
#!/usr/bin/env bash
seconds=10
while (( seconds > 0 )); do
    echo -ne "\rCountdown: ${seconds}s  "
    sleep 1
    (( seconds-- ))
done
echo -e "\rDone!          "
```

---

## 27. Tests & the `if` Clause

### The `if` Statement

```bash
if [ condition ]; then
    # commands
elif [ other_condition ]; then
    # commands
else
    # fallback
fi
```

### Numeric Comparisons (inside `[ ]` or `(( ))`)

> **Why `-eq` instead of `==` in `[ ]`?** Inside `[ ]`, the shell sees everything as strings. Using `==` or `>` compares lexicographically — `"9" > "10"` is true because `"9"` sorts after `"1"` alphabetically. The `-eq`, `-lt`, `-gt` etc. operators force numeric comparison. Inside `(( ))`, the shell already knows it's doing arithmetic, so `==`, `<`, `>` work as expected. This is the main practical reason to prefer `(( ))` for numeric tests in modern scripts — the syntax matches every other programming language.

| `[ ]` operator | `(( ))` operator | Meaning |
|----------------|-----------------|---------|
| `-eq` | `==` | equal |
| `-ne` | `!=` | not equal |
| `-lt` | `<` | less than |
| `-le` | `<=` | less than or equal |
| `-gt` | `>` | greater than |
| `-ge` | `>=` | greater than or equal |

```bash
age=25

if [ "$age" -ge 18 ]; then
    echo "Adult"
fi

# Preferred modern style using (( )):
if (( age >= 18 )); then
    echo "Adult"
fi
```

### String Comparisons

> **`[ ]` vs `[[ ]]`:** `[ ]` is the original POSIX test command — it's portable but limited. `[[ ]]` is a Bash built-in keyword that adds pattern matching (`==` with wildcards), regex matching (`=~`), and safer handling of empty variables (no need to quote `$var` to avoid word-splitting). In modern Bash scripts, prefer `[[ ]]` for all string and regex tests; use `[ ]` only when writing portable POSIX shell scripts.

```bash
name="alice"

if [ "$name" = "alice" ]; then echo "Match"; fi
if [ "$name" != "bob" ]; then echo "Not bob"; fi
if [ -z "$name" ]; then echo "Empty string"; fi   # -z: zero length
if [ -n "$name" ]; then echo "Non-empty"; fi      # -n: non-zero length

# [[ ]] supports pattern matching and regex:
if [[ "$name" == a* ]]; then echo "Starts with a"; fi
if [[ "$name" =~ ^[a-z]+$ ]]; then echo "All lowercase letters"; fi
```

### File Tests

> **Key flags to memorise:** `-f` tests for a regular file (not a directory or symlink). `-d` tests for a directory. `-e` tests for existence of any filesystem object. `-s` checks that a file exists **and** is non-empty — useful before parsing a file. `-x` checks executability, not just existence — use this before trying to run a binary. `-nt` (newer than) and `-ot` (older than) compare modification timestamps between two files, useful in build scripts to skip work when sources haven't changed.

```bash
if [ -f "/etc/passwd" ]; then echo "File exists"; fi
if [ -d "/tmp" ]; then echo "Directory exists"; fi
if [ -e "/path" ]; then echo "Path exists (file or dir)"; fi
if [ -r "/etc/passwd" ]; then echo "Readable"; fi
if [ -w "/tmp/test" ]; then echo "Writable"; fi
if [ -x "/usr/bin/python3" ]; then echo "Executable"; fi
if [ -s "file.txt" ]; then echo "File is non-empty"; fi
if [ "a.txt" -nt "b.txt" ]; then echo "a.txt is newer"; fi
```

### Logical Operators

> **`&&` and `||` inside vs outside tests:** Inside `[ ]`, use `-a` for AND and `-o` for OR (but these are deprecated and error-prone with complex expressions). Inside `[[ ]]`, use `&&` and `||` directly. Outside any brackets — at the command level — `&&` means "run the next command only if this one succeeded" and `||` means "run the next command only if this one failed". This command-level behaviour is heavily used for one-liners like `mkdir dir && cd dir` or `[ -d dir ] || mkdir dir`.

```bash
# Inside [ ]:
if [ -f "file.txt" ] && [ -r "file.txt" ]; then echo "Readable file"; fi
if [ "$x" -lt 0 ] || [ "$x" -gt 100 ]; then echo "Out of range"; fi

# Inside [[ ]] — cleaner syntax:
if [[ -f "file.txt" && -r "file.txt" ]]; then echo "Readable file"; fi
if [[ "$x" -lt 0 || "$x" -gt 100 ]]; then echo "Out of range"; fi
```

### `case` — Multi-Branch Conditionals

> **`case` vs `if/elif`:** `case` is cleaner than a long chain of `if/elif` when you're comparing one variable against multiple fixed values. Each pattern ends with `)`, the commands end with `;;` (double semicolons — this is required), and `*` is the catch-all wildcard equivalent to `else`. Patterns support globs (`*.txt`), alternation with `|` (`yes|y|Y`), and character classes. `case` is also slightly faster than `if/elif` chains because the shell stops checking once a match is found.

```bash
read -p "Enter a fruit: " fruit

case "$fruit" in
    apple|Apple)
        echo "Red fruit" ;;
    banana)
        echo "Yellow fruit" ;;
    *)
        echo "Unknown fruit" ;;
esac
```

### Real-World Example — Disk Space Alert

```bash
#!/usr/bin/env bash
threshold=80
usage=$(df / | awk 'NR==2 {print $5}' | tr -d '%')

if (( usage >= threshold )); then
    echo "⚠️  ALERT: Disk usage at ${usage}% — threshold is ${threshold}%"
else
    echo "✅ Disk OK: ${usage}% used"
fi
```

### Exit Codes & `$?`

> **What are exit codes?** Every command in Linux, when it finishes, reports a number back to the shell — its **exit code**. `0` always means success. Any non-zero value (1, 2, 127, etc.) means some kind of failure. This is how `if`, `&&`, `||`, and `set -e` all work — they all read this number. `$?` is the special variable that holds the exit code of the last command that ran. You should check exit codes in any script that does something consequential (file operations, API calls, deployments).

```bash
ls /nonexistent 2>/dev/null
echo "Exit code: $?"     # 2

if grep -q "error" app.log; then
    echo "Errors found in log"
fi

# Use exit codes in your own scripts:
if ! ping -c1 8.8.8.8 &>/dev/null; then
    echo "No internet connection"
    exit 1
fi
```

---

## 28. `while` Loops

### Basic Syntax

> **How `while` evaluates its condition:** The condition is just a command — `while` checks its exit code. Exit code `0` (success) means keep looping; anything else stops the loop. This means you can use any command as the condition: `while read -r line`, `while curl -sf url`, `while pgrep myprocess`. The `[ ]` and `[[ ]]` test commands are just a common case, not the only one. `break` exits the loop immediately; `continue` skips the rest of the current iteration and jumps back to the condition check.

```bash
while [ condition ]; do
    # commands
done
```

```bash
i=1
while [ "$i" -le 5 ]; do
    echo "Iteration $i"
    (( i++ ))
done
```

### Reading a File Line by Line

This is the most common use of `while` in production scripts:

> **Why `IFS= read -r`?** By default, `read` strips leading and trailing whitespace (controlled by `IFS`, the Internal Field Separator). Setting `IFS=` (empty) before `read` prevents that stripping, preserving lines exactly as they appear. The `-r` flag tells `read` not to treat backslashes as escape characters — without it, a line like `path=C:\new\file` would silently corrupt your data. Always use both together when reading files.

```bash
while IFS= read -r line; do
    echo "Processing: $line"
done < /etc/hosts

# IFS=  preserves leading/trailing whitespace
# -r    prevents backslash interpretation
```

### Infinite Loop with Break

```bash
while true; do
    read -p "Type 'quit' to exit: " input
    if [ "$input" = "quit" ]; then
        break
    fi
    echo "You typed: $input"
done
```

### `until` Loop — Opposite of `while`

> **`until` vs `while`:** `while` runs as long as its condition is **true**. `until` runs as long as its condition is **false** — it stops when the condition *becomes* true. They're logically equivalent (`until cmd` = `while ! cmd`), but `until` often reads more naturally when you're waiting for something to succeed, like a service coming online.

Runs until the condition becomes true:

```bash
until ping -c1 8.8.8.8 &>/dev/null; do
    echo "Waiting for network..."
    sleep 5
done
echo "Network is up."
```

### Real-World Example — Retry Logic

> **The `command && break` pattern:** `curl -sf url && break` works because `&&` only executes the right side if the left side succeeds (exit code 0). So if `curl` succeeds, `break` runs and exits the loop immediately. If `curl` fails, `&&` short-circuits, `break` is skipped, and the loop continues to the next attempt. This is cleaner than wrapping the command in an `if` block. The post-loop `if (( attempt > max_attempts ))` check distinguishes "loop exited via break (success)" from "loop exhausted all attempts (failure)".

```bash
#!/usr/bin/env bash
max_attempts=5
attempt=1

while (( attempt <= max_attempts )); do
    echo "Attempt $attempt/$max_attempts..."
    curl -sf https://api.example.com/health && break
    (( attempt++ ))
    sleep 3
done

if (( attempt > max_attempts )); then
    echo "Service unreachable after $max_attempts attempts."
    exit 1
fi
```

### Real-World Example — Watch a Log File for a Pattern

> **`[[ "$line" == *"$pattern"* ]]` — glob substring match:** Inside `[[ ]]`, `==` supports shell glob patterns. Wrapping the variable in `*...*` creates a "contains" check — this line matches if `$pattern` appears anywhere in `$line`. This is faster than running `echo "$line" | grep` for every line, since it's a pure shell operation with no subprocess. The `$pattern` variable is quoted to prevent glob characters inside it from being interpreted — without the quotes, a pattern like `ERR*R` would be treated as a glob rather than a literal string.

```bash
#!/usr/bin/env bash
logfile="/var/log/app.log"
pattern="ERROR"

tail -f "$logfile" | while IFS= read -r line; do
    if [[ "$line" == *"$pattern"* ]]; then
        echo "$(date '+%F %T') — ERROR DETECTED: $line" | tee -a /var/log/alerts.log
    fi
done
```

---

## 29. `for` Loops

### Basic Syntax

> **How `for` works:** `for item in list` iterates over a whitespace-separated list of words, assigning each one to `item` in turn. The list can be literal words, a glob (`*.log`), a variable expansion, brace expansion (`{1..10}`), or command substitution (`$(cmd)`). Always quote `"${array[@]}"` when iterating arrays — unquoted, elements containing spaces would be split into separate words. `break` exits the loop; `continue` skips to the next item.

```bash
for item in list; do
    # commands
done
```

### Looping Over a List

> **Brace expansion `{start..end..step}`:** This is a pure shell feature — no subprocesses. `{1..5}` expands to `1 2 3 4 5` before the command runs. The optional third number is the step: `{0..20..5}` gives `0 5 10 15 20`. Brace expansion happens before glob expansion and variable expansion, so `{1..$n}` does **not** work (use `seq` or a C-style loop for dynamic ranges).

```bash
for color in red green blue; do
    echo "Color: $color"
done

# Brace expansion:
for i in {1..5}; do echo "Item $i"; done

# With step:
for i in {0..20..5}; do echo "$i"; done     # 0 5 10 15 20
```

### C-Style `for` Loop

> **When to use C-style:** The `(( init; condition; step ))` form is best when you need a numeric counter with a dynamic range — especially when the end value is stored in a variable (which brace expansion can't handle). It's also clearer when the step is not 1, or when you need to count down. The three sections (init, test, increment) work exactly like C/Java: `i=0; i<5; i++`.

```bash
for (( i=0; i<5; i++ )); do
    echo "i = $i"
done
```

### Looping Over Files

> **How glob expansion works in `for`:** When Bash sees `for f in /var/log/*.log`, it expands the glob *before* the loop starts, replacing `*.log` with a space-separated list of all matching paths. Each path becomes one iteration, including the full path — so `$logfile` will be `/var/log/syslog`, not just `syslog`. If the directory doesn't exist or the pattern matches nothing, see the `nullglob` note below. Never parse `ls` output to loop over files — always use globs directly, which handle spaces in filenames correctly (as long as you double-quote `"$f"`).

```bash
# Process all .log files in a directory:
for logfile in /var/log/*.log; do
    echo "Checking: $logfile"
    wc -l "$logfile"
done

# Safely loop even when glob matches nothing:
# Without nullglob, if no *.tmp files exist, Bash passes the literal string "*.tmp"
# as the loop variable — your loop body runs once with a non-existent filename.
# shopt -s nullglob makes globs that match nothing expand to zero words instead.
shopt -s nullglob
for f in /tmp/*.tmp; do
    rm -v "$f"
done
shopt -u nullglob     # Turn it back off to avoid unexpected behaviour elsewhere
```

### Looping Over Command Output

> **Word splitting danger with `for $(cmd)`:** When you write `for x in $(cmd)`, Bash runs `cmd`, then splits its entire output on any whitespace (spaces, tabs, newlines) to form the list. This works fine when output items never contain spaces — like usernames or process IDs. But it silently breaks on filenames with spaces, multi-word values, or output with irregular spacing. The `while IFS= read -r` pattern below avoids this entirely by processing one complete line at a time, making it the safe default for any output you don't fully control.

```bash
# Loop over users in /etc/passwd:
for user in $(cut -d: -f1 /etc/passwd); do
    echo "User: $user"
done

# Safer — use while + process substitution for lines with spaces:
while IFS= read -r user; do
    echo "User: $user"
done < <(cut -d: -f1 /etc/passwd)
```

### Real-World Example — Batch File Rename

> **The `[ -f "$file" ] || continue` guard:** When a glob like `*.jpeg` matches nothing, Bash (without `nullglob`) passes the literal string `"*.jpeg"` as the loop variable. The `-f` test checks that the path is actually an existing regular file — if it's the unexpanded literal, the test fails and `continue` skips to the next iteration cleanly, preventing the script from trying to rename a non-existent file. This one-liner guard is a safe alternative to `shopt -s nullglob` when you only need to protect a single loop.

```bash
#!/usr/bin/env bash
# Rename all .jpeg files to .jpg
for file in *.jpeg; do
    [ -f "$file" ] || continue          # Skip if no match
    newname="${file%.jpeg}.jpg"
    mv -v "$file" "$newname"
done
```

### Real-World Example — Health Check Multiple Servers

> **`ping -c1 -W2`:** `-c1` sends exactly one packet (without it, `ping` runs forever). `-W2` sets a 2-second timeout for the reply — without it, an unreachable host would stall the loop for the OS default (which can be 10–30 seconds per host). `&>/dev/null` suppresses both stdout and stderr so the script output stays clean. This combination gives you a fast, reliable reachability check per host. For HTTP health checks, swap `ping` for `curl -sf --max-time 2 http://$server/health`.

```bash
#!/usr/bin/env bash
servers=("web-01" "web-02" "db-01" "cache-01")

for server in "${servers[@]}"; do
    if ping -c1 -W2 "$server" &>/dev/null; then
        echo "✅ $server — reachable"
    else
        echo "❌ $server — UNREACHABLE"
    fi
done
```

---

## 30. Accessing External Data — JSON & APIs

### Fetching Data with `curl`

> **`curl` flags you'll use every time:** `-s` suppresses the progress bar (essential in scripts). `-f` makes curl return a non-zero exit code on HTTP errors (without it, curl exits 0 even on a 404 or 500). `-o` saves to a file. `-w` lets you append extra info like the HTTP status code to the output. Always combine `-sf` as a minimum when calling APIs in scripts so errors propagate properly.

```bash
# Basic GET request:
curl https://api.example.com/data

# Save to file:
curl -o output.json https://api.example.com/data

# Silent mode (no progress bar):
curl -s https://api.example.com/data

# With headers and auth:
curl -s -H "Authorization: Bearer $TOKEN" https://api.example.com/users

# POST with JSON body:
curl -s -X POST \
  -H "Content-Type: application/json" \
  -d '{"name":"alice","role":"admin"}' \
  https://api.example.com/users

# Check HTTP status code only:
curl -s -o /dev/null -w "%{http_code}" https://api.example.com/health
```

### Parsing JSON with `jq`

> **What is `jq`?** `jq` is a command-line JSON processor. It reads JSON from stdin (or a file) and lets you query, filter, and transform it using its own small query language. Think of it as `grep` and `awk` combined, but purpose-built for JSON. The `-r` (raw output) flag removes the surrounding quotes from strings — always use it when you want to store a value in a shell variable. Without `-r`, strings come back like `"alice"` with quotes included.

`jq` is the standard CLI tool for working with JSON in scripts.

```bash
# Install:
sudo apt install jq          # Debian/Ubuntu
sudo dnf install jq          # RHEL/Fedora

# Parse a field:
curl -s https://api.github.com/users/octocat | jq '.name'

# Multiple fields:
curl -s https://api.github.com/users/octocat | jq '{name: .name, repos: .public_repos}'

# Array element:
echo '[{"id":1},{"id":2}]' | jq '.[0].id'       # 1

# All elements of an array:
echo '[{"name":"a"},{"name":"b"}]' | jq '.[].name'

# Filter array by condition:
echo '[{"status":"ok"},{"status":"fail"}]' | jq '.[] | select(.status == "fail")'

# Extract to plain strings (strip quotes):
curl -s https://api.github.com/users/octocat | jq -r '.login'

# Iterate and format:
curl -s https://api.github.com/orgs/github/repos | jq -r '.[].full_name'
```

### Storing API Data in Variables

> **Capture once, query multiple times:** Rather than making a separate `curl` call for each field, capture the full JSON response once into a variable and run multiple `jq` queries against it. This reduces network calls, is faster, and ensures all fields come from the same snapshot of the data. The `-r` flag on `jq` strips the surrounding quotes from string values — without it, `temp` would be `"25"` (with literal quote characters), breaking any numeric comparison later.

```bash
#!/usr/bin/env bash
response=$(curl -s "https://wttr.in/Cairo?format=j1")

temp=$(echo "$response" | jq -r '.current_condition[0].temp_C')
desc=$(echo "$response" | jq -r '.current_condition[0].weatherDesc[0].value')

echo "Cairo: ${temp}°C — ${desc}"
```

### Real-World Example — Poll an API Until Ready

> **`case` for API status polling:** Using `case` to branch on the API's status string is cleaner than nested `if/elif` and handles an open-ended set of in-progress states (like `"pending"`, `"running"`, `"queued"`) via the `*` catch-all — all of which sleep and retry without needing to enumerate every possible value. The `break` in the success branch exits the `while true` loop; `exit 1` in the failure branch exits the entire script. This pattern applies to any long-running async job: database migrations, CI builds, cloud resource provisioning, etc.

```bash
#!/usr/bin/env bash
job_id="abc123"
api="https://api.example.com/jobs/$job_id"

echo "Waiting for job to complete..."
while true; do
    status=$(curl -s -H "Authorization: Bearer $TOKEN" "$api" | jq -r '.status')
    case "$status" in
        "completed") echo "✅ Job done!"; break ;;
        "failed")    echo "❌ Job failed!"; exit 1 ;;
        *)           echo "Status: $status — waiting..."; sleep 5 ;;
    esac
done
```

### Handling API Errors

> **The `-w "\n%{http_code}"` trick:** By default, `curl -s` gives you the body with no indication of whether the request succeeded. Adding `-w "\n%{http_code}"` appends the HTTP status code on a new line at the very end of the output. You can then split on that last line to get the code separately from the body. This is the standard pattern for checking API responses in scripts without making two separate requests.

```bash
#!/usr/bin/env bash
response=$(curl -s -w "\n%{http_code}" https://api.example.com/data)
http_code=$(echo "$response" | tail -1)
body=$(echo "$response" | head -n -1)

if [ "$http_code" != "200" ]; then
    echo "API error: HTTP $http_code"
    echo "Body: $body"
    exit 1
fi

echo "$body" | jq .
```

---

## 31. Functions

### Defining and Calling Functions

> **Function fundamentals:** In Bash, functions must be defined *before* they are called — the shell reads the file top to bottom. Inside a function, `$1`, `$2`, `$@`, and `$#` refer to the function's own arguments, completely independently from the script's arguments. Functions share the script's variable scope by default — any variable you set inside a function is visible outside it unless you declare it `local`. There is no function signature or type declaration; a function is simply a named group of commands.

```bash
greet() {
    echo "Hello, $1!"          # $1 is the first argument passed to the function
}

greet "Alice"    # → Hello, Alice!
greet "Bob"      # → Hello, Bob!
```

### Function Arguments

```bash
add_numbers() {
    local a="$1"
    local b="$2"
    echo $(( a + b ))
}

result=$(add_numbers 10 25)
echo "Result: $result"    # 35
```

> Always use `local` for function variables. Without it, variables are global and can clobber script state.

### Return Values

> **Why `return` doesn't return data:** Bash's `return` statement only sets the exit code (0–255) — it's not like `return` in Python or JavaScript. The three patterns below are the standard workarounds. Pattern 1 (stdout capture) is the most common and cleanest — call the function inside `$()` to capture what it prints. Pattern 2 (global variable) avoids a subshell but pollutes global scope; use an uppercase convention to make it obvious. Pattern 3 (exit code only) is perfect for boolean checks — the function itself becomes a test condition you can use directly in `if` or `&&`.

Bash functions don't return data — they return exit codes (0–255). Use `echo` or global variables to pass data back:

```bash
# Pattern 1: capture stdout
get_user_count() {
    wc -l < /etc/passwd
}
count=$(get_user_count)
echo "Users: $count"

# Pattern 2: global variable
get_hostname() {
    RESULT=$(hostname -f)
}
get_hostname
echo "Host: $RESULT"

# Pattern 3: return exit code for success/failure
file_exists() {
    [ -f "$1" ]   # returns 0 if true, 1 if false
}

if file_exists "/etc/passwd"; then
    echo "Found it"
fi
```

### Script Structuring with Functions

> **`$*` vs `$@`:** Both refer to all arguments passed to a function or script. The difference matters when they're quoted: `"$*"` joins all arguments into one single string (separated by the first character of `IFS`). `"$@"` keeps each argument as a separate quoted word — preserving arguments that contain spaces. Always use `"$@"` when forwarding arguments, looping, or passing them to other commands.

```bash
#!/usr/bin/env bash
set -euo pipefail

# ---------- Functions ----------

log_info()  { echo "[INFO]  $*"; }
log_warn()  { echo "[WARN]  $*" >&2; }
log_error() { echo "[ERROR] $*" >&2; }

check_dependencies() {
    local deps=("curl" "jq" "awk")
    for dep in "${deps[@]}"; do
        if ! command -v "$dep" &>/dev/null; then
            log_error "Missing dependency: $dep"
            exit 1
        fi
    done
    log_info "All dependencies found."
}

usage() {
    echo "Usage: $(basename "$0") [--dry-run] <target>"
    exit 0
}

cleanup() {
    log_info "Cleaning up..."
    rm -f /tmp/script_tmp_$$
}

# ---------- Main ----------
trap cleanup EXIT          # Always run cleanup on exit

check_dependencies
log_info "Starting script..."
```

### Real-World Example — Reusable Logging Library

> **The `local level="$1"; shift` pattern:** This is a common function design: consume the first argument into a named variable, then call `shift` to drop it from the argument list. After the shift, `$*` (or `$@`) contains all *remaining* arguments — in this case, the log message. This avoids needing to pass the message as a single pre-quoted string; the caller can write `log_info "Deployed" "$APP_NAME" "to" "$ENV"` and all words are joined naturally. The internal `_log` helper keeps the logging logic in one place; the public `log_info`, `log_warn`, `log_error` wrappers are thin and readable.

```bash
# ---------- lib/logging.sh ----------
LOG_FILE="${LOG_FILE:-/var/log/app.log}"

_log() {
    local level="$1"; shift
    local msg="$*"
    local ts
    ts=$(date '+%Y-%m-%d %H:%M:%S')
    echo "[$ts] [$level] $msg" | tee -a "$LOG_FILE"
}

log_info()  { _log "INFO"  "$@"; }
log_warn()  { _log "WARN"  "$@"; }
log_error() { _log "ERROR" "$@"; }

# ---------- app.sh ----------
#!/usr/bin/env bash
source lib/logging.sh

log_info "App started"
log_warn "Low memory warning"
log_error "Connection failed"
```

---

## 32. Arguments In Bash

### Positional Parameters

> **The special variables:** `$0` is the script's name as it was invoked — useful for usage messages. `$1`–`$9` are the individual arguments; use `${10}` and beyond with braces for double-digit positions. `$#` is the argument count — check it at the top of your script to validate the user passed the right number. `$@` expands to all arguments as separate quoted words — this is what you use when forwarding arguments or looping. Never use `$*` unquoted; it collapses all arguments into one string and breaks on spaces.

```bash
#!/usr/bin/env bash
# Usage: ./deploy.sh myapp production

script_name="$0"       # Script name
app="$1"               # First argument
env="$2"               # Second argument
all_args="$@"          # All arguments as separate words
arg_count="$#"         # Number of arguments

echo "Deploying '$app' to '$env'"
echo "Total args: $arg_count"

# Loop over all arguments:
for arg in "$@"; do
    echo "Arg: $arg"
done
```

### Shifting Arguments

```bash
#!/usr/bin/env bash
while [ "$#" -gt 0 ]; do
    echo "Processing: $1"
    shift         # Drop $1, shift remaining args left
done
```

### Parsing Flags with `getopts`

> **How `getopts` works:** The format string `"s:d:vh"` defines the accepted flags. A letter followed by `:` (like `s:`) means that flag **requires an argument** — `getopts` puts that argument into `$OPTARG`. A letter without `:` (like `v`, `h`) is a boolean flag with no argument. The `?` case catches any unrecognised option. `getopts` only handles single-character flags like `-s` and `-v`; for long flags like `--env` you need the manual while-loop approach shown below.

```bash
#!/usr/bin/env bash
# Usage: ./backup.sh -s /source -d /dest -v

source_dir=""
dest_dir=""
verbose=false

while getopts "s:d:vh" opt; do
    case "$opt" in
        s) source_dir="$OPTARG" ;;
        d) dest_dir="$OPTARG" ;;
        v) verbose=true ;;
        h) echo "Usage: $0 -s <src> -d <dest> [-v]"; exit 0 ;;
        ?) echo "Unknown option"; exit 1 ;;
    esac
done

if [ -z "$source_dir" ] || [ -z "$dest_dir" ]; then
    echo "Error: -s and -d are required"
    exit 1
fi

$verbose && echo "Verbose mode on"
echo "Backing up $source_dir → $dest_dir"
```

### Parsing Long Flags Manually

> **How the `while [[ $# -gt 0 ]]; case` pattern works:** This is the standard idiom for parsing `--long-flags`. Each iteration looks at `$1` (the current first argument). `shift` at the end of the loop body drops `$1` and slides everything left. When a flag takes a value (like `--env staging`), you read `$2` into the variable and call `shift` *again* inside that branch to skip over the value before the outer `shift` runs. The `--env=*` alternative handles the `--env=value` form using parameter expansion to strip the `--env=` prefix. This pattern naturally handles any mix of flags and their order.

```bash
#!/usr/bin/env bash
dry_run=false
env=""

while [[ "$#" -gt 0 ]]; do
    case "$1" in
        --dry-run)   dry_run=true ;;
        --env)       env="$2"; shift ;;
        --env=*)     env="${1#--env=}" ;;
        -h|--help)   echo "Usage: $0 [--dry-run] [--env ENV]"; exit 0 ;;
        *)           echo "Unknown: $1"; exit 1 ;;
    esac
    shift
done

echo "Dry run: $dry_run | Env: $env"
```

### Default Values for Arguments

> **Parameter expansion defaults:** `${var:-default}` is Bash's built-in way to supply a fallback. It reads as "use `$var` if it's set and non-empty, otherwise use `default`". The `:` makes it also trigger on empty strings (not just unset variables). A related form, `${var:?message}`, aborts the script with an error message if the variable is unset — useful for mandatory parameters (you'll see this in the deployment example below as `"${1:?Usage: ...}"`).

```bash
env="${1:-production}"          # Use "production" if $1 is unset or empty
port="${PORT:-8080}"            # Use env var PORT, default to 8080
log_level="${LOG_LEVEL:-info}"
```

---

## 33. Arrays in Bash

### Indexed Arrays

> **Key syntax patterns:** Always quote `"${arr[@]}"` (with double quotes) when iterating or passing an array — unquoted, elements with spaces split into separate words. `${#arr[@]}` gives the element count. `${!arr[@]}` gives the list of *indices* (useful when you need the index inside the loop). Arrays in Bash are **sparse** — after `unset 'arr[1]'`, indices 0 and 2 both still exist; the count drops by 1 but index 2 doesn't become index 1. This catches people out when they assume arrays are always contiguous. `+=` on an array appends element(s) to the end.

```bash
# Declaration and assignment:
fruits=("apple" "banana" "cherry")
fruits[3]="date"

# Access:
echo "${fruits[0]}"          # apple
echo "${fruits[@]}"          # all elements
echo "${#fruits[@]}"         # count: 4
echo "${fruits[@]:1:2}"      # slice: banana cherry

# Append:
fruits+=("elderberry")

# Loop:
for fruit in "${fruits[@]}"; do
    echo "$fruit"
done

# Loop with index:
for i in "${!fruits[@]}"; do
    echo "$i: ${fruits[$i]}"
done

# Delete element:
unset 'fruits[1]'            # Removes banana (index stays sparse)
```

### Associative Arrays (Dictionaries)

> **`declare -A` is mandatory:** Unlike indexed arrays (which Bash creates automatically), associative arrays *must* be declared with `declare -A` before use. Without it, Bash silently treats the variable as an indexed array and all your string keys get coerced to `0`. Key access uses the same `${arr[key]}` syntax, but `${!arr[@]}` gives you the keys, not numeric indices. Note that associative arrays have **no guaranteed ordering** — when you loop over `${!config[@]}`, keys come back in an arbitrary order, not insertion order.

```bash
declare -A config
config["host"]="localhost"
config["port"]="5432"
config["db"]="myapp"

echo "${config["host"]}"         # localhost
echo "${!config[@]}"             # all keys
echo "${config[@]}"              # all values

# Loop:
for key in "${!config[@]}"; do
    echo "$key = ${config[$key]}"
done
```

### Real-World Example — Service Health Check with Arrays

> **`ss -tlnp` — socket status:** `ss` (socket statistics) is the modern replacement for `netstat`. The flags mean: `-t` (TCP only), `-l` (listening sockets only), `-n` (numeric ports, don't resolve service names), `-p` (show the process using each socket). Piping to `grep -q ":${port} "` checks silently whether anything is listening on that port — the trailing space after the port number prevents `:5432` from matching `:54321`. This is more reliable than checking `pgrep` by name when a service might be listening on a non-standard port.

```bash
#!/usr/bin/env bash
declare -A services
services["nginx"]="80"
services["postgres"]="5432"
services["redis"]="6379"

for svc in "${!services[@]}"; do
    port="${services[$svc]}"
    if ss -tlnp | grep -q ":${port} "; then
        echo "✅ $svc (port $port) — running"
    else
        echo "❌ $svc (port $port) — NOT running"
    fi
done
```

### Building Arrays from Command Output

> **`mapfile` (also called `readarray`):** This built-in reads lines from stdin into an indexed array. It's the right tool whenever you want to capture multi-line command output into an array. The `-t` flag strips the trailing newline from each element (without it, every array element would end with `\n`). It's safer than `arr=($(command))` because it handles filenames with spaces correctly.

```bash
# All .log files into an array:
mapfile -t logfiles < <(find /var/log -name "*.log" -type f)
echo "Found ${#logfiles[@]} log files"

# Split a CSV line into an array:
IFS=',' read -r -a fields <<< "alice,admin,active"
echo "User: ${fields[0]}, Role: ${fields[1]}"

# All running container IDs:
mapfile -t containers < <(docker ps -q 2>/dev/null)
```

---

## 34. Effective Data Filtering with `grep` & Regular Expressions

### `grep` Essentials

> **Flags worth memorising for production use:** `-A/-B/-C` (after/before/context) are indispensable when debugging — errors in logs rarely happen alone; seeing the surrounding lines tells you *why*. `-l` lists filenames only, not matches — perfect for "which config files mention this hostname?". `-w` prevents partial matches: `grep "fail" log` would also match "failure"; `-w` requires word boundaries. `-F` treats the pattern as a literal string with no regex interpretation — much faster when searching for fixed strings like URLs or stack trace lines that contain `.`, `(`, `[` which are regex metacharacters. `-q` (quiet) suppresses all output and just sets the exit code — use it in `if grep -q "pattern" file` conditions.

```bash
grep "error" app.log                    # Lines containing 'error'
grep -i "error" app.log                 # Case-insensitive
grep -n "error" app.log                 # Show line numbers
grep -v "debug" app.log                 # Exclude lines with 'debug'
grep -c "error" app.log                 # Count matching lines
grep -l "error" /var/log/*.log          # List files that match
grep -r "TODO" ./src/                   # Recursive search
grep -A 3 "ERROR" app.log              # 3 lines after match
grep -B 2 "ERROR" app.log              # 2 lines before match
grep -C 2 "ERROR" app.log              # 2 lines before AND after
grep -w "fail" app.log                  # Whole word match
grep -F "literal.string" file.txt       # Fixed string, no regex
```

### Basic Regular Expressions

> **The most important regex concepts for log work:** `^` and `$` are anchors — they match a *position* (start/end of line), not a character. `.` matches any single character; to match a literal dot, escape it as `\.`. The `*` quantifier means "zero or more of the preceding atom" — so `.*` matches anything (including nothing). In BRE, `[0-9]` is a character class; `[^0-9]` is its negation. These few primitives cover 90% of real-world `grep` usage. For `+` (one or more), `?` (zero or one), or `{n,m}` (exact counts), you need `-E` (extended regex).

| Pattern | Meaning |
|---------|---------|
| `.` | Any single character |
| `*` | Zero or more of the preceding |
| `^` | Start of line |
| `$` | End of line |
| `[abc]` | Any of a, b, c |
| `[^abc]` | NOT a, b, or c |
| `[a-z]` | Character range |

```bash
grep "^Error" app.log          # Lines that START with "Error"
grep "\.log$" filelist.txt     # Lines that END with ".log"
grep "^$" file.txt             # Empty lines
grep "[0-9]" file.txt          # Lines with any digit
grep "^[A-Z]" file.txt         # Lines starting with uppercase
```

### Extended Regex with `grep -E` (or `egrep`)

> **Basic vs Extended Regex:** By default `grep` uses Basic Regular Expressions (BRE), where `+`, `?`, `|`, `{`, `(` are treated as literal characters unless backslash-escaped. `grep -E` switches to Extended Regular Expressions (ERE), where those characters work as operators without escaping. In practice, always use `grep -E` when your pattern uses `+`, `|`, `{n,m}`, or `()` grouping — it's far more readable. The `-o` flag (only) prints only the matched portion of the line rather than the whole line, which is essential for extraction tasks like pulling IPs or status codes from logs.

```bash
grep -E "error|warning|critical" app.log    # Match any of the three
grep -E "[0-9]{1,3}\.[0-9]{1,3}" file.txt   # IP-like patterns
grep -E "^(GET|POST|PUT) " access.log       # HTTP methods
grep -E "\.jpg|\.png|\.gif" urls.txt        # Image URLs
grep -E "[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}" contacts.txt   # Email addresses
```

### Real-World Log Analysis

```bash
#!/usr/bin/env bash
logfile="/var/log/nginx/access.log"

echo "=== Top 10 IPs ==="
grep -oE '^[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+' "$logfile" | sort | uniq -c | sort -rn | head -10

echo "=== HTTP Errors ==="
grep -E '" [45][0-9]{2} ' "$logfile" | grep -oE '"[45][0-9]{2}"' | sort | uniq -c | sort -rn

echo "=== Recent 404s ==="
grep '" 404 ' "$logfile" | tail -20 | awk '{print $7}'
```

### `sed` — Stream Editor

> **What is `sed`?** `sed` processes text line by line, applying editing commands to each line as it passes through. The most common use is `s/old/new/` (substitute). The `-i` flag edits the file in-place rather than printing to stdout — this is how you update config files in deployment scripts without a temp file. Always test your `sed` command without `-i` first to confirm it does what you expect.

```bash
# Replace first occurrence per line:
sed 's/old/new/' file.txt

# Replace ALL occurrences:
sed 's/old/new/g' file.txt

# In-place edit (modifies file directly):
sed -i 's/localhost/db.internal/g' config.cfg

# Delete lines matching a pattern:
sed '/^#/d' config.cfg          # Remove comment lines
sed '/^$/d' file.txt            # Remove empty lines

# Print specific lines:
sed -n '5,10p' file.txt         # Lines 5 to 10
sed -n '/START/,/END/p' file.txt  # Between two patterns

# Add a line before/after a match:
sed '/\[database\]/a host=localhost' config.ini

# Real-world: update a version string:
sed -i "s/VERSION=.*/VERSION=${new_version}/" .env
```

### `awk` — Field Processor

> **`awk` built-in variables:** `NR` (Number of Records) is the current line number — `NR==2` means "only line 2". `NF` (Number of Fields) is the count of fields on the current line — `NF > 0` skips empty lines. `$0` is the entire line; `$1`, `$2`, etc. are individual fields. The `END` block runs once after all lines are processed, making it useful for totals and summaries. The `$5+0` trick forces a field to be treated as a number — `awk` compares strings by default, so `"9" > "80"` would be true lexicographically; adding `+0` forces numeric comparison.

```bash
# Default field separator is whitespace:
awk '{print $1}' file.txt          # First field of each line
awk '{print $1, $3}' file.txt      # First and third fields
awk 'NR==2' file.txt               # Print only line 2
awk 'NF > 0' file.txt              # Skip empty lines
awk '{sum += $1} END {print sum}' numbers.txt   # Sum a column

# Custom delimiter:
awk -F: '{print $1}' /etc/passwd   # Print usernames
awk -F, '{print $2}' data.csv      # Second CSV column

# With condition:
awk -F: '$3 >= 1000 {print $1}' /etc/passwd    # Regular users only
awk '$5 > 80 {print $1, "HIGH CPU"}' procs.txt

# Real-world: disk usage alert:
df -h | awk 'NR>1 && $5+0 > 80 {print "⚠️ " $6 " at " $5}'
```

---

## 35. File Handling — Reading, Writing & Parsing

### Reading Files

> **`$(<file.txt)` vs `$(cat file.txt)`:** Both read a file into a variable, but `$(<file)` is a Bash built-in that avoids spawning a `cat` subprocess. It's faster and preferred. Note that command substitution strips trailing newlines — this is usually fine for config values, but matters if your file ends with deliberate blank lines.

```bash
# Line by line (safest for all content):
while IFS= read -r line; do
    echo "$line"
done < input.txt

# Read entire file into a variable:
content=$(<file.txt)

# Read into an array (one line per element):
mapfile -t lines < file.txt
echo "Lines: ${#lines[@]}"
echo "First: ${lines[0]}"

# Read specific field from CSV:
while IFS=',' read -r name email role; do
    echo "Name=$name | Email=$email | Role=$role"
done < users.csv
```

### Writing Files

> **Heredoc `<< EOF`:** A heredoc lets you feed multiple lines of text into a command without creating a separate file. Everything between `<< EOF` and the closing `EOF` (on its own line, no leading spaces) is passed as stdin to the command. Use `<< 'EOF'` (quoted) if you want the content treated literally — no variable expansion. Use `<<- EOF` (with a dash) to allow indentation with tabs (useful for readability inside functions).

```bash
# Overwrite:
echo "Hello" > output.txt

# Append:
echo "$(date) — Script started" >> app.log

# Multi-line with heredoc:
cat > /etc/app/config.conf << EOF
host=localhost
port=5432
user=appuser
EOF

# Write from variable:
config="key=value"
printf '%s\n' "$config" > config.txt
```

### Parsing Config Files

> **`set -a` (allexport):** Normally, variables you define in a shell are not automatically exported to child processes. `set -a` turns on "auto-export" — every variable assignment after it is automatically exported. Combined with `source .env`, this loads all variables from the file directly into the environment, making them available to any subprocesses or commands launched by the script. `set +a` turns auto-export back off. This is a common pattern in Docker entrypoints and CI scripts.

> **`declare "$key=$value"` trick:** When parsing `KEY=VALUE` files line by line, `declare "$key=$value"` dynamically creates a shell variable named whatever `$key` is. It's a clean alternative to `eval` for this specific use case, but be cautious — only use it with files you control, as a malicious value like `key='x=$(rm -rf /)'` could be dangerous.

```bash
# Parse KEY=VALUE files:
while IFS='=' read -r key value; do
    [[ "$key" =~ ^# ]] && continue    # Skip comments
    [[ -z "$key" ]] && continue        # Skip empty lines
    declare "$key=$value"
done < config.env

echo "$DB_HOST"    # If config.env had DB_HOST=localhost

# Safer: source a .env file (only if trusted):
set -a; source .env; set +a
```

### File Permissions in Scripts

> **`install -d` vs `mkdir -p`:** Both create directories, but `install -d` lets you set the owner, group, and permissions in a single atomic command with `-m` (mode), `-o` (owner), and `-g` (group). Using `mkdir -p` followed by `chmod` and `chown` is three separate steps — if the script is interrupted between them, you can end up with a directory at the wrong permission level. `install -d` is the preferred approach in deployment scripts.

```bash
# Check before acting:
if [ ! -r "$file" ]; then
    echo "Cannot read: $file"
    exit 1
fi

# Set permissions on created files:
touch /etc/app/secret.conf
chmod 600 /etc/app/secret.conf     # Owner read/write only
chown appuser:appgroup /etc/app/secret.conf

# Create directories with correct permissions:
install -d -m 750 -o appuser -g appgroup /opt/myapp/data
```

---

## 36. Error Handling & Debugging

### `set` Options — Safer Scripts

> **What each flag does:** `set -e` makes the script exit immediately if any command returns a non-zero exit code, instead of silently continuing. Without it, a failed `cp` or `curl` is ignored and the script keeps running with corrupt state. `set -u` makes using an unset variable an error — without it, `$TYPO` silently expands to an empty string, causing subtle bugs. `set -o pipefail` changes pipeline behaviour: normally `cmd1 | cmd2` only checks `cmd2`'s exit code; with `pipefail` the pipeline fails if **any** command in it fails. Together these three form the standard safety header for any production script.

```bash
set -e          # Exit immediately on any error
set -u          # Treat unset variables as errors
set -o pipefail # Catch errors in pipelines (e.g., cmd1 | cmd2)
set -x          # Debug mode — print each command before running

# Combined (put at top of every production script):
set -euo pipefail
```

### `trap` — Catch Errors and Signals

> **What is `trap`?** `trap` registers a command to run automatically when your script receives a signal or hits a special condition. The most important pseudo-signals are: `EXIT` (runs on any exit, normal or error — use for guaranteed cleanup), `ERR` (runs when any command fails — use for error reporting), `INT` (Ctrl+C), and `TERM` (kill signal). You can have multiple `trap` lines for different events.

> **`$$` — Current script's PID:** Every process has a unique Process ID. `$$` expands to the PID of the currently running shell. Using `$$` in temp file names like `/tmp/myscript_$$_tmp` ensures each script run gets its own uniquely named temp files, preventing collisions if the script runs in parallel.

> **`mktemp`:** Creates a temporary file (or directory with `-d`) with a unique name and returns its path. The `XXXXXX` in the template is replaced with random characters. Always use `mktemp` rather than constructing temp file paths manually — it handles race conditions and ensures the file is created securely before anything else can claim that name.

> **`$LINENO`:** A special variable that expands to the current line number in the script. Passing it to an error handler tells you exactly where the failure occurred without having to add explicit line numbers in your code.

```bash
#!/usr/bin/env bash
set -euo pipefail

cleanup() {
    echo "Cleaning up temp files..."
    rm -f /tmp/myscript_$$_*
}

on_error() {
    local line="$1"
    echo "❌ Script failed at line $line"
    cleanup
    exit 1
}

trap 'on_error $LINENO' ERR    # Run on_error when any command fails
trap 'cleanup' EXIT            # Always run cleanup on exit
trap 'echo "Interrupted!"; exit 130' INT   # Handle Ctrl+C

# Create a temp file safely:
tmpfile=$(mktemp /tmp/myscript_$$.XXXXXX)
echo "Using temp file: $tmpfile"
```

### Defensive Scripting Patterns

> **`${!1}` — Indirect variable expansion:** When you write `${!varname}`, Bash treats the value of `$varname` as a variable name and expands *that* variable. So if `$1` is the string `"DB_HOST"`, then `${!1}` expands to the value of `$DB_HOST`. This is how `require_var` below can check any variable by name without using `eval`.

> **`command -v` vs `which`:** `command -v` is a Bash built-in that returns the path or type of a command and exits 0 if found, non-zero if not. `which` is an external program that only searches `$PATH` and doesn't know about shell functions or aliases. `command -v` is the correct, portable way to check for tool availability in scripts.

> **`$EUID` — Effective User ID:** This is the numeric ID of the user the script is running as. Root always has EUID `0`. Checking `$EUID` is the reliable way to guard a script against being run as the wrong user — more reliable than checking `$USER` (which is just a string and can be spoofed).

```bash
# Fail fast with a meaningful message:
require_var() {
    [ -n "${!1}" ] || { echo "Required variable '$1' is not set."; exit 1; }
}
require_var DB_HOST
require_var DB_PASSWORD

# Check command exists:
require_cmd() {
    command -v "$1" &>/dev/null || { echo "Required command '$1' not found."; exit 1; }
}
require_cmd curl
require_cmd jq

# Safe directory change:
cd /var/app/data || { echo "Cannot enter /var/app/data"; exit 1; }

# Guard against running as root (or require it):
if [ "$EUID" -eq 0 ]; then
    echo "Do not run as root"; exit 1
fi
# Or: require root:
[ "$EUID" -eq 0 ] || { echo "Must run as root"; exit 1; }
```

### Debugging Techniques

> **`PS4` — Debug prompt:** When you run `set -x`, Bash prints each command prefixed by `PS4` (default is `+`). By customising `PS4` to include `${BASH_SOURCE}` (filename), `${LINENO}` (line number), and `${FUNCNAME[0]}` (current function name), the debug output shows exactly where in your script each command is being executed — far more useful than the default `+` prefix.

> **`shellcheck`:** A static analysis tool that reads your script without running it and flags common mistakes: unquoted variables, deprecated syntax, unreachable code, wrong test operators, and more. It's the single most effective tool for catching bugs before they hit production. Run it on every script before deploying.

```bash
# Run script in debug mode:
bash -x myscript.sh

# Debug specific sections only:
set -x
risky_command arg1 arg2
set +x

# Print variable state:
echo "DEBUG: var='$var', count=$count" >&2

# Use PS4 for better debug output:
export PS4='+(${BASH_SOURCE}:${LINENO}): ${FUNCNAME[0]:+${FUNCNAME[0]}(): }'
set -x

# shellcheck — static analysis for scripts:
# sudo apt install shellcheck
shellcheck myscript.sh
```

---

## 37. User Interaction — `select`, Menus & Input

### `select` — Simple Numbered Menus

> **How `select` works:** `select` automatically numbers your list, prints it, and prompts for input using `PS3` (the select prompt variable — set this before using `select`, as the default `#?` is unhelpful). The user's raw input is stored in `$REPLY` and the chosen word is stored in the loop variable (`$choice` below). If the user enters an invalid number, the loop variable is empty but `$REPLY` still has whatever they typed. `select` loops forever until you `break` out of it.

```bash
#!/usr/bin/env bash
PS3="Choose an option: "    # Prompt for select

select choice in "Deploy" "Rollback" "Status" "Quit"; do
    case "$REPLY" in
        1) echo "Deploying..."; break ;;
        2) echo "Rolling back..."; break ;;
        3) systemctl status myapp; ;;
        4) break ;;
        *) echo "Invalid option" ;;
    esac
done
```

### Confirmation Prompts

> **Function as a test condition:** The `confirm` function ends with `[[ "$response" =~ ^[Yy]$ ]]` — not an `if` statement or `echo`, just the test expression on its own. Since the test expression is the last command in the function, its exit code (0 for match, 1 for no match) becomes the function's exit code. This means `if confirm "..."` works directly in the caller — the function *is* the condition. The regex `^[Yy]$` anchors with `^` and `$` so only a single `y` or `Y` is accepted; `yes`, `yeah`, or an empty Enter all correctly return false.

```bash
confirm() {
    local prompt="${1:-Are you sure?}"
    read -r -p "$prompt [y/N] " response
    [[ "$response" =~ ^[Yy]$ ]]
}

if confirm "Delete all temp files?"; then
    rm -rf /tmp/myapp_*
    echo "Deleted."
else
    echo "Cancelled."
fi
```

### Reading Passwords Safely

> **Why the bare `echo` after `-s`:** `read -s` suppresses echoing so the user's keystrokes are invisible. But it also suppresses the newline — when the user hits Enter, the terminal cursor stays at the end of the prompt line. A bare `echo` (with no arguments) simply prints a newline, moving the cursor to a fresh line. Without it, the next thing your script prints would appear on the same line as the password prompt, making output look garbled. The function then returns the password via `echo "$pass"`, captured by the caller with `$()` — the command substitution strips the trailing newline, giving you a clean string.

```bash
read_password() {
    local pass
    read -r -s -p "Enter password: " pass
    echo                        # Move to new line (echo was suppressed)
    echo "$pass"
}

password=$(read_password)
```

### Interactive Menu with Validation

> **`while true` + `case` — the menu loop pattern:** This is the standard shell menu structure. `while true` loops forever; `exit 0` inside the quit branch is the only clean exit path. The `*` catch-all in `case` handles invalid input without crashing, printing a warning and looping back to the menu. The `ps aux --sort=-%cpu` sorts processes by CPU usage in descending order (`-` prefix = reverse) — the `%` before `cpu` is just part of the column name format `ps` uses, not a modulo operator. This pattern is widely used in maintenance scripts, server administration tools, and deployment helpers where you want a repeating interactive interface.

```bash
#!/usr/bin/env bash
while true; do
    echo ""
    echo "=== Main Menu ==="
    echo "1) Show disk usage"
    echo "2) Show memory"
    echo "3) List processes"
    echo "4) Exit"
    echo ""
    read -r -p "Select [1-4]: " choice

    case "$choice" in
        1) df -h ;;
        2) free -h ;;
        3) ps aux --sort=-%cpu | head -10 ;;
        4) echo "Goodbye!"; exit 0 ;;
        *) echo "⚠️  Invalid choice. Try again." ;;
    esac
done
```

---

## 38. Working with Processes, Logs & System Operations

### Process Management in Scripts

> **`pgrep -x` — exact name match:** `pgrep` searches running processes by name. Without `-x`, `pgrep nginx` would also match `nginx-debug` or `php-nginx`. The `-x` flag requires the full process name to match exactly. Use `pgrep -x` in scripts to avoid false positives.

> **`$!` — Last background process PID:** When you run a command with `&`, Bash immediately stores its PID in `$!`. Capture it right after the `&` if you need to track or wait on that specific process. `wait <PID>` then blocks until that process finishes and captures its exit code in `$?`.

> **`SIGTERM` vs `SIGKILL`:** `SIGTERM` (signal 15) is the polite shutdown signal — it asks the process to clean up and exit gracefully. Well-behaved processes catch `SIGTERM` to close files, finish in-progress work, and exit cleanly. `SIGKILL` (signal 9) is a forced kill by the kernel — the process has no chance to clean up. Always try `SIGTERM` first and give the process a few seconds before escalating to `SIGKILL`.

```bash
# Check if a process is running:
if pgrep -x "nginx" &>/dev/null; then
    echo "nginx is running"
fi

# Get PID of a process:
pid=$(pgrep -x "myapp")
echo "PID: $pid"

# Kill a process by name:
pkill -SIGTERM myapp        # Graceful shutdown
pkill -SIGKILL myapp        # Force kill

# Wait for a background process:
long_running_task &
bg_pid=$!
echo "Started PID $bg_pid"
wait "$bg_pid"
echo "Task done, exit code: $?"

# Run tasks in parallel:
for host in web-01 web-02 web-03; do
    ssh "$host" "sudo systemctl restart nginx" &
done
wait
echo "All restarts done."
```

### Working with Systemd in Scripts

> **`is-active` and `is-enabled` as exit-code commands:** `systemctl is-active nginx` exits `0` if the service is running, non-zero otherwise. `systemctl is-enabled nginx` exits `0` if the service is set to start at boot. The `--quiet` flag suppresses the output (the printed status word) so only the exit code matters — exactly what you need in an `if` condition or a `&&`/`||` chain. This lets you write idempotent service management: "start it only if it's not already running", or "enable it only if not already enabled" — without noisy output or false failures.

```bash
start_service() {
    local svc="$1"
    systemctl is-active --quiet "$svc" && { echo "$svc already running"; return; }
    systemctl start "$svc" && echo "$svc started" || { echo "Failed to start $svc"; exit 1; }
}

ensure_enabled() {
    local svc="$1"
    systemctl is-enabled --quiet "$svc" || systemctl enable "$svc"
}

start_service nginx
ensure_enabled nginx
```

### Log Rotation and Management

> **`stat -c%s`:** `stat` reports detailed file metadata. The `-c` flag lets you specify a format string, and `%s` means "file size in bytes". This is the reliable way to check a file's size in a script without parsing `ls -l` output (which is fragile). On macOS the equivalent is `stat -f%z`.

> **`grep --line-buffered`:** By default, `grep` buffers its output — it waits to accumulate data before printing, which causes long delays when piped from `tail -f` or `journalctl -f`. The `--line-buffered` flag flushes output after every line, making filtered live log streams work in real time.

```bash
# Rotate a log file manually in scripts:
rotate_log() {
    local logfile="$1"
    local maxsize=10485760    # 10MB in bytes

    if [ -f "$logfile" ] && [ "$(stat -c%s "$logfile")" -gt "$maxsize" ]; then
        mv "$logfile" "${logfile}.$(date +%Y%m%d_%H%M%S).bak"
        touch "$logfile"
        echo "Log rotated: $logfile"
    fi
}

rotate_log /var/log/myapp.log

# Parse systemd journal in scripts:
journalctl -u nginx --since "1 hour ago" --no-pager -q | grep "error"

# Watch for new log entries matching a pattern:
journalctl -fu myapp | grep --line-buffered "CRITICAL" | while IFS= read -r line; do
    echo "$line" >> /var/log/alerts.log
done
```

---

## 39. Automation Tasks — DevOps Patterns

### Downloading Files with `wget`

> **`wget` vs `curl` for downloading:** Both can download files, but `wget` is simpler for the common case. Key practical flags: `-q` suppresses all progress output (use in scripts to avoid cluttered logs). `-O` renames the output file — without it, `wget` uses the filename from the URL. `-c` resumes a partial download by checking what's already been received and requesting only the remaining bytes (uses the `Range` HTTP header). `-P` sets the download directory. For scripts that need to check HTTP status codes or pass custom headers, `curl` is more flexible; for simple reliable file downloads, `wget` is the cleaner choice.

```bash
# Basic download:
wget https://example.com/file.tar.gz

# Save with custom name:
wget -O myapp.tar.gz https://releases.example.com/v1.2.3/app.tar.gz

# Quiet mode (no output):
wget -q https://example.com/file.tar.gz

# Resume interrupted download:
wget -c https://example.com/bigfile.iso

# Download to a directory:
wget -P /opt/downloads/ https://example.com/file.tar.gz

# Multiple files from a list:
wget -i urls.txt
```

### Verifying File Integrity with Checksums

> **Why checksums matter in scripts:** When you download a release tarball and immediately install it, you're trusting the network connection. A checksum (SHA-256 hash) is a fingerprint of a file's exact contents — even a single flipped bit produces a completely different hash. Software releases always publish a `.sha256` file alongside the tarball. `sha256sum -c` reads that file, recomputes the hash of the local file, and compares them — printing `OK` or `FAILED`. In a deployment script running `set -euo pipefail`, a failed `sha256sum -c` will abort the whole script before the corrupted archive is ever installed. Always verify before extracting.

```bash
# Generate a checksum:
sha256sum file.tar.gz > file.tar.gz.sha256

# Verify:
sha256sum -c file.tar.gz.sha256     # OK or FAILED

# Inline verification in scripts:
expected="abc123def456..."
actual=$(sha256sum file.tar.gz | awk '{print $1}')

if [ "$actual" = "$expected" ]; then
    echo "✅ Checksum verified"
else
    echo "❌ Checksum MISMATCH — aborting"
    exit 1
fi
```

### Deployment Script Pattern

> **`tar -xzf ... --strip-components=1`:** Archives typically contain a top-level directory named after the release (e.g., `myapp-1.2.3/`). Without `--strip-components=1`, extracting into `/opt/myapp` would create `/opt/myapp/myapp-1.2.3/bin`, etc. With it, the top-level directory is stripped and files land directly in the target — so your paths are always `/opt/myapp/bin` regardless of version number.

> **`cp -a` for backups:** The `-a` (archive) flag is equivalent to `-rpd`: recursive, preserve all metadata (permissions, timestamps, ownership), and preserve symlinks. It's the correct flag when you want an exact copy that behaves identically to the original, not just a copy of the bytes.

> **`mktemp -d`:** Creates a temporary *directory* (not just a file) with a unique name. Using a temp directory as a staging area for downloads and extraction keeps your work isolated and ensures cleanup via `trap 'rm -rf "$tmpdir"' EXIT` removes everything automatically, even if the script fails halfway through.

```bash
#!/usr/bin/env bash
set -euo pipefail

APP_NAME="myapp"
RELEASE_URL="https://releases.example.com/${APP_NAME}-${VERSION}.tar.gz"
INSTALL_DIR="/opt/${APP_NAME}"
BACKUP_DIR="/opt/backups/${APP_NAME}"
CHECKSUM_URL="${RELEASE_URL}.sha256"

log() { echo "[$(date '+%H:%M:%S')] $*"; }

log "Starting deployment of $APP_NAME v${VERSION}"

# Download release
tmpdir=$(mktemp -d)
trap 'rm -rf "$tmpdir"' EXIT

log "Downloading release..."
wget -q -P "$tmpdir" "$RELEASE_URL" "$CHECKSUM_URL"

# Verify checksum
log "Verifying checksum..."
cd "$tmpdir"
sha256sum -c "$(basename "$CHECKSUM_URL")" || { log "Checksum failed!"; exit 1; }

# Backup current
if [ -d "$INSTALL_DIR" ]; then
    log "Backing up current installation..."
    mkdir -p "$BACKUP_DIR"
    cp -a "$INSTALL_DIR" "${BACKUP_DIR}/$(date +%Y%m%d_%H%M%S)"
fi

# Install
log "Installing..."
tar -xzf "$(basename "$RELEASE_URL")" -C "$INSTALL_DIR" --strip-components=1

# Restart service
log "Restarting service..."
systemctl restart "$APP_NAME"
systemctl is-active --quiet "$APP_NAME" && log "✅ Deployment successful" || { log "❌ Service failed to start"; exit 1; }
```

### Backup Script with Archiving and Compression

> **`tar -C "$(dirname ...)" "$(basename ...)"` — clean archive paths:** If you run `tar -czf backup.tar.gz /opt/myapp`, the archive contains the full absolute path `/opt/myapp/...` and extracting it recreates that exact path. By using `-C "$(dirname "$SOURCE_DIR")"` to change into the parent directory first, then passing just `"$(basename "$SOURCE_DIR")"` as the target, the archive stores only the relative path `myapp/...`. This means you can extract the backup anywhere without it imposing absolute paths. The `${1:?message}` syntax on the first two lines is a built-in self-documenting mandatory argument — if the user doesn't pass the argument, the script immediately aborts with the usage message.

```bash
#!/usr/bin/env bash
set -euo pipefail

SOURCE_DIR="${1:?Usage: $0 <source_dir> <dest_dir>}"
DEST_DIR="${2:?Usage: $0 <source_dir> <dest_dir>}"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
ARCHIVE_NAME="backup_$(basename "$SOURCE_DIR")_${TIMESTAMP}.tar.gz"
KEEP_DAYS=7

mkdir -p "$DEST_DIR"

echo "Backing up $SOURCE_DIR → $DEST_DIR/$ARCHIVE_NAME"
tar -czf "${DEST_DIR}/${ARCHIVE_NAME}" -C "$(dirname "$SOURCE_DIR")" "$(basename "$SOURCE_DIR")"

echo "Backup size: $(du -sh "${DEST_DIR}/${ARCHIVE_NAME}" | cut -f1)"

# Remove backups older than KEEP_DAYS:
find "$DEST_DIR" -name "backup_*.tar.gz" -mtime "+${KEEP_DAYS}" -delete
echo "Old backups cleaned up (kept last ${KEEP_DAYS} days)."
```

### Scheduling with Cron (from Scripts)

> **Idempotent cron injection:** The pattern `crontab -l 2>/dev/null | grep -qF "$command"` checks whether the job already exists before adding it. `crontab -l` lists current jobs; `grep -qF` searches silently for the exact string. If found, the `||` short-circuits and nothing changes. If not found, a new crontab is created by printing the existing entries plus the new line and piping into `crontab -`. Running this script multiple times never creates duplicate entries.

> **`find -mtime +N`:** The `+N` means "more than N days old". `find -mtime +7` finds files modified more than 7 days ago. `-mtime 7` (no `+`) means exactly 7 days. `-mtime -7` means less than 7 days (i.e., modified in the last week). This is the standard pattern for time-based cleanup in backup and log management scripts.

```bash
# Add a cron job from within a script (idempotent):
add_cron_job() {
    local schedule="$1"
    local command="$2"
    local job="$schedule $command"

    # Only add if it doesn't already exist:
    crontab -l 2>/dev/null | grep -qF "$command" || \
        (crontab -l 2>/dev/null; echo "$job") | crontab -
}

add_cron_job "0 2 * * *" "/opt/myapp/scripts/backup.sh"
add_cron_job "*/5 * * * *" "/opt/myapp/scripts/health_check.sh"
```

---

## 40. Advanced Bash Features

### Subshells and Command Groups

> **`( )` subshell vs `{ }` command group:** Both group commands together, but they differ in scope. A subshell `( )` spawns a child shell — any variable changes, `cd`, or `set` options inside it are completely isolated and do not affect the parent script. A command group `{ }` runs in the **current** shell — it shares the same variables and environment. Use a subshell when you want true isolation (e.g., temporarily changing directory without affecting the rest of the script). Use a command group when you want to redirect or pipe the combined output of several commands without isolation overhead. Note that `{ }` requires a space after `{` and a semicolon before `}`.

```bash
# Subshell — changes don't affect parent:
(
    cd /tmp
    ls
)   # Back in original directory

# Command group — runs in current shell:
{
    echo "Step 1"
    echo "Step 2"
} | tee output.log

# Capture a group's output:
result=$( { command1; command2; } )
```

### Process Substitution

> **How `<( )` works:** Process substitution makes a command's output appear as a temporary file-like path (usually `/dev/fd/63` or `/proc/self/fd/63`). This lets you pass the output of a command anywhere a filename is expected. The most important use is feeding output into a `while read` loop: `while ... done < <(cmd)` runs `cmd` in a subshell but the `while` loop runs in the **current** shell, so any variables you set inside the loop are available afterwards. Compare this to `cmd | while ... done`, where the loop runs in a subshell and all variables are lost when it ends.

```bash
# Feed command output as a file to another command:
diff <(sort file1.txt) <(sort file2.txt)

# Compare two command outputs:
diff <(ssh server1 'cat /etc/hosts') <(ssh server2 'cat /etc/hosts')

# Avoid subshell issue with pipes:
while IFS= read -r line; do
    echo "Got: $line"
done < <(find /var/log -name "*.log")
```

### `eval` — Use Sparingly

> **Why `eval` is dangerous:** `eval` takes a string and executes it as a shell command. The risk is that if any part of that string comes from user input or an untrusted source, an attacker can inject arbitrary commands. For example, if `$cmd` is `"rm -rf /"` from user input, `eval "$cmd"` will execute it without question. The array alternative below is safe because Bash treats each element as a literal argument, with no re-parsing or injection risk. Only use `eval` when you genuinely need dynamic variable name expansion and `${!var}` or `declare -n` won't work.

```bash
# Build and run commands dynamically (only with trusted input):
cmd="echo hello"
eval "$cmd"

# Better alternative — store in array:
cmd_parts=(echo hello world)
"${cmd_parts[@]}"
```

### String Manipulation

> **Parameter expansion operators — the memory trick:** The `#` and `%` operators strip from the start and end of a string respectively. One `#` or `%` = shortest match (non-greedy). Two `##` or `%%` = longest match (greedy). Think of it as: `#` strips a **prefix** (front), `%` strips a **suffix** (end). The pattern after the operator is a glob, not a regex. This is all done by the shell with zero subprocesses — much faster than piping through `sed` or `cut` inside a loop, and works on every system without installing anything. The path-parsing example at the bottom (`dir`, `base`, `ext`, `name`) is a pattern used constantly in real scripts.

```bash
str="Hello, World!"

echo "${#str}"              # Length: 13
echo "${str,,}"             # Lowercase: hello, world!
echo "${str^^}"             # Uppercase: HELLO, WORLD!
echo "${str:7:5}"           # Substring: World
echo "${str/World/Bash}"    # Replace first: Hello, Bash!
echo "${str//l/L}"          # Replace all: HeLLo, WorLd!

# Remove prefix/suffix:
filename="archive.tar.gz"
echo "${filename%.gz}"      # archive.tar    (remove shortest .gz suffix)
echo "${filename%%.*}"      # archive        (remove longest .* suffix)
echo "${filename#*.}"       # tar.gz         (remove shortest prefix up to .)
echo "${filename##*.}"      # gz             (remove longest prefix up to .)

# Real use:
path="/var/log/app.log"
dir="${path%/*}"             # /var/log
base="${path##*/}"           # app.log
ext="${base##*.}"            # log
name="${base%.*}"            # app
```

### Here Strings

> **`<<<` — here string:** A here string feeds a single string directly into a command's stdin, without needing `echo` and a pipe. `cmd <<< "text"` is equivalent to `echo "text" | cmd` but cleaner and avoids a subshell. It's particularly useful with `read` to split a string into variables or an array in the current shell (piping into `read` would create a subshell and lose the variables). The string undergoes normal Bash expansion — variable references, command substitution, and arithmetic all work inside it.

```bash
# Feed a string directly into stdin without echo + pipe:
wc -w <<< "hello world bash"        # 3

# Read from a variable:
data="apple,banana,cherry"
IFS=',' read -r -a fruits <<< "$data"
echo "${fruits[1]}"                  # banana
```

### Nameref Variables (Indirect References)

> **`declare -n` — nameref:** A nameref (name reference) makes one variable act as an alias for another. Wherever you use the nameref, Bash transparently reads or writes the target variable instead. The killer use case is **passing arrays to functions**: Bash can't pass arrays as arguments directly (only their elements as separate words). With namerefs, you pass the array's *name* as a string argument, then inside the function declare a local nameref pointing to it — giving you full read/write access to the original array by reference. This is the idiomatic Bash solution for what other languages solve with pass-by-reference.

```bash
# Reference another variable by name:
declare -n ref=fruits
echo "${ref[0]}"        # Same as echo "${fruits[0]}"

# Useful in functions to pass arrays by name:
print_array() {
    declare -n arr="$1"
    for item in "${arr[@]}"; do
        echo "  - $item"
    done
}

servers=("web-01" "web-02" "db-01")
print_array servers
```

### Regular Expressions in `[[ ]]`

> **`=~` and `BASH_REMATCH`:** The `=~` operator inside `[[ ]]` matches the left side against an Extended Regular Expression on the right. Do **not** quote the regex — quoting forces literal string matching. When a match succeeds, Bash populates the `BASH_REMATCH` array: index `0` is the full match, index `1` is the first capture group `( )`, index `2` is the second, and so on. This lets you extract specific parts of a string without running `sed` or `awk`.

> **POSIX character classes `[[:space:]]`, `[[:digit:]]`, etc.:** Inside `[[ =~ ]]`, you can use POSIX named character classes. These are locale-aware and more portable than raw ranges. Common ones: `[[:space:]]` (whitespace), `[[:digit:]]` (0–9), `[[:alpha:]]` (letters), `[[:alnum:]]` (letters and digits), `[[:upper:]]`, `[[:lower:]]`.

```bash
input="user@example.com"

if [[ "$input" =~ ^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$ ]]; then
    echo "Valid email"
fi

# Capture groups via BASH_REMATCH:
log_line="2024-03-15 ERROR connection refused"
if [[ "$log_line" =~ ([0-9-]+)[[:space:]]([A-Z]+)[[:space:]](.+) ]]; then
    date="${BASH_REMATCH[1]}"
    level="${BASH_REMATCH[2]}"
    message="${BASH_REMATCH[3]}"
    echo "Date=$date | Level=$level | Msg=$message"
fi
```

### `xargs` — Build Commands from Input

> **What `xargs` does:** `xargs` reads items from stdin (one per line, or separated by whitespace by default) and builds a command line from them. Without any flags it appends all items to the end of the command at once. The `-I{}` flag switches to one-item-at-a-time mode with a placeholder: everywhere `{}` appears in the command template, `xargs` substitutes the current item. `-P N` runs up to N copies of the command simultaneously — this is an easy way to parallelise tasks like downloading or scanning multiple hosts without writing complex background-process management. Combine `find` with `xargs` instead of `find -exec` when you need parallelism or when the file list is very large (avoids "argument list too long" errors).

```bash
# Delete files from a list:
cat files_to_delete.txt | xargs rm -f

# Run a command for each input with replacement:
cat servers.txt | xargs -I{} ssh {} 'uptime'

# Parallel execution:
cat urls.txt | xargs -P 4 -I{} wget -q {}     # 4 simultaneous downloads

# Combine with find:
find /var/log -name "*.log" -mtime +30 | xargs gzip     # Compress old logs
find . -name "*.sh" | xargs chmod +x                    # Make all scripts executable
```

### `tee` for Parallel Processing

> **`tee` as a pipeline splitter:** A pipe is linear — data flows from left to right and you can only read it once. `tee` breaks that constraint by writing its input to **both** a file and stdout simultaneously, letting the pipeline continue. This means you can save a copy of intermediate data mid-pipeline for debugging or archiving while the rest of the pipeline keeps processing. With multiple filenames, `tee` writes to all of them at once — useful for sending a report to a file, a backup location, and a downstream command all in one go. The `-a` flag appends instead of overwriting.

```bash
# Write to a file AND continue the pipeline:
cat big_file.txt | tee /tmp/raw_copy.txt | grep "ERROR" > errors.txt

# Write to multiple files at once:
generate_report | tee report.txt report_backup.txt | mail -s "Report" ops@company.com
```

### Complete Production Script Template

> **`${BASH_SOURCE[0]}` vs `$0`:** `$0` is the name used to invoke the script, which changes depending on how it's called (e.g., `./deploy.sh`, `bash deploy.sh`, or sourced). `${BASH_SOURCE[0]}` always gives the actual path of the file being executed, even when sourced. Combined with `cd "$(dirname ...)" && pwd`, this reliably resolves the absolute directory of the script itself — so relative paths inside the script work regardless of where the user calls it from.

> **`readonly` vs `declare -r`:** They're equivalent. `readonly VAR=value` is the traditional POSIX form; `declare -r VAR=value` is the Bash-specific form. Either prevents the variable from being reassigned or unset later in the script. Using `readonly` (or `declare -r`) for script-level constants like `SCRIPT_DIR` and `TIMESTAMP` makes their intent clear and prevents accidental overwriting.

> **The `run()` dry-run wrapper:** Wrapping all destructive commands in a `run()` function that checks a `DRY_RUN` flag is one of the most practical patterns in deployment scripting. It lets you test the full logic of a script — argument parsing, validation, loop iterations, conditional branches — and see exactly what it *would* do, without actually touching any files or services. Add this pattern to any script that modifies system state.

> **`2>/dev/null || true`:** In a `set -e` script, a command that exits non-zero will abort the whole script. Sometimes you deliberately want to attempt something that might fail without caring about the failure (e.g., stopping a service that might not be running yet). Appending `|| true` forces the overall expression to return exit code 0 regardless, telling Bash "this failure is expected and acceptable".

```bash
#!/usr/bin/env bash
# =============================================================
# Script: deploy.sh
# Description: Deploy application to target environment
# Usage: ./deploy.sh --env <production|staging> --version <ver>
# =============================================================
set -euo pipefail

# ---------- Constants ----------
readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
readonly SCRIPT_NAME="$(basename "$0")"
readonly TIMESTAMP="$(date +%Y%m%d_%H%M%S)"
readonly LOG_FILE="/var/log/deploys/${SCRIPT_NAME}_${TIMESTAMP}.log"

# ---------- Defaults ----------
ENV=""
VERSION=""
DRY_RUN=false

# ---------- Logging ----------
log() { echo "[$(date '+%H:%M:%S')] $*" | tee -a "$LOG_FILE"; }
die() { echo "❌ [ERROR] $*" | tee -a "$LOG_FILE" >&2; exit 1; }

# ---------- Argument Parsing ----------
usage() {
    echo "Usage: $SCRIPT_NAME --env <env> --version <ver> [--dry-run]"
    exit 0
}

while [[ "$#" -gt 0 ]]; do
    case "$1" in
        --env)       ENV="$2";     shift ;;
        --version)   VERSION="$2"; shift ;;
        --dry-run)   DRY_RUN=true ;;
        -h|--help)   usage ;;
        *)           die "Unknown argument: $1" ;;
    esac
    shift
done

# ---------- Validation ----------
[[ -n "$ENV" ]]     || die "--env is required"
[[ -n "$VERSION" ]] || die "--version is required"
[[ "$ENV" =~ ^(production|staging)$ ]] || die "Invalid env: $ENV"

command -v curl &>/dev/null || die "curl is required"
command -v jq   &>/dev/null || die "jq is required"

# ---------- Cleanup ----------
cleanup() {
    log "Cleaning up..."
    rm -f /tmp/deploy_$$.* 2>/dev/null || true
}
trap cleanup EXIT
trap 'die "Script interrupted"' INT TERM

# ---------- Functions ----------
run() {
    if $DRY_RUN; then
        log "[DRY-RUN] Would run: $*"
    else
        log "Running: $*"
        "$@"
    fi
}

health_check() {
    local url="$1"
    local max_wait=60
    local waited=0

    log "Waiting for service at $url..."
    until curl -sf "$url" &>/dev/null; do
        (( waited += 5 ))
        (( waited > max_wait )) && die "Service did not come up after ${max_wait}s"
        sleep 5
    done
    log "✅ Service is healthy"
}

# ---------- Main ----------
mkdir -p "$(dirname "$LOG_FILE")"
log "=== Deployment started: env=$ENV version=$VERSION dry_run=$DRY_RUN ==="

run systemctl stop myapp || true
run tar -xzf "/releases/myapp-${VERSION}.tar.gz" -C /opt/myapp
run systemctl start myapp

health_check "http://localhost:8080/health"

log "=== ✅ Deployment complete ==="
