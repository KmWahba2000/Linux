# Linux & Bash — RHEL Command Reference

## Table of Contents

### Part I — Bash CLI
1. [File Management](#1-file-management)
2. [Text File Operations](#2-text-file-operations)
3. [Redirection](#3-redirection)
4. [Pipes & Filters](#4-pipes--filters)
5. [Environment Variables](#5-environment-variables)
6. [Shell Expansions](#6-shell-expansions)

### Part II — Linux System
7. [The Filesystem & Devices](#7-the-filesystem--devices)
8. [User & Group Management](#8-user--group-management)
9. [File Permissions & Ownership](#9-file-permissions--ownership)
10. [Processes](#10-processes)
11. [Job Control](#11-job-control)
12. [Archives & Compression](#12-archives--compression)
13. [Package Management](#13-package-management)
14. [Boot Process & Systemd](#14-boot-process--systemd)
15. [Storage — Partitions, Mounts & LVM](#15-storage--partitions-mounts--lvm)
16. [System Upgrade & Recovery](#16-system-upgrade--recovery)
17. [Cron Jobs](#17-cron-jobs)
18. [Networking](#18-networking)
19. [Firewall (firewalld)](#19-firewall-firewalld)
20. [SELinux](#20-selinux)

---

# Part I — Bash CLI

## 1. File Management

### Output & Printing — `echo`

```bash
echo -e 'Hello\nworld'     # -e: interpret escape sequences (\n = newline)
echo -n 'Hello'            # -n: suppress trailing newline
```

---

### Navigation

```bash
cd ~/projects        # Navigate to a directory (~ = home directory)
cd ..                # Go up one level
cd -                 # Go back to the previous directory ($OLDPWD)
cd /                 # Go to filesystem root
pwd                  # Print current working directory
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
touch ~/new/{a..c}.txt                  # Brace expansion: creates a.txt, b.txt, c.txt
```

> `touch` also updates the modification timestamp on an existing file without changing its content.

---

### Copying — `cp`

```bash
cp pages/page1.html pages/page2.html    # Copy file to a new name
cp file.txt ~/backup/                   # Copy file to another directory
cp -r folder/ backup/                   # Copy directory recursively
cp -a folder/ backup/                   # Archive copy: preserves permissions, timestamps, symlinks
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
cat < in.txt > out.txt      # Redirect input from in.txt and output to out.txt
```

---

## 4. Pipes & Filters

A **pipe** (`|`) connects the stdout of one command to the stdin of the next.

### Basic Pipes

```bash
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
ping -c 4 google.com 2>&1 | tee ping.txt       # Save and display ping results
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

ip addr show | grep -F 'inet'                  # Show only IP address lines
ps -ef | grep 'nginx' | grep -v grep           # Find process, exclude grep itself
```

---

### `tr` — Translate / Transform Characters

```bash
echo 'awesome' | tr 'a-z' 'A-Z'     # Convert to uppercase → AWESOME
echo 'hello   world' | tr -s ' '    # Squeeze multiple spaces to one
echo 'hello world' | tr -d ' '      # Delete all spaces → helloworld
```

---

### `cut` — Extract Fields or Characters

```bash
cut -d: -f1 /etc/passwd     # Extract usernames (first field, colon-delimited)
cut -d: -f1,3 /etc/passwd   # Extract fields 1 and 3
uptime | cut -c 1-10        # First 10 characters
```

> Use `-c` for characters, `-f` for fields with a delimiter (`-d`).

---

### `sed` — Stream Editor

```bash
sed 's/old/new/' file.txt               # Replace first occurrence on each line (stdout only)
sed 's/old/new/g' file.txt              # Replace ALL occurrences per line (global)
sed -i 's/old/new/g' file.txt           # In-place edit: modify the original file directly
sed 's/old/new/g' file.txt > new.txt    # Replace and save to a new file
sed -n '5,10p' file.txt                 # Print only lines 5 through 10
sed '/pattern/d' file.txt               # Delete lines matching pattern
```

**`sed` substitution syntax:** `s/search/replace/flags`

| Substitution flag | Meaning |
|-------------------|---------|
| `g` | Global: replace all matches on each line |
| `i` | Case-insensitive match |

> `-i` is a **`sed` command-line option** (not a substitution flag): it edits the file in place instead of printing to stdout.

---

## 5. Environment Variables

### Viewing Variables

```bash
env                         # List ALL environment variables
printenv HOME               # Print a single variable
echo "${USER}"              # Current logged-in username
echo "${HOME}"              # Home directory path
echo "${PWD}"               # Current working directory
echo "${PATH}"              # Directories searched for commands
echo "${SHELL}"             # Path to the current shell
echo "${HOSTNAME}"          # Machine hostname
```

---

### Setting & Removing Variables

```bash
MY_VAR='hello'              # Assign shell variable (local to current shell)
export MY_VAR='hello'       # Export as environment variable (available to child processes)
export CITY='Cairo'         # Create and export in one step
unset CITY                  # Remove variable entirely
MY_VAR='value' python3 script.py    # Inline: set variable only for that one command
```

---

### PATH Management

```bash
echo "${PATH}"                          # View current PATH
PATH="${PATH}:/home/user/bin"           # Append custom directory to PATH (current session only)
which cat                               # Show full path of a command (e.g. /usr/bin/cat)
type ls                                 # Show whether command is built-in or external
```

---

### Shell Configuration Files

| File | When it's loaded |
|------|-----------------|
| `~/.bashrc` | Every interactive non-login shell (most common for customizations) |
| `~/.bash_profile` | Login shells (terminal login, SSH) |
| `/etc/profile` | System-wide login shell config |
| `/etc/profile.d/*.sh` | System-wide drop-in scripts (preferred for custom env vars on RHEL) |

```bash
vi ~/.bashrc                # Edit your personal shell config
source ~/.bashrc            # Reload without reopening terminal
. ~/.bashrc                 # Shorthand for source

# Example additions to ~/.bashrc:
export MY_TOKEN='value'
PATH="${PATH}:/usr/local/myapp/bin"
alias gohome='cd ~'
```

---

### Aliases

```bash
alias ll='ls -alh'              # Shortcut for a long listing
alias ..='cd ..'                # Navigate up one level quickly
alias gohome='cd ~'             # Create a temporary alias (no spaces around =)

alias gohome                    # Check what an alias expands to
unalias gohome                  # Remove an alias (current session only)

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
shopt -s cdspell    # Auto-correct small typos in directory names

# Change default shell (takes effect on next login):
chsh -s /bin/bash
cat /etc/shells     # View all available shells installed on the system
```

---

## 6. Shell Expansions

### Globbing & Path Expansion

```bash
ls *                            # All files/directories in current directory
echo *.txt                      # All .txt files
echo ?.txt                      # Single-character name + .txt (e.g. a.txt)
ls *[0-9]*                      # Files with a digit in their name
```

---

### Variable Expansion

```bash
echo "${HOME}"                  # Value of HOME
echo "${#HOME}"                 # Length of HOME (character count)
echo "${HOME:1:10}"             # Substring: 10 characters starting at position 1
echo "${HOME//home/Users}"      # Replace all 'home' with 'Users'
echo "${VAR:-default}"          # Use 'default' if VAR is unset or empty
echo "${VAR:=default}"          # Assign 'default' to VAR if unset, then use it
```

---

### Brace Expansion

```bash
echo data.{csv,txt}             # → data.csv data.txt
echo {1..5}                     # → 1 2 3 4 5
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

# Part II — Linux System

## 7. The Filesystem & Devices

### Symbolic & Hard Links

```bash
# Symbolic (soft) link — pointer to a path:
ln -s /etc/nginx/nginx.conf /tmp/nginx.conf     # Absolute path symlink
readlink -f ./link                              # Show the real absolute path a symlink points to

# Hard link — second name for the same inode (same file data):
ln file.txt hardlink.txt                        # Both names point to the same data on disk
```

| | Symlink | Hard link |
|--|---------|-----------|
| Spans filesystems? | ✅ Yes | ❌ No |
| Works on directories? | ✅ Yes | ❌ No (usually) |
| Broken if target deleted? | ✅ Yes (dangling) | ❌ No (data persists) |

---

### Disk Usage & Inodes

```bash
df -h               # Disk space usage (human-readable)
df -Th              # Disk space with filesystem type
df -ih              # Inode usage

du -h file.txt      # Size of a single file
du -sh folder/      # Summary size of a directory
du -sh *            # Size of each item in current directory
```

---

### /proc Virtual Filesystem

`/proc` is not a real disk directory — it's a live window into the running kernel.

```bash
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
lsblk                       # List block devices (disks, partitions)
lsblk -f                    # Include filesystem type and UUID
ls -l /dev/sda              # Info about the sda disk device
dd if=/dev/urandom of=~/random.bin bs=1M count=1   # Generate 1 MiB of random data
```

---

### Root Directory Structure (`/`)

| Directory | Purpose |
|-----------|---------|
| `/bin` | Essential user binaries (`ls`, `cp`, `bash`). Symlink to `/usr/bin` on RHEL 8+ |
| `/sbin` | System admin binaries (`mount`, `shutdown`). Symlink to `/usr/sbin` on RHEL 8+ |
| `/boot` | Kernel images, initrd, GRUB bootloader files |
| `/dev` | Device files (interfaces to hardware) |
| `/etc` | System-wide configuration files |
| `/home` | User home directories |
| `/lib` / `/lib64` | Shared libraries for `/bin` and `/sbin` |
| `/mnt` | Temporary manual mount point |
| `/opt` | Optional third-party software packages |
| `/proc` | Virtual filesystem: kernel and process information |
| `/root` | Home directory of the root superuser |
| `/run` | Volatile runtime data (PID files, sockets); cleared on boot |
| `/sys` | Virtual filesystem: hardware/kernel interfaces |
| `/tmp` | Temporary files, cleared on reboot |
| `/usr` | Secondary hierarchy: user utilities, libraries, documentation |
| `/var` | Variable data: logs (`/var/log`), spool, cache |

---

## 8. User & Group Management

### Creating Users — `useradd`

```bash
sudo useradd -m samar                              # Create user with home directory (/home/samar)
sudo useradd -d /custom/home samar                 # Set a custom home directory path (add -m to also create it)
sudo useradd -s /bin/bash samar                    # Set the login shell
sudo useradd -g users samar                        # Set primary group
sudo useradd -G wheel,users samar                  # Add to supplementary groups at creation
sudo useradd -c "Full Name" samar                  # Set display name (GECOS field)

# Full example:
sudo useradd -m -d /home/samar -s /bin/bash -c "Samar Ayman" -G wheel,users samar

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
passwd -S samar             # Short password status for 'samar'
chage -l samar              # Detailed password aging info for 'samar'
```

---

### Modifying Users — `usermod`

```bash
sudo usermod -l newname oldname                 # Rename a user
sudo usermod -d /home/newname -m newname        # Update home dir path and move contents
sudo usermod -c "Full Name" username            # Update full display name
sudo usermod -s /bin/bash username              # Change default shell
sudo usermod -G wheel,users username            # Replace supplementary groups
sudo usermod -aG docker,wheel username          # APPEND to supplementary groups (-a preserves existing)
chsh -s /bin/bash                               # User changing their OWN shell (no sudo needed)
sudo chsh -s /bin/bash username                 # Root changing another user's shell
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
sudo userdel username           # Delete user (home directory is NOT removed)
sudo userdel -r username        # Delete user AND remove home directory
sudo userdel -f username        # Force delete even if user is currently logged in
```

---

### Managing Groups

```bash
sudo groupadd -g 2500 developers        # Create group with a specific GID
sudo groupmod -n devteam developers     # Rename group
sudo groupmod -g 5000 devteam           # Change GID
sudo groupdel devteam                   # Delete group

sudo usermod -aG audio,video username   # Add user to multiple groups
sudo gpasswd -d username wheel          # Remove user from group
```

---

### Password Aging Policy — `chage`

```bash
sudo chage -l username                  # Show password aging details
sudo chage -m 7 -M 90 username          # Min 7 days, max 90 days before change required
sudo chage -W 14 username               # Warn 14 days before password expires
sudo chage -E 2027-12-31 username       # Account expires on this date
sudo chage -M 99999 username            # Effectively disable password expiry
```

---

### Login Monitoring

```bash
who             # Who is currently logged in
w               # Who is logged in + what they're doing
last            # Recent login history from /var/log/wtmp
last ahmed      # Login history for a specific user
sudo lastb      # Failed login attempts

ps -u ahmed     # Processes belonging to user 'ahmed'
top -u ahmed    # Monitor only ahmed's processes in top

# Log files:
sudo cat /var/log/secure      # Login events, sudo usage, SSH activity
sudo cat /var/log/messages    # General system activity
```

---

### Root Access & `sudo`

```bash
sudo passwd root                    # Set a password for the root account
su -                                # Switch to root shell (requires root password)
sudo -s                             # Open a root shell using YOUR sudo password
sudo -u username command            # Run a command as a different user
sudo -k                             # Invalidate cached sudo credentials
```

**Sudoers file** (`sudo visudo` — always use this, never edit directly):
```
# Grant full sudo to a user:
username ALL=(ALL:ALL) ALL

# Grant full sudo to all members of wheel group:
%wheel ALL=(ALL:ALL) ALL

# Allow a user to run sudo without a password:
username ALL=(ALL:ALL) NOPASSWD: ALL

# Allow only a specific command without password:
username ALL=(ALL:ALL) NOPASSWD: /usr/bin/dnf
```

---

### Hostname

```bash
hostnamectl                             # Show current hostname and system info
sudo hostnamectl set-hostname my-host   # Change hostname (persistent)
sudo vi /etc/hosts                      # Add: 127.0.0.1    my-host
```

---

## 9. File Permissions & Ownership

### Understanding Permission Notation

```bash
ls -al      # Long listing showing permissions for all files including hidden
ls -ld /data    # Show permissions of a specific directory itself (not its contents)
```

```
-rwxr-xr--  1 user developers 4096 Mar 21 12:00 script.sh
│└┬┘└┬┘└┬┘
│ │  │  └───────── Others: r-- (read only)
│ │  └──────────── Group:  r-x (read + execute)
│ └─────────────── Owner:  rwx (read + write + execute)
└───────────────── File type: - (file), d (dir), l (symlink)
```

> Use `ls -ld /data` (lowercase L + d flag) to inspect the directory's own permissions
> without listing its contents. The `d` flag means "directory itself".

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
```

| Number | Permissions |
|--------|------------|
| `4` | `r--` read |
| `5` | `r-x` read + execute |
| `6` | `rw-` read + write |
| `7` | `rwx` read + write + execute |

---

### `chown` — Change Ownership

```bash
sudo chown username file.txt                # Change owner
sudo chown username:groupname file.txt      # Change owner AND group
sudo chown :groupname file.txt              # Change group only
sudo chown -R username:groupname folder/    # Recursive ownership change
```

---

### `umask` — Default Permission Mask

```bash
umask                   # Show current umask (e.g. 0022)
umask 022               # Set umask: new files = 644, new dirs = 755
umask 027               # More restrictive: new files = 640, new dirs = 750

# System-wide umask (preferred RHEL approach — drop-in file):
sudo vi /etc/profile.d/custom-umask.sh
# Add: umask 022
```

> umask subtracts permissions from the default (666 for files, 777 for dirs).
> umask `022` → `666 - 022 = 644` for files.

---

### Sticky Bit

Prevents users from deleting files they don't own, even in a world-writable directory.

```bash
chmod +t /data              # Set sticky bit
chmod 1777 /data            # Full permissions + sticky bit (like /tmp)
ls -ld /tmp                 # Shows 't' in the others execute position: drwxrwxrwt
```

---

### SetUID & SetGID

**SetUID** — file runs with the **owner's** privileges (not the caller's):
```bash
sudo chown root:root myexec
sudo chmod u+s myexec           # or: chmod 4755 myexec
ls -l myexec
# → -rwsr-xr-x  (s in user position = SetUID active)
```

> Example: `/usr/bin/passwd` uses SetUID to write to `/etc/shadow` as root.

**SetGID** — new files in the directory **inherit** the directory's group:
```bash
sudo groupadd developers
sudo mkdir /shared
sudo chown root:developers /shared
sudo chmod 2775 /shared             # or: chmod g+s /shared
ls -ld /shared
# → drwxrwsr-x  (s in group position = SetGID active)
```

---

### ACL — Access Control Lists (`getfacl` / `setfacl`)

ACLs extend standard Unix permissions, allowing fine-grained access control for
individual users or groups beyond the owner/group/others model.

> `getfacl` and `setfacl` are part of the `acl` package — pre-installed on RHEL 8/9.
> If missing: `sudo dnf install acl`

#### The `+` sign in `ls -l` output

When a file or directory has an ACL applied, `ls -l` appends a **`+`** at the end
of the permission string:

```
drwxr-xr-x. 2 root root 6 Jan  5 10:00 /folder     ← no ACL
drwxr-xr-x+ 2 root root 6 Jan  5 10:00 /folder     ← ACL is set ('+' indicator)
```

The `.` (dot) indicates an SELinux context is present. The `+` indicates an ACL exists.
Both can appear together: `drwxr-xr-x+`

---

#### `getfacl` — View ACL

```bash
getfacl /folder             # Show all ACL entries for a directory
getfacl file.txt            # Show ACL for a file
getfacl -R /folder          # Recursive: show ACLs for all items inside
```

**Example output:**
```
# file: folder
# owner: root
# group: root
user::rwx
group::r-x
group:mody:r-x          ← explicit ACL entry for group 'mody'
mask::r-x
other::r-x
```

---

#### `setfacl` — Set ACL

```bash
# Grant a specific group read+execute access to /folder:
setfacl -m g:mody:rx /folder
```

**Before:**
```
$ ls -ld /folder
drwxr-xr-x. 2 root root 6 Jan  5 10:00 /folder
```

**After:**
```
$ ls -ld /folder
drwxr-xr-x+ 2 root root 6 Jan  5 10:00 /folder
                    ↑
              '+' confirms ACL is now active
```

**Verify with getfacl:**
```
$ getfacl /folder
# file: folder
# owner: root
# group: root
user::rwx
group::r-x
group:mody:r-x          ← mody group now has read+execute
mask::r-x
other::r-x
```

---

#### Common `setfacl` Commands

```bash
# --- Grant access ---
setfacl -m u:samar:rwx /folder          # Grant user 'samar' full rwx
setfacl -m g:mody:rx /folder            # Grant group 'mody' read+execute
setfacl -m u:samar:r-- file.txt         # Grant user 'samar' read-only on a file

# --- Recursive (applies to all existing files inside) ---
setfacl -R -m u:samar:rwx /folder       # Recursive: set ACL on /folder and all contents

# --- Default ACL (inherited by NEW files created inside the directory) ---
setfacl -m d:u:samar:rwx /folder        # New files inside /folder will inherit this ACL
setfacl -m d:g:mody:rx /folder          # New files inherit read+execute for group 'mody'

# --- Remove a specific ACL entry ---
setfacl -x u:samar /folder              # Remove user 'samar' ACL entry
setfacl -x g:mody /folder              # Remove group 'mody' ACL entry

# --- Remove ALL ACLs from a file/directory ---
setfacl -b /folder                      # Wipe all ACL entries (back to standard permissions)
setfacl -b file.txt

# --- Copy ACL from one path to another ---
getfacl /folder | setfacl --set-file=- /other_folder
```

**ACL permission reference:**

| Symbol | Permission |
|--------|-----------|
| `r` | Read |
| `w` | Write |
| `x` | Execute |
| `rwx` | Full access |
| `rx` | Read + execute (common for directories) |
| `r--` | Read-only |
| `---` | No access |

---

## 10. Processes

### Viewing Processes — `ps`

```bash
ps -e                       # ALL processes (short format)
ps -ef                      # All processes, full detail (UID, PID, PPID, TTY, CMD)
ps aux                      # BSD style: includes %CPU and %MEM
ps -ef --forest             # Parent/child tree view
ps -p 1234 -f               # Details for a specific PID
ps -eo pid,comm,%cpu,nice --sort=-%cpu   # Custom columns, sorted by CPU

# Filter:
ps -ef | grep 'nginx' | grep -v grep    # Find nginx processes (exclude grep itself)
pgrep nginx                             # PIDs by name (cleaner)
pgrep -f nginx                          # Match against full command line
```

**Key columns in `ps -ef`:**

| Column | Meaning |
|--------|---------|
| `UID` | User owning the process |
| `PID` | Process ID |
| `PPID` | Parent process ID |
| `TTY` | Terminal (`pts/N` for SSH, `?` for daemons) |
| `CMD` | Full command with arguments |

---

### Real-time Monitoring — `top`

```bash
top                             # Interactive monitor (sorted by CPU by default)
top -u username -d 1 -c         # Filter by user, 1s refresh, show full commands

# htop — enhanced top (requires EPEL; see Section 13 for EPEL setup):
sudo dnf install htop
htop
```

**`top` keyboard shortcuts:**

| Key | Action |
|-----|--------|
| `k` | Kill a process (enter PID) |
| `r` | Renice (change priority) |
| `f` | Add/remove columns |
| `H` | Show threads |
| `Shift+W` | Save configuration |
| `q` | Quit |

---

### Process Priority — `nice` / `renice`

Nice values range from **-20** (highest priority) to **19** (lowest). Default is 0.

```bash
nice -n 10 bash myjob.sh                # Start a job with lower priority
sudo nice -n -5 bash important.sh       # Higher priority (negative values require sudo)
sudo renice -n 10 -p 1234              # Change running process PID 1234 to nice = 10
sudo renice -n 19 $(pgrep nginx)       # Deprioritize all nginx processes
```

---

### Signals

```bash
kill -l                         # List all signal names and numbers
kill -15 1234                   # Send SIGTERM (graceful termination request)
kill -9 1234                    # Send SIGKILL (force kill, no cleanup)

pkill -SIGTERM nginx            # Kill by process name (graceful)
pkill -9 nginx                  # Force kill by name

# killall is available via the psmisc package:
sudo dnf install psmisc
killall nginx
```

**Common signals:**

| Signal | Number | Meaning |
|--------|--------|---------|
| `SIGHUP` | 1 | Hangup — reload config / graceful restart |
| `SIGINT` | 2 | Interrupt (same as Ctrl+C) |
| `SIGKILL` | 9 | Force kill — cannot be caught or ignored |
| `SIGTERM` | 15 | Graceful termination request (default `kill`) |
| `SIGCONT` | 18 | Continue a stopped process |
| `SIGSTOP` | 19 | Pause a process — cannot be caught or ignored |

---

### Pausing & Resuming Processes

```bash
kill -SIGSTOP 1234      # Pause a running process
kill -SIGCONT 1234      # Resume a paused process
# Ctrl+Z                # Suspend the foreground process (sends SIGTSTP)
fg                      # Resume suspended process in foreground
bg                      # Resume suspended process in background

ps -elf | grep ' Z '    # Find zombie processes (state Z)
```

---

## 11. Job Control

### Background & Foreground

```bash
ping -c 20 google.com &         # Run in background
jobs                            # List all background jobs and their status
jobs -l                         # Include PIDs

fg                              # Bring last background job to foreground
fg %1                           # Bring job number 1 to foreground
bg %1                           # Resume stopped job 1 in background
kill %2                         # Kill job number 2

# Ctrl+Z                        # Suspend the current foreground process
# Ctrl+C                        # Terminate the current foreground process
```

---

### `nohup` — Survive Terminal Close

```bash
nohup bash longtask.sh &                         # Background, immune to hangup
nohup bash longtask.sh > out.txt 2>&1 &          # Redirect all output to a file
```

| Method | Background? | Survives terminal close? |
|--------|-------------|--------------------------|
| `cmd &` | ✅ | ❌ |
| `nohup cmd` | ❌ | ✅ |
| `nohup cmd &` | ✅ | ✅ |

---

### Waiting for Jobs — `wait`

```bash
wait                    # Wait for ALL background jobs to complete
wait 1234               # Wait for process PID 1234
wait %1                 # Wait for job number 1

# Example: run parallel tasks, notify when all done
bash task1.sh > /dev/null &
bash task2.sh > /dev/null &
bash task3.sh > /dev/null &
wait; echo "All tasks completed."
```

---

## 12. Archives & Compression

### `tar`

```bash
# Extract:
tar -xvzf file.tar.gz       # Extract .tar.gz
tar -xvjf file.tar.bz2      # Extract .tar.bz2
tar -xvJf file.tar.xz       # Extract .tar.xz
tar -xvf  file.tar          # Extract plain .tar

# Create:
tar -cvzf archive.tar.gz folder/    # Create .tar.gz from a folder
tar -cvjf archive.tar.bz2 folder/   # Create .tar.bz2
tar -cvJf archive.tar.xz folder/    # Create .tar.xz

# List contents without extracting:
tar -tvzf file.tar.gz

# Flags reference:
# -x = extract, -c = create, -t = list
# -v = verbose, -f = filename follows
# -z = gzip, -j = bzip2, -J = xz
```

### Other Archive Formats

```bash
# unzip and zip are in RHEL AppStream:
sudo dnf install unzip zip

# p7zip requires EPEL (see Section 13 for EPEL setup):
sudo dnf install p7zip p7zip-plugins

# Extract:
unzip file.zip              # Extract .zip
7z x file.7z               # Extract .7z

# Create:
zip -r archive.zip folder/
```

---

## 13. Package Management

### EPEL Setup (RHEL-Specific)

> ⚠️ On genuine RHEL, `dnf install epel-release` alone does **not** work (that is a CentOS shortcut). Use the method below.

```bash
# RHEL 8 — Enable CodeReady Linux Builder repo, then install EPEL:
sudo subscription-manager repos --enable codeready-builder-for-rhel-8-$(arch)-rpms
sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

# RHEL 9 — Enable CodeReady Linux Builder repo, then install EPEL:
sudo subscription-manager repos --enable codeready-builder-for-rhel-9-$(arch)-rpms
sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm

# Note: On cloud instances using RHUI (AWS, Azure, GCP), the subscription-manager
# step may differ. Refer to your cloud provider's RHEL documentation.
```

---

### DNF

```bash
sudo dnf upgrade --refresh                  # Refresh metadata and upgrade all packages
sudo dnf install nginx                      # Install a package
sudo dnf remove nginx                       # Remove a package
sudo dnf autoremove                         # Remove unneeded dependencies

# Searching packages:
sudo dnf search nginx                       # Search by name and summary
sudo dnf search all 'nginx'                 # Deep search: name + summary + description (broader results)

dnf info nginx                              # Package details (version, size, repo, description)
sudo dnf check                              # Check for RPM database problems

# Repository management:
dnf repolist                                # List all ENABLED repositories
dnf repolist all                            # List ALL repos (enabled and disabled)
dnf repolist --enabled                      # Explicitly list enabled repos only
dnf repolist --disabled                     # List disabled repos only
ls /etc/yum.repos.d/                        # List configured repo files
sudo vi /etc/yum.repos.d/<repo-name>.repo   # Edit a repo file manually
sudo dnf install dnf-plugins-core           # Required for config-manager plugin
sudo dnf config-manager --add-repo https://example.repo/pkg.repo  # Add a repo
sudo dnf config-manager --disable example-repo                    # Disable a repo
sudo dnf config-manager --enable example-repo                     # Enable a repo

# Kernel version management:
sudo dnf mark install kernel-core-6.8.0-31.el9.x86_64   # Mark as user-installed (protects from autoremove)
sudo dnf install python3-dnf-plugin-versionlock          # Install versionlock plugin
sudo dnf versionlock add kernel                          # Lock kernel version
sudo dnf versionlock delete kernel                       # Unlock
dnf list kernel                                          # List available kernel packages
```

---

### Flatpak

> Flatpak is available in RHEL 8/9 AppStream. It is an optional package delivery system
> for sandboxed desktop applications.

```bash
sudo dnf install flatpak                                            # Install Flatpak
sudo flatpak remote-add --if-not-exists flathub \
    https://flathub.org/repo/flathub.flatpakrepo                   # Add Flathub remote

flatpak remote-ls                                                   # List all available apps from all remotes
flatpak remote-ls flathub                                           # List apps from the Flathub remote only
flatpak remote-ls --app flathub                                     # List only apps (exclude runtimes)

flatpak remote-modify --disable flathub                             # Disable Flathub remote (stops fetching updates)
flatpak remote-modify --enable flathub                              # Re-enable Flathub remote

flatpak remotes                                                     # List all configured remotes and their status
flatpak list                                                        # List installed Flatpak apps
flatpak install flathub org.gimp.GIMP                              # Install an app from Flathub
flatpak uninstall org.gimp.GIMP                                    # Remove an installed app
flatpak update                                                      # Update all installed Flatpak apps
```

---

### RPM

```bash
sudo rpm -ivh package.rpm           # Manually install a local .rpm file
sudo rpm -e package-name            # Remove an installed package
rpm -qa | grep nginx                # List installed packages matching a name
rpm -qf /usr/bin/ls                 # Which package owns a file
rpm -V bash                         # Verify a specific package
rpm -Va                             # Verify all installed packages
rpm -K package.rpm                  # Check .rpm GPG signature
```

---

### System Info

```bash
cat /etc/redhat-release     # RHEL version string
uname -a                    # Full kernel and OS info
uname -r                    # Kernel version only
hostnamectl                 # System info: OS, hostname, kernel, architecture

# Timezone and locale:
timedatectl                                 # Show current time, timezone, NTP status
sudo timedatectl set-timezone Region/City   # Set timezone (e.g. America/New_York)
timedatectl list-timezones                  # List all available timezones
date +%d/%m/%Y                              # Date in day/month/year format
```

---

## 14. Boot Process & Systemd

### GRUB Configuration

```bash
sudo vi /etc/default/grub
# Key options:
# GRUB_TIMEOUT=5                → show menu for 5 seconds
# GRUB_TIMEOUT_STYLE=menu       → always show menu
# GRUB_DEFAULT=0                → default menu entry (0 = first)

# Apply changes:
# BIOS/Legacy systems:
sudo grub2-mkconfig -o /boot/grub2/grub.cfg

# UEFI systems:
sudo grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg

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
dmesg -T | tail -50         # Last 50 boot messages with human-readable timestamps

lsmod                       # List loaded kernel modules
modinfo bluetooth           # Info about a specific module
sudo modprobe bluetooth     # Load a module
sudo modprobe -r bluetooth  # Unload a module

ls /usr/lib/modules/$(uname -r)/   # Modules for the current kernel
```

---

### systemd — Units & Services

```bash
ps 1                                        # Confirm systemd is PID 1

# Unit file locations (in priority order):
# /etc/systemd/system     → custom overrides (highest priority)
# /run/systemd/system     → runtime-generated units
# /usr/lib/systemd/system → distribution-provided units (do not edit directly)

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
sudo systemctl daemon-reload        # Always run after editing unit files
sudo systemctl show nginx           # Low-level technical unit properties
```

---

### systemd Targets (Runlevels)

```bash
systemctl get-default                            # Show current default target
systemctl list-units --type=target --all         # All targets
sudo systemctl set-default multi-user.target     # Boot to CLI (no GUI)
sudo systemctl set-default graphical.target      # Boot to GUI
sudo systemctl isolate multi-user.target         # Switch to CLI NOW (no reboot)
sudo systemctl isolate graphical.target          # Switch to GUI NOW
systemctl cat graphical.target                   # View target unit file contents
```

---

### Custom Service Unit File

```bash
sudo vi /etc/systemd/system/myapp.service
```

```ini
[Unit]
Description=My Application
After=network.target

[Service]
ExecStart=/usr/bin/myapp
Restart=on-failure
User=appuser
WorkingDirectory=/opt/myapp

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload                # Always reload after editing unit files
sudo systemctl enable --now myapp

# Drop-in override (preferred over modifying the vendor unit file):
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

journalctl -u nginx                     # Logs for nginx service
journalctl -u nginx -f                  # Follow nginx logs live
journalctl -u nginx -r                  # Reverse (newest first)
journalctl -p err                       # Error level and above only

journalctl --since "today"
journalctl --since "2 hours ago"
journalctl --since "today" --until "1 hour ago"
journalctl -u sshd --since "1 hour ago"

echo 'my message' | systemd-cat         # Write a custom log entry
journalctl | grep SELinux               # Filter for SELinux messages
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
ExecStart=/usr/local/bin/my-task.sh
StandardOutput=append:/var/log/my-task.log
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
sudo systemctl list-timers                              # List all active timers

# Test calendar expressions:
systemd-analyze calendar '*:00/15'                      # Every 15 minutes
systemd-analyze calendar 'Mon..Fri 08:00'               # Weekdays at 8 AM
systemd-analyze calendar '2027-01-01 00:00'             # One-time future date
```

---

### cgroups — Resource Limits

**Recommended: via systemd service unit:**
```ini
[Service]
MemoryMax=300M
CPUQuota=25%
```

```bash
sudo systemctl daemon-reload
sudo systemctl restart myapp
```

**Direct cgroups v2 (manual, advanced):**
```bash
# Check cgroup version in use:
mount | grep cgroup

# Create a cgroup and apply limits:
sudo mkdir /sys/fs/cgroup/mygroup
echo "10000 100000" | sudo tee /sys/fs/cgroup/mygroup/cpu.max    # 10% CPU
echo $((50 * 1024 * 1024)) | sudo tee /sys/fs/cgroup/mygroup/memory.max  # 50 MB RAM

# Assign the current shell's process:
echo $$ | sudo tee /sys/fs/cgroup/mygroup/cgroup.procs

# Monitor:
cat /sys/fs/cgroup/mygroup/cpu.stat
cat /sys/fs/cgroup/mygroup/memory.current

# Clean up (all processes must have left first):
sudo rmdir /sys/fs/cgroup/mygroup
```

> ⚠️ RHEL 9 uses **cgroups v2** (unified hierarchy) exclusively. The legacy cgroups v1 tools (`libcgroup-tools`) are available for backward compatibility but deprecated.

---

## 15. Storage — Partitions, Mounts & LVM

### Partitioning with `parted`

```bash
sudo parted /dev/sdb                    # Open interactive session on a specific disk

# Inside parted:
    print all                           # List all storage devices
    print                               # Show current disk's partition table
    print free                          # Show partition table including free space
    mklabel gpt                         # Create GPT partition table (⚠️ destroys all data on disk)
    mklabel msdos                       # MBR partition table (for BIOS/legacy)
    unit GiB                            # Set unit for sizes
    # GPT: mkpart <name> <fs-type> <start> <end>
    mkpart part1 xfs 0% 50%             # GPT: first 50%
    mkpart part2 xfs 50% 100%           # GPT: remaining space
    # MBR: mkpart <primary|logical|extended> <fs-type> <start> <end>
    set 1 lvm on                        # Mark partition for LVM use
    rm 1                                # Remove partition 1
    quit
```

---

### Formatting & Mounting

```bash
# Format:
sudo mkfs.xfs /dev/sdb1                 # Format as XFS    (default on RHEL; xfsprogs included)
sudo mkfs.ext4 /dev/sdb1               # Format as ext4   (e2fsprogs included)
sudo mkfs.vfat /dev/sdb1               # Format as FAT32  (requires: sudo dnf install dosfstools)
sudo mkfs.exfat /dev/sdb1              # Format as exFAT  (RHEL 8: EPEL — sudo dnf install exfatprogs; RHEL 9: AppStream)

# Label a partition:
sudo xfs_admin -L mydata /dev/sdb1      # Label XFS filesystem
sudo e2label /dev/sdb1 mydata           # Label ext4 filesystem

# Mount:
sudo mkdir /mnt/data
sudo mount /dev/sdb1 /mnt/data                              # Basic mount
sudo mount -o ro /dev/sdb1 /mnt/data                        # Read-only
sudo mount -o ro,noexec,nosuid,noatime /dev/sdb1 /mnt/data  # Secure mount options

# Unmount:
sudo umount /dev/sdb1                   # Unmount by device
sudo umount /mnt/data                   # Unmount by mount point

# Check:
mount | grep /mnt/data                  # Show mount details
lsblk -f                                # Filesystem type, label, UUID
df -h                                   # Disk space usage
sudo blkid /dev/sdb1                    # UUID and filesystem type
```

---

### Persistent Mounts — `/etc/fstab`

```bash
sudo blkid /dev/sdb1     # Get UUID first
sudo vi /etc/fstab
```

```
# device/UUID                                           mountpoint  fstype  options      dump  pass
UUID=31b8c5a6-738d-4f3d-b9f2-1a4e7c830d51   /mnt/data    xfs     defaults     0     0
/dev/sdb1                                   /mnt/backup  ext4    ro,noexec    0     0
```

```bash
sudo mount -a       # Test: mount everything in fstab (catches errors before reboot)
```

---

### SMART Disk Health

```bash
sudo dnf install smartmontools

sudo smartctl -H /dev/sda               # Overall health: PASSED / FAILED
sudo smartctl -a /dev/sda               # Full SMART data
sudo smartctl -t short /dev/sda         # Run a short self-test
```

---

### Filesystem Check — `fsck`

```bash
# ⚠️ Never run fsck on a mounted filesystem — unmount first
sudo umount /dev/sdb1
sudo fsck -n /dev/sda1              # Dry run: check without fixing
sudo fsck -y /dev/sda1              # Auto-fix all errors
sudo e2fsck -f /dev/sdb1            # Force check ext4 even if marked clean

# XFS uses its own tool:
sudo xfs_repair /dev/sdb1           # Repair XFS filesystem

sudo journalctl -b | grep 'File System'    # Check logs from last boot for FS errors
```

---

### `tune2fs` — ext4 Filesystem Parameters

> `tune2fs` applies to ext2/ext3/ext4 only — **not XFS**.

```bash
sudo tune2fs -l /dev/sda1           # List all filesystem parameters
sudo tune2fs -L MyData /dev/sda1    # Set filesystem label
sudo tune2fs -i 30d /dev/sda1       # Set check interval to 30 days
sudo tune2fs -c 0 /dev/sda2         # Disable mount-count-based checks
```

---

### LVM — Logical Volume Manager

```bash
sudo dnf install lvm2
```

**Setup flow:**
```bash
# 1. Prepare disk with parted (mark partition as LVM):
sudo parted /dev/sdb mklabel gpt
sudo parted /dev/sdb mkpart pv 0% 100%      # GPT uses a name (not 'primary' — that's MBR only)
sudo parted /dev/sdb set 1 lvm on

# 2. Create Physical Volumes (PV):
sudo pvcreate /dev/sdb1 /dev/sdc1
pvs; pvdisplay

# 3. Create Volume Group (VG):
sudo vgcreate myvg /dev/sdb1 /dev/sdc1
vgs; vgdisplay

# 4. Create Logical Volumes (LV):
sudo lvcreate -L 10G -n data myvg           # Fixed size
sudo lvcreate -l 100%FREE -n backups myvg   # Use all remaining space
lvs; lvdisplay

# 5. Format and mount:
sudo mkfs.xfs /dev/myvg/data
sudo mkdir /mnt/data
sudo mount /dev/myvg/data /mnt/data
lsblk -f; df -h
```

**Managing LVM:**
```bash
# Extend:
sudo vgextend myvg /dev/sde1                    # Add a new disk to VG
sudo lvextend -L +20G --resizefs /dev/myvg/data # Extend LV and XFS filesystem in one step

# Shrink (ext4 only — XFS cannot be shrunk):
sudo e2fsck -f /dev/myvg/data                   # Check ext4 filesystem first
sudo resize2fs /dev/myvg/data 15G               # Shrink ext4 filesystem to 15G
sudo lvreduce -L 15G /dev/myvg/data             # Then shrink the LV

# Move and remove a PV:
sudo pvmove -v /dev/sdc1                        # Move data off /dev/sdc1
sudo vgreduce myvg /dev/sdc1                    # Remove from VG
sudo pvremove /dev/sdc1                         # Remove PV label

# Rename an LV:
sudo lvrename myvg oldname newname

# Clean up / destroy:
sudo umount /mnt/data
sudo lvchange -an /dev/myvg/data
sudo lvremove /dev/myvg/data
sudo vgremove myvg
sudo pvremove /dev/sdb1 /dev/sdc1
sudo wipefs -a /dev/sdb /dev/sdc
lsblk -f                                        # Confirm clean state
```

---

## 16. System Upgrade & Recovery

### Full Upgrade

```bash
# Minor version upgrade (e.g., RHEL 9.3 → 9.4) — standard package update:
sudo dnf upgrade --refresh
sudo reboot

# Major version in-place upgrade (e.g., RHEL 8 → RHEL 9) — use Leapp:
sudo dnf install leapp-upgrade
sudo leapp preupgrade            # Compatibility checks; review the generated report
sudo leapp upgrade               # Perform the actual upgrade
sudo reboot                      # System boots into the new RHEL version

# Verify after reboot:
cat /etc/redhat-release
```

---

### Boot Recovery

```bash
# 1. Boot from RHEL installation media → "Troubleshooting" →
#    "Rescue a Red Hat Enterprise Linux system"
#    The rescue environment auto-mounts the installed system at /mnt/sysroot

# 2. Bind-mount kernel filesystems BEFORE chrooting:
mount --bind /dev  /mnt/sysroot/dev
mount --bind /proc /mnt/sysroot/proc
mount --bind /sys  /mnt/sysroot/sys

# 3. Enter the installed system:
chroot /mnt/sysroot

# Fix GRUB (running inside the chroot):
vi /etc/default/grub

# BIOS:
grub2-mkconfig -o /boot/grub2/grub.cfg
# UEFI:
grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg

# Protect a specific kernel from being removed:
dnf mark install kernel-core-6.8.0-31.el9.x86_64
dnf mark install kernel-modules-6.8.0-31.el9.x86_64

# Remove a broken kernel:
rpm -qa | grep kernel
dnf remove kernel-core-6.8.0-35.el9.x86_64
grub2-mkconfig -o /boot/grub2/grub.cfg
```

---

## 17. Cron Jobs

### Cron Basics

```bash
crontab -e                  # Edit current user's crontab (opens in $EDITOR)
EDITOR=vi crontab -e        # Force vi as editor
crontab -l                  # List current crontab
crontab -r                  # Remove ALL cron jobs (use with caution!)
crontab -u ahmed -l         # List another user's crontab (root only)

ls /var/spool/cron/         # Crontab files stored here (root access required)
journalctl -u crond.service -r      # Recent cron logs
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
# Always set SHELL and PATH inside crontab to avoid environment issues:
SHELL=/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

*/5 * * * *       ping -c 4 google.com >> /var/log/ping.log 2>&1    # Every 5 minutes
0,15,30,45 * * * * /usr/local/bin/task.sh                           # Every 15 minutes
0 2 * * *         /usr/local/bin/backup.sh >> /var/log/backup.log 2>&1  # Daily at 2 AM
0 0 * * 1         /usr/local/bin/weekly.sh                          # Every Monday at midnight
@reboot           /usr/local/bin/startup.sh                         # Once at system startup
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

### Prevent Overlapping Jobs — `flock`

```bash
# In crontab:
*/5 * * * * flock -n /tmp/myjob.lock /path/to/job.sh >> /var/log/job.log 2>&1
```

> `flock -n` skips execution if the lock is already held (previous run still going). `flock` is part of `util-linux` — always present on RHEL.

---

### System-wide Cron

```bash
sudo vi /etc/crontab
# Format: minute hour dom month dow USERNAME command
0 2 * * *  root  /usr/local/bin/backup.sh >> /var/log/backup.log 2>&1

# Drop-in directories (scripts placed here run automatically):
ls /etc/cron.hourly/
ls /etc/cron.daily/
ls /etc/cron.weekly/
ls /etc/cron.monthly/
```

---

### Email Notifications

```bash
# postfix is the default MTA on RHEL:
sudo dnf install postfix
sudo systemctl enable --now postfix

# In /etc/crontab or user crontab, set MAILTO:
MAILTO=admin@example.com
0 2 * * * root /usr/local/bin/backup.sh

# View local mail delivered to root:
sudo cat /var/spool/mail/root
```

---

### Anacron — For Systems That Aren't Always On

Anacron runs missed jobs when the system comes back up (unlike cron, which simply skips them).

```bash
cat /etc/anacrontab     # Main anacron config
```

```
# period(days)  delay(min)  job-id       command
1               5           cron.daily   run-parts /etc/cron.daily
7               10          cron.weekly  run-parts /etc/cron.weekly
30              15          cron.monthly run-parts /etc/cron.monthly
```

---

## 18. Networking

### Interface Overview

```bash
ip addr show                            # Show ALL interfaces with IP addresses
ip -br addr show                        # Brief/compact format: one line per interface
ip -c addr show                         # Colorized output (full detail)

ip addr show ens33                      # Show a SPECIFIC interface by name
ip -br addr show ens33                  # Brief view of one specific interface
```

**Example output of `ip -br addr show`:**
```
lo               UNKNOWN        127.0.0.1/8 ::1/128
ens33            UP             192.168.1.100/24 fe80::1a2b:3c4d:5e6f:7a8b/64
ens37            DOWN
```

---

### Link Layer & Interface State

```bash
ip link show                            # Show link-layer info for ALL interfaces (MAC, state, MTU)
ip link show ens33                      # Link-layer info for a SPECIFIC interface
ip -br link show                        # Brief: state + MAC per interface

sudo ip link set dev ens33 up           # Bring interface up
sudo ip link set dev ens33 down         # Bring interface down
sudo ip link set dev ens33 mtu 9000     # Set jumbo frames MTU
```

---

### IP Address Management

```bash
sudo ip addr add 192.168.1.10/24 dev ens33   # Add a temporary IP (lost on reboot)
sudo ip addr del 192.168.1.10/24 dev ens33   # Remove an IP address
sudo ip addr flush dev ens33                 # Remove ALL addresses from an interface
```

---

### Routing

```bash
ip route show                           # Show full routing table
ip -c route show                        # Routing table with color
ip -br route show                       # Brief routing table
ip route get 8.8.8.8                    # Which route would be used to reach this IP
sudo ip route add default via 192.168.1.1    # Add default gateway (temporary)
sudo ip route del default via 192.168.1.1    # Remove a default gateway entry
sudo ip route add 10.0.0.0/8 via 192.168.1.254 dev ens33  # Add a static route
```

---

### Sockets & Listening Ports

```bash
ss -4nap                                # All IPv4 sockets with process info
ss -tulnp                               # TCP/UDP listening ports with PIDs
ss -s                                   # Socket summary statistics
ss -tnp state established               # Only established TCP connections
```

---

### NetworkManager — `nmcli`

```bash
# Overview:
nmcli device status                                         # List interfaces and connection state
nmcli connection show                                       # List all connection profiles

# Inspect a specific interface or connection:
nmcli device show ens33                                     # Full details for a specific interface
                                                            # (IP, MAC, DNS, gateway, state)
nmcli con show ens33                                        # All settings for a named connection profile

# Configure a static IP:
sudo nmcli con modify ens33 ipv4.addresses 192.168.1.100/24
sudo nmcli con modify ens33 ipv4.gateway 192.168.1.1
sudo nmcli con modify ens33 ipv4.dns "8.8.8.8 1.1.1.1"
sudo nmcli con modify ens33 ipv4.method manual              # Disable DHCP, use static IP
sudo nmcli con down ens33 && sudo nmcli con up ens33        # Apply changes

# Switch back to DHCP:
sudo nmcli con modify ens33 ipv4.method auto
sudo nmcli con modify ens33 ipv4.addresses ""
sudo nmcli con modify ens33 ipv4.gateway ""
sudo nmcli con down ens33 && sudo nmcli con up ens33

# Connect / disconnect a device:
sudo nmcli device connect ens33                             # Connect interface
sudo nmcli device disconnect ens33                          # Disconnect interface

# Reload connection from disk:
sudo nmcli con reload

# --- Autoconnect ---
# Controls whether NetworkManager brings up a connection automatically on boot
# or whenever the interface becomes available (e.g. cable plugged in, VM start).

sudo nmcli con modify ens33 connection.autoconnect yes      # Enable autoconnect for this profile (default: yes)
sudo nmcli con modify ens33 connection.autoconnect no       # Disable autoconnect (profile won't come up automatically)

# Priority — when multiple profiles compete for the same device, the highest wins:
sudo nmcli con modify ens33 connection.autoconnect-priority 10    # Higher number = higher priority (default: 0)
sudo nmcli con modify ens33 connection.autoconnect-priority 0     # Reset to default priority

# Retries — how many times NM retries before marking autoconnect as failed:
sudo nmcli con modify ens33 connection.autoconnect-retries 3      # Retry 3 times, then stop (default: -1 = infinite)
sudo nmcli con modify ens33 connection.autoconnect-retries -1     # Infinite retries (never give up)
sudo nmcli con modify ens33 connection.autoconnect-retries 0      # Try once and never retry on failure

# Device-level autoconnect (affects ALL profiles on the device, not just one profile):
sudo nmcli device set ens33 autoconnect yes                 # Allow NM to autoconnect any profile on ens33
sudo nmcli device set ens33 autoconnect no                  # Block NM from auto-activating ens33 entirely

# Verify autoconnect settings for a connection profile:
nmcli con show ens33 | grep -i autoconnect

# Apply after any modify:
sudo nmcli con down ens33 && sudo nmcli con up ens33

nmtui                                                       # Text-based UI — easier alternative to nmcli
journalctl -u NetworkManager -b                             # NetworkManager logs for current boot
```

---

### Connectivity & Path Testing

```bash
ping -c 4 google.com                    # Send exactly 4 packets
ping -i 0.2 google.com                  # Faster interval (0.2s between packets)

# traceroute (install if not present):
sudo dnf install traceroute
traceroute google.com                   # Show each hop to destination
traceroute -n google.com                # Skip DNS lookups (faster)

# mtr — ping + traceroute combined (best tool for diagnosing flaky connections):
sudo dnf install mtr
mtr google.com                          # Live: packet loss and latency per hop
mtr --report google.com                 # Non-interactive report (10 packets, then exit)
mtr --report-cycles 30 google.com       # More packets = more accurate loss %
mtr -n google.com                       # No DNS resolution (faster)
```

---

### DNS Tools

```bash
# dig — the standard DNS query tool
dig google.com                      # A record (IPv4 address)
dig google.com AAAA                 # IPv6 address
dig google.com MX                   # Mail exchange records
dig google.com NS                   # Authoritative name servers
dig google.com TXT                  # TXT records (SPF, DKIM, domain verification)
dig +short google.com               # Clean output: just the IP(s)
dig +trace google.com               # Full resolution path from root servers
dig @8.8.8.8 google.com             # Query a specific DNS server
dig -x 8.8.8.8                      # Reverse lookup: IP → hostname (PTR record)

# nslookup — simpler alternative
nslookup google.com                 # Basic A record lookup
nslookup google.com 1.1.1.1         # Use a specific DNS server
nslookup -type=MX google.com        # Mail records

# host — quickest one-liner
host google.com                     # A + MX records in one shot
host 8.8.8.8                        # Reverse lookup

cat /etc/resolv.conf                # View configured DNS servers
```

---

### File Transfer — `scp` / `rsync` / `sftp`

```bash
# scp — encrypted copy over SSH (simple, one-shot transfers)
scp file.txt user@server:/home/user/          # Local file → remote
scp user@server:/var/log/messages ~/          # Remote file → local
scp -r folder/ user@server:/home/user/        # Recursive directory copy
scp -P 2222 file.txt user@server:/tmp/        # Non-default SSH port (capital -P)

# rsync — smart sync: only transfers what changed (preferred for directories)
rsync -av folder/ user@server:/backup/            # Sync with verbose output
rsync -avz folder/ user@server:/backup/           # Compress during transfer (faster on slow links)
rsync -av --delete folder/ user@server:/backup/   # Mirror: delete files missing from source
rsync -av --dry-run folder/ user@server:/backup/  # Preview what WOULD be transferred (safe test)
rsync -av --progress folder/ user@server:/backup/ # Show per-file progress
rsync -av -e "ssh -p 2222" folder/ user@server:/backup/  # Custom SSH port

# sftp — interactive FTP-like session over SSH
sftp user@server
# Inside sftp:
#   ls / lls               → list remote / local directory
#   get remote_file.txt    → download file to current local directory
#   put local_file.txt     → upload file to current remote directory
#   get -r remote_folder/  → download entire folder
#   cd / lcd               → change remote / local directory
#   exit                   → quit
```

> Prefer `rsync` over `scp` for directories — on repeated runs it only transfers changed files.

---

### HTTP Tools — `curl` / `wget`

```bash
# curl — transfer data to/from URLs
curl https://example.com                    # GET request, print body to stdout
curl -o file.html https://example.com       # Save output to a named file
curl -O https://example.com/archive.zip     # Save with the server's original filename
curl -L https://example.com                 # Follow HTTP redirects
curl -I https://example.com                 # Headers only (HEAD request)
curl -v https://example.com                 # Verbose: full request + response headers
curl -s https://example.com -o /dev/null -w "%{http_code}\n"  # Print only HTTP status code

# POST request with JSON body:
curl -X POST https://api.example.com/login \
     -H "Content-Type: application/json" \
     -d '{"user":"admin","pass":"secret"}'

# With Authorization header:
curl -H "Authorization: Bearer YOUR_TOKEN" https://api.example.com/data

# Resume an interrupted download:
curl -C - -O https://example.com/bigfile.zip

# wget — simple download tool
wget https://example.com/file.zip               # Download a file
wget -O myfile.zip https://example.com/f.zip    # Save with a custom name
wget -c https://example.com/bigfile.zip         # Continue/resume a download
wget -q https://example.com                     # Quiet mode (no output)
```

---

### Port Scanning — `nmap`

```bash
# nmap is available in RHEL AppStream:
sudo dnf install nmap

nmap 192.168.1.1                    # Scan top 1000 most common ports
nmap -p 22,80,443 192.168.1.1       # Scan specific ports only
nmap -p 1-65535 192.168.1.1         # Scan ALL 65535 ports
nmap -sV 192.168.1.1                # Detect service name and version on open ports
nmap -sn 192.168.1.0/24             # Ping sweep: find alive hosts WITHOUT port scanning
nmap --open 192.168.1.0/24          # Only show hosts with open ports
nmap -sU -p 53,123 192.168.1.1      # UDP scan (DNS port 53, NTP port 123)
```

> ⚠️ Only scan networks you own or have explicit written permission to test.

---

### Netcat — `nc`

```bash
# Install nmap-ncat (the standard netcat package on RHEL 8/9):
sudo dnf install nmap-ncat

# Test if a port is open:
nc -zv 192.168.1.1 22               # Check if SSH port is open
nc -zv 192.168.1.1 80               # Check if HTTP port is open

# Quick port check in a script:
nc -z -w 3 192.168.1.1 22 && echo "SSH open" || echo "SSH closed"
# -w 3 = timeout after 3 seconds

# Simple TCP listener (server side):
nc -l 4444                          # Listen on port 4444

# Transfer a file over the network (no SSH needed):
# On receiver:  nc -l 4444 > received_file.txt
# On sender:    nc 192.168.1.1 4444 < file.txt
```

---

### SSH Tunnels

```bash
# Local port forwarding (-L):
# "Make the remote service available on MY local port"
ssh -L 8080:localhost:80 user@server
# → Access server's port 80 via http://localhost:8080 on your machine

ssh -L 5432:db-internal.lan:5432 user@jumpserver
# → Tunnel local DB client to db-internal (only reachable from jumpserver)

# Remote port forwarding (-R):
# "Expose MY local service on the remote server's port"
ssh -R 9000:localhost:3000 user@server

# Dynamic SOCKS proxy (-D):
ssh -D 1080 user@server
# → Set browser SOCKS5 proxy to localhost:1080

# Background tunnel (no shell, just port forwarding):
ssh -fNL 8080:localhost:80 -o ServerAliveInterval=60 user@server
# -f = fork to background, -N = no remote command
```

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
sudo dnf install -y openssh-server
sudo systemctl enable --now sshd
```

**Connect:**
```bash
ssh user@192.168.1.100              # Connect by IP
ssh user@myserver                   # Connect by hostname
ssh -p 2222 user@server             # Non-default port
ssh -i ~/.ssh/my_key user@server    # Use a specific private key
ssh -v user@server                  # Verbose: debug connection issues
```

**Set up passwordless SSH (key-based auth):**
```bash
# Generate a key pair (run on the CLIENT):
ssh-keygen -t ed25519               # Ed25519 — modern and recommended
ssh-keygen -t rsa -b 4096           # RSA 4096-bit — widely compatible alternative

ssh-copy-id user@192.168.1.100      # Copy public key to server (easiest method)

# Manual alternative (if ssh-copy-id is not available):
cat ~/.ssh/id_ed25519.pub | ssh user@server \
  "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys && chmod 700 ~/.ssh"
```

---

### SSH Configuration

**Prevent disconnection — add to `/etc/ssh/sshd_config` on the server:**
```
ClientAliveInterval 60
ClientAliveCountMax 3
```

**Client config (`~/.ssh/config`) — shortcuts and keep-alive:**
```
Host myserver
    HostName 192.168.1.100
    User myuser
    Port 22
    IdentityFile ~/.ssh/id_ed25519
    ServerAliveInterval 60
    ServerAliveCountMax 3

# Now: ssh myserver  (instead of typing the full command)
```

**Harden SSH (recommended on any internet-facing server):**
```bash
sudo vi /etc/ssh/sshd_config
```
```
PermitRootLogin no              # Never allow root login over SSH
PasswordAuthentication no       # Keys only — disable password login
AllowUsers myuser               # Whitelist specific users
Port 2222                       # Non-default port reduces automated attacks
MaxAuthTries 3                  # Limit brute-force attempts
```
```bash
sudo systemctl restart sshd
```

**Check SSH logs:**
```bash
sudo cat /var/log/secure              # Logins, sudo, SSH activity
journalctl -u sshd --since "1 hour ago"
```

**Verify server fingerprint (before connecting to a new server):**
```bash
# On the SERVER — get the true fingerprint:
sudo ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub
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
```

---

### Bandwidth Monitoring

```bash
# iftop, nethogs, and vnstat require EPEL (see Section 13 for EPEL setup):
sudo dnf install iftop nethogs vnstat

sudo iftop -i ens33             # Live bandwidth per connection on a specific interface
sudo iftop -n                   # Don't resolve hostnames (faster)

sudo nethogs ens33              # Bandwidth broken down by process

vnstat                          # Traffic summary (uses historical data)
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
sudo dnf install tcpdump

sudo tcpdump -i ens33                       # Capture all traffic on ens33
sudo tcpdump -i any                         # Capture on ALL interfaces
sudo tcpdump -i ens33 -n                    # No DNS resolution (show IPs only)
sudo tcpdump -i ens33 -c 50                 # Capture exactly 50 packets then stop
sudo tcpdump -i ens33 port 80               # Only HTTP traffic
sudo tcpdump -i ens33 port 443              # Only HTTPS traffic
sudo tcpdump -i ens33 host 192.168.1.1      # Traffic to/from a specific host

# Save to a file (open with Wireshark):
sudo tcpdump -i ens33 -w capture.pcap
sudo tcpdump -r capture.pcap                # Read back a saved capture

# Combine filters:
sudo tcpdump -i ens33 host 192.168.1.1 and port 22   # SSH traffic to/from a host
sudo tcpdump -i ens33 port 80 or port 443             # All web traffic
```

---

### NIC Information — `ethtool`

```bash
# ethtool is in RHEL BaseOS; install if missing:
sudo dnf install ethtool

ethtool ens33               # NIC speed, duplex, link status, auto-negotiation
ethtool -i ens33            # Driver name, version, firmware version
ethtool -S ens33            # NIC statistics: errors, dropped packets, collisions
```

---

### `/etc/hosts` — Hostname Mapping

```bash
sudo vi /etc/hosts
# Add entries like:
# 192.168.1.100    myserver
# 192.168.1.101    rhel-server
# 10.0.0.5         db-server db

# Now you can use: ssh user@myserver  or  ping db-server
```

---

### tmux — Persistent Terminal Sessions

> `tmux` is available in the RHEL AppStream repository (no EPEL required).

```bash
sudo dnf install tmux

tmux                                # Start a new unnamed session
tmux new -s mysession               # Start with a named session
tmux ls                             # List all sessions
tmux attach -t mysession            # Reattach to a named session
tmux attach                         # Reattach to the last session

# Inside tmux (all commands use Ctrl+B prefix):
# Ctrl+B then D    → detach (session keeps running in background)
# Ctrl+B then c    → create a new window
# Ctrl+B then n    → next window
# Ctrl+B then p    → previous window
# Ctrl+B then w    → list all windows
# Ctrl+B then ,    → rename current window
# Ctrl+B then %    → split pane vertically
# Ctrl+B then "    → split pane horizontally
# Ctrl+B then x    → kill current pane
# Ctrl+B then &    → kill current window
```

---

## 19. Firewall (firewalld)

```bash
firewall-cmd --state                        # Is firewalld running?
firewall-cmd --list-all                     # Show active rules for the default zone

# firewalld is pre-installed on RHEL; enable it:
sudo systemctl enable --now firewalld
```

**Services & Ports:**
```bash
sudo firewall-cmd --get-services                            # All known service names
sudo firewall-cmd --info-service http                       # Details about a service

# Temporary rules (lost on reload/reboot):
sudo firewall-cmd --add-service=http
sudo firewall-cmd --add-port=8080/tcp
sudo firewall-cmd --remove-service=http
sudo firewall-cmd --remove-port=8080/tcp

# Permanent rules (persist across reboot):
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload                                  # Apply permanent rules now
```

**Zones:**
```bash
sudo firewall-cmd --get-zones                               # All zone names
sudo firewall-cmd --get-default-zone                        # Active default zone
sudo firewall-cmd --set-default-zone=work                   # Change default zone
sudo firewall-cmd --zone=work --list-all                    # Rules in 'work' zone
sudo firewall-cmd --zone=public --change-interface=ens33    # Assign interface to zone
sudo firewall-cmd --zone=work --add-service=https --permanent

# Zone definition files:
ls /usr/lib/firewalld/zones/        # Default zone configs (do not edit directly)
ls /etc/firewalld/zones/            # Custom overrides (edit these)
```

---

## 20. SELinux

**Check status:**
```bash
getenforce                          # Enforcing / Permissive / Disabled
sestatus                            # Detailed SELinux status

sudo setenforce 0                   # Temporarily set Permissive (no block, only log)
sudo setenforce 1                   # Temporarily set Enforcing (blocks and logs)

sudo vi /etc/selinux/config         # Permanent setting (requires reboot)
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
sudo restorecon -v file.html                        # Restore to default context (based on policy)
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
sudo semanage boolean -l                                # Full boolean list with descriptions
```

**Ports:**
```bash
sudo semanage port -l | grep http                       # Ports allowed for HTTP
sudo semanage port -a -t http_port_t -p tcp 8888        # Allow HTTP service on port 8888
```

**Audit logs:**
```bash
sudo ausearch -ts recent                                # Recent audit events
sudo ausearch -m avc                                    # All SELinux AVC denials
sudo ausearch -m avc -c nginx                           # Denials related to nginx
sudo journalctl -t setroubleshoot --since "1 hour ago"  # SELinux troubleshoot messages
```

**Emergency recovery (broken context on a critical path):**
```bash
# 1. Reboot → in GRUB menu press 'e' on the kernel entry
# 2. On the 'linux' line append: enforcing=0
#    (boots in SELinux permissive mode without disabling SELinux entirely)
# 3. Press Ctrl+X to boot
# 4. After boot, restore correct labels:
sudo restorecon -v -R /etc
# 5. Reboot normally to re-enter enforcing mode
```
