**LINUX COMMANDS**

## Table of Contents

1. [Linux Fundamentals — Theory First](#1-linux-fundamentals--theory-first)
2. [pwd](#2-pwd)
3. [ls](#3-ls)
4. [cd](#4-cd)
5. [mkdir](#5-mkdir)
6. [touch](#6-touch)
7. [cp](#7-cp)
8. [mv](#8-mv)
9. [rm](#9-rm)
10. [cat](#10-cat)
11. [less / more](#11-less--more)
12. [head / tail](#12-head--tail)
13. [find](#13-find)
14. [grep](#14-grep)
15. [chmod](#15-chmod)
16. [chown / chgrp](#16-chown--chgrp)
17. [ps](#17-ps)
18. [top / htop](#18-top--htop)
19. [kill / killall](#19-kill--killall)
20. [df / du](#20-df--du)
21. [free](#21-free)
22. [tar / zip / unzip](#22-tar--zip--unzip)
23. [ssh](#23-ssh)
24. [scp / rsync](#24-scp--rsync)
25. [wget / curl](#25-wget--curl)
26. [apt / yum / dnf (package management)](#26-apt--yum--dnf-package-management)
27. [sudo / su](#27-sudo--su)
28. [Environment Variables / export](#28-environment-variables--export)
29. [Pipes and Redirection](#29-pipes-and-redirection)
30. [alias](#30-alias)
31. [systemctl](#31-systemctl)
32. [cron / crontab](#32-cron--crontab)
33. [man / --help](#33-man---help)
34. [which / whereis / type](#34-which--whereis--type)
35. [history](#35-history)
36. [useradd / passwd / usermod](#36-useradd--passwd--usermod)
37. [ln (symlinks)](#37-ln-symlinks)
38. [netstat / ss](#38-netstat--ss)
39. [Real-Time Troubleshooting Scenarios](#39-real-time-troubleshooting-scenarios)

---

## 1. Linux Fundamentals — Theory First

Before the commands, three ideas make everything else make sense:

**1. "Everything is a file."** Devices, processes, sockets — Linux represents almost everything as a file somewhere in the filesystem. This is why commands like `cat` (meant for reading text files) can also read from devices, and why permissions apply uniformly everywhere.

**2. The Filesystem Hierarchy** — a single tree starting at `/` (root), no drive letters like Windows:
```
/            → root of everything
/home        → user home folders (/home/username)
/etc         → system-wide configuration files
/var         → variable data: logs, mail, caches (/var/log)
/usr         → installed programs and their files
/bin, /sbin  → essential system binaries/commands
/tmp         → temporary files, cleared on reboot
/root        → the root user's home folder (not the same as /)
```

**3. Permissions model** — every file/folder has an owner, a group, and permission bits for three categories: **owner**, **group**, **others**, each with **read (r), write (w), execute (x)**. Shown as `-rwxr-xr--`: first char is file type (`-` file, `d` directory, `l` symlink), then three groups of rwx.

---

## 2. pwd

**Definition:** "Print Working Directory" — shows the absolute path of the directory you're currently in.

**Syntax:**
```bash
pwd
```

**Example:**
```bash
pwd
# /home/raj/projects/git-guide
```

**When to use it:** Confirming your location before running relative-path commands (like `rm ./*`), especially in scripts.

**How to handle issues:** If output looks wrong (e.g. shows a symlinked path instead of the real path), use `pwd -P` to resolve symlinks to the physical path.

---

## 3. ls

**Definition:** Lists the contents (files and folders) of a directory.

**Syntax:**
```bash
ls
ls -l        # long format: permissions, owner, size, date
ls -a        # include hidden files (dotfiles)
ls -la       # combine both
ls -lh       # human-readable sizes (KB/MB/GB instead of raw bytes)
ls -lt       # sort by modified time, newest first
```

**Example:**
```bash
ls -la
# drwxr-xr-x  5 raj raj 4096 Sep  6 10:20 .
# -rw-r--r--  1 raj raj  220 Sep  6 10:20 .bashrc
# -rw-r--r--  1 raj raj 1500 Sep  6 10:22 README.md
```

**When to use it:** Constantly — checking what's in a directory, verifying file permissions/ownership, checking hidden config files.

**How to handle issues:**
- *Can't see hidden files (dotfiles like `.env`, `.gitignore`):* add `-a`.
- *Output is too cluttered to read sizes:* add `-h` for human-readable file sizes.

---

## 4. cd

**Definition:** "Change Directory" — moves your shell's current working location.

**Syntax:**
```bash
cd /path/to/folder
cd ..            # go up one level
cd ~              # go to home directory
cd -              # go back to the PREVIOUS directory you were in
cd                # (no argument) also goes home
```

**Example:**
```bash
cd /var/log
cd ..
cd -              # jumps back to /var/log instantly
```

**When to use it:** Navigating the filesystem tree in daily work.

**How to handle issues:**
- *"No such file or directory":* typo in path, or the folder doesn't exist — use `ls` first, or Tab-autocomplete the path as you type.
- *"Permission denied":* you lack execute permission on that directory (execute permission on a DIRECTORY means "allowed to enter/traverse it") — check with `ls -ld foldername`.

---

## 5. mkdir

**Definition:** Creates a new directory.

**Syntax:**
```bash
mkdir foldername
mkdir -p parent/child/grandchild    # create nested folders in one go, no error if parts already exist
```

**Example:**
```bash
mkdir -p projects/git-guide/docs
```

**When to use it:** Setting up new project structures, especially nested ones in a single command with `-p`.

**How to handle issues:**
- *"No such file or directory" when creating a nested path:* forgot `-p` — without it, `mkdir` refuses to create missing parent folders automatically.
- *"File exists":* a folder (or file) with that name is already there.

---

## 6. touch

**Definition:** Creates a new, empty file if it doesn't exist. If the file already exists, it just updates its "last modified" timestamp without changing content.

**Syntax:**
```bash
touch filename.txt
touch file1.txt file2.txt file3.txt
```

**Example:**
```bash
touch app.js
ls -l app.js
# -rw-r--r-- 1 raj raj 0 Sep  6 10:30 app.js
```

**When to use it:** Quickly creating empty placeholder files, or updating a file's timestamp for build/cache-busting purposes.

**How to handle issues:** If you meant to create a FOLDER but used `touch` by mistake, you'll get a plain empty file — use `mkdir` instead for directories.

---

## 7. cp

**Definition:** Copies files or directories from one location to another, leaving the original untouched.

**Syntax:**
```bash
cp source.txt destination.txt
cp -r sourcefolder/ destfolder/     # -r required for directories (recursive)
cp -i source.txt dest.txt            # prompt before overwriting an existing file
cp -v source.txt dest.txt            # verbose, shows what's being copied
```

**Example:**
```bash
cp -r ./configs ./configs-backup
```

**When to use it:** Backing up files before editing, duplicating a template/config, moving files between project folders.

**How to handle issues:**
- *"Omitting directory" error:* forgot `-r` when copying a folder.
- *Accidentally overwrote an important file:* use `-i` (interactive/confirm) as a habit for anything destination-critical; if already overwritten and no backup exists, recovery is not possible via `cp` itself — this is why version control (Git) or regular backups matter.

---

## 8. mv

**Definition:** Moves a file/folder to a new location, OR renames it if the destination is in the same directory with a different name — internally, a rename is treated as a "move" with the same path.

**Syntax:**
```bash
mv oldname.txt newname.txt          # rename
mv file.txt /path/to/folder/         # move
mv -i file.txt destination/          # prompt before overwrite
```

**Example:**
```bash
mv draft.md README.md
mv report.pdf ~/Documents/reports/
```

**When to use it:** Renaming files, reorganizing folder structure, moving completed work into archive folders.

**How to handle issues:**
- *Accidentally overwrote a file with the same name at destination:* `mv` overwrites silently by default — always use `-i` for anything important, since there's no built-in undo.
- *"mv: cannot move ... Directory not empty" (rare edge case with certain flags):* usually resolved by checking destination contents with `ls` first.

---

## 9. rm

**Definition:** Permanently deletes files or directories. **There is no trash bin / undo in the terminal by default** — this is the most dangerous basic command in Linux.

**Syntax:**
```bash
rm file.txt
rm -r foldername/          # recursive, required for directories
rm -rf foldername/         # recursive + force (no confirmation, ignores non-existent files)
rm -i file.txt              # prompt before each deletion
```

**Example:**
```bash
rm -i old-notes.txt
# rm: remove regular file 'old-notes.txt'? y
```

**When to use it:** Deleting files/folders you're certain you no longer need.

**How to handle issues:**
- *Ran `rm -rf` on the wrong folder:* there is generally **no recovery** without a backup or snapshot (unlike Git, which has `reflog`) — this is why `rm -rf` should always be typed slowly and double-checked, especially with wildcards.
- **Golden safety rule:** never run `rm -rf` with a variable path in a script without validating it first (`rm -rf $VAR/*` is catastrophic if `$VAR` is accidentally empty — it becomes `rm -rf /*`).
- *Want a safer habit:* alias `rm` to `rm -i` in your `.bashrc`, or use a trash-cli tool (`trash-put`) instead of raw `rm` for anything you're not 100% sure about.

---

## 10. cat

**Definition:** "Concatenate" — prints file contents directly to the terminal, or combines multiple files together.

**Syntax:**
```bash
cat file.txt
cat file1.txt file2.txt > combined.txt
cat -n file.txt          # show line numbers
```

**Example:**
```bash
cat /etc/os-release
# NAME="Ubuntu"
# VERSION="24.04 LTS"
```

**When to use it:** Quickly viewing small files' full contents, combining multiple files into one.

**How to handle issues:** For large files, `cat` dumps everything at once and floods your terminal — use `less` instead (see §11) for anything long.

---

## 11. less / more

**Definition:** Both display file contents one screen at a time, letting you scroll through large files without flooding the terminal. `less` is the modern, more capable version (supports backward scrolling, search); `more` is the older, more limited original.

**Syntax:**
```bash
less file.txt
# inside less: space = next page, b = previous page, / to search, q to quit
more file.txt
```

**Example:**
```bash
less /var/log/syslog
# then type /error and press Enter to search for "error"
```

**When to use it:** Reading large log files, config files, or any long text file without overwhelming your terminal.

**How to handle issues:** If you're stuck inside `less` and don't know how to exit, press `q`.

---

## 12. head / tail

**Definition:** `head` shows the first N lines of a file (default 10). `tail` shows the last N lines (default 10) — critically, `tail -f` "follows" a file, showing new lines as they're written in real time.

**Syntax:**
```bash
head file.txt
head -n 20 file.txt
tail file.txt
tail -n 50 file.txt
tail -f /var/log/app.log      # live-follow, used constantly for watching logs
```

**Example:**
```bash
tail -f /var/log/nginx/access.log
# streams new log entries live as requests come in — press Ctrl+C to stop
```

**When to use it:** `tail -f` is one of the most-used commands in real production work — watching an application's logs live while debugging or during a deployment.

**How to handle issues:** If `tail -f` isn't showing new lines even though the app is clearly writing to the log, the app might be writing to a NEW file (log rotation) — use `tail -F` (capital F) instead, which re-attaches to the file if it gets rotated/recreated.

---

## 13. find

**Definition:** Searches the filesystem for files/directories matching criteria — name, type, size, modification time, permissions — recursively through a directory tree.

**Syntax:**
```bash
find /path -name "*.log"
find . -type f -name "*.js"        # files only
find . -type d -name "node_modules" # directories only
find . -mtime -7                     # modified in the last 7 days
find . -size +100M                   # larger than 100MB
find . -name "*.tmp" -delete         # find AND delete matches
```

**Example:**
```bash
find /var/log -name "*.log" -mtime +30
# lists log files older than 30 days — often piped into a cleanup command
```

**When to use it:** Locating files by name/pattern across a large directory tree, cleaning up old files, finding oversized files eating disk space.

**How to handle issues:**
- *`find` is very slow on a huge tree:* narrow the starting path as much as possible instead of searching from `/`.
- *Accidentally used `-delete` too broadly:* always run the `find` command WITHOUT `-delete` first to review the matched list before adding it.

---

## 14. grep

**Definition:** "Global Regular Expression Print" — searches text (files or piped input) for lines matching a pattern.

**Syntax:**
```bash
grep "pattern" file.txt
grep -r "pattern" ./folder            # recursive, search all files in a folder
grep -i "pattern" file.txt            # case-insensitive
grep -n "pattern" file.txt            # show line numbers
grep -v "pattern" file.txt            # invert match — show lines that DON'T match
grep -E "pattern1|pattern2" file.txt  # extended regex, multiple patterns
```

**Example:**
```bash
grep -rn "TODO" ./src
# src/app.js:42:// TODO: handle null case
```

**When to use it:** Searching codebases for specific strings/functions, filtering log files for errors, combined constantly with pipes (`cat file.log | grep "ERROR"`).

**How to handle issues:**
- *Special characters in your pattern aren't matching as expected:* you likely need `-E` (extended regex) or need to escape characters like `.`, `*`, `[` properly.
- *Too many irrelevant matches:* narrow with `-w` (whole word only) or combine with more specific context using `grep -B 2 -A 2` (2 lines before/after each match).

---

## 15. chmod

**Definition:** Changes a file or directory's permission bits — read, write, execute for owner/group/others.

**Syntax:**
```bash
chmod 755 file.sh          # numeric mode: owner=rwx(7), group=rx(5), others=rx(5)
chmod +x script.sh          # add execute permission for everyone
chmod -R 644 folder/        # recursive, apply to all files inside
chmod u+w,g-w file.txt      # symbolic mode: add write for user, remove write for group
```

**Numeric permission reference:**
| Number | Meaning |
|---|---|
| 7 | rwx (read+write+execute) |
| 6 | rw- (read+write) |
| 5 | r-x (read+execute) |
| 4 | r-- (read only) |
| 0 | --- (no permission) |

**Example:**
```bash
chmod +x deploy.sh
./deploy.sh
```

**When to use it:** Making a script executable, restricting sensitive file access (e.g. `chmod 600` on SSH private keys — owner read/write only).

**How to handle issues:**
- *"Permission denied" trying to run a script:* it's missing execute permission — `chmod +x scriptname.sh`.
- *SSH key rejected with "permissions are too open":*
  ```bash
  chmod 600 ~/.ssh/id_ed25519
  ```
- *Used `chmod -R 777` broadly (common bad habit):* this is a real security risk — it grants read/write/execute to EVERYONE, including other users on the system. Prefer the minimum permission that actually works.

---

## 16. chown / chgrp

**Definition:** `chown` changes a file's owner (and optionally group). `chgrp` changes only the group.

**Syntax:**
```bash
chown user file.txt
chown user:group file.txt
chown -R user:group folder/    # recursive
chgrp groupname file.txt
```

**Example:**
```bash
sudo chown -R raj:raj /var/www/myapp
```

**When to use it:** Fixing ownership after copying files as root, setting up correct ownership for a web server (e.g. `www-data`) to read/write app files.

**How to handle issues:**
- *"Operation not permitted":* changing ownership almost always requires `sudo` — regular users can't give away files they own to someone else without elevated privileges.
- *App can't write to its own files after a deploy:* usually an ownership mismatch between the deploying user and the app's running user — `chown -R` to the correct service user fixes it.

---

## 17. ps

**Definition:** Shows a snapshot of currently running processes.

**Syntax:**
```bash
ps
ps aux              # ALL processes, all users, detailed format — the most commonly used form
ps -ef               # alternative detailed format
ps aux | grep nginx  # find a specific process
```

**Example:**
```bash
ps aux | grep node
# raj   4521  2.3  1.2  984532  98234 ?  Sl  10:15  0:12 node server.js
```

**When to use it:** Checking if a process/service is running, finding a process's PID (process ID) to kill it, investigating what's consuming resources.

**How to handle issues:** Output too wide/wraps badly in a narrow terminal — pipe to `less` or widen the terminal: `ps aux | less`.

---

## 18. top / htop

**Definition:** Real-time, continuously updating view of system resource usage (CPU, memory) per process. `htop` is a more user-friendly, colorized, interactive alternative (usually needs separate installation).

**Syntax:**
```bash
top
htop
# inside top: press 'q' to quit, 'k' to kill a process by PID, 'M' to sort by memory
```

**Example:**
```bash
top
# shows live CPU%, MEM%, and top processes, refreshing every few seconds
```

**When to use it:** Diagnosing "why is the server slow / using 100% CPU" in real time.

**How to handle issues:** If `htop` isn't installed: `sudo apt install htop` (Debian/Ubuntu) or `sudo yum install htop` (RHEL/CentOS).

---

## 19. kill / killall

**Definition:** Sends a signal to a running process — most commonly to terminate it. `kill` targets a process by PID (process ID); `killall` targets by process NAME (can match multiple processes at once).

**Syntax:**
```bash
kill <PID>              # sends SIGTERM (graceful shutdown request)
kill -9 <PID>            # sends SIGKILL (force kill, immediate, no cleanup)
killall processname       # kill all processes matching a name
```

**Example:**
```bash
ps aux | grep node          # find the PID first
kill 4521                   # graceful
kill -9 4521                # force, if graceful didn't work
```

**When to use it:** Stopping a hung/unresponsive process, restarting a service manually, freeing up a port that's stuck in use.

**How to handle issues:**
- *Process won't die with plain `kill`:* it may be ignoring SIGTERM (common for hung processes) — escalate to `kill -9` (SIGKILL, cannot be ignored).
- *"Operation not permitted":* the process belongs to another user (often root/a system service) — needs `sudo kill <PID>`.
- *Port still shows as "in use" after killing the process:* the OS sometimes takes a moment to release it (TIME_WAIT state) — wait a few seconds, or check with `ss -tulnp` to confirm nothing else is actually holding it.

---

## 20. df / du

**Definition:** `df` ("disk free") shows overall disk space usage per mounted filesystem/partition. `du` ("disk usage") shows how much space specific files/folders are consuming.

**Syntax:**
```bash
df -h                       # human-readable disk space overview
du -sh foldername/           # total size of one folder, summarized
du -sh */                    # size of each subfolder in current directory
du -ah folder/ | sort -rh | head -20    # top 20 largest files/folders
```

**Example:**
```bash
df -h
# Filesystem  Size  Used  Avail  Use%  Mounted on
# /dev/sda1    50G   38G    10G   80%  /

du -sh /var/log
# 2.3G   /var/log
```

**When to use it:** "Disk is full" investigations — `df` tells you WHICH partition is full, `du` tells you WHAT is taking up the space.

**How to handle issues:**
- *`df` shows disk full but `du` on visible folders doesn't add up:* very common cause is deleted-but-still-open files (a process still holds a file handle to a deleted file, so the space isn't actually freed) — check with `lsof | grep deleted`, and restart the holding process to release it.
- *Need to find what's eating space quickly:* `du -sh /* 2>/dev/null | sort -rh | head -10` from root to find the biggest top-level offenders.

---

## 21. free

**Definition:** Shows current RAM and swap memory usage.

**Syntax:**
```bash
free -h        # human-readable
```

**Example:**
```bash
free -h
#               total   used   free   shared  buff/cache  available
# Mem:           16G     6.2G   1.1G    200M      8.7G       9.1G
```

**When to use it:** Diagnosing memory pressure, checking if swap is being heavily used (a sign of RAM exhaustion).

**How to handle issues:** "Available" is the number that matters, not "free" — Linux aggressively uses free RAM for disk caching (`buff/cache`), which is reclaimed instantly when needed, so a low "free" number alone does NOT mean you're low on memory.

---

## 22. tar / zip / unzip

**Definition:** `tar` bundles multiple files into a single archive (optionally compressed) — the standard Linux archive format. `zip`/`unzip` handle the more cross-platform `.zip` format.

**Syntax:**
```bash
tar -cvf archive.tar folder/        # create
tar -czvf archive.tar.gz folder/    # create + gzip compress (most common)
tar -xvf archive.tar                 # extract
tar -xzvf archive.tar.gz             # extract gzip-compressed
tar -tvf archive.tar                 # list contents without extracting

zip -r archive.zip folder/
unzip archive.zip
```

**Example:**
```bash
tar -czvf backup-2026-09-06.tar.gz /var/www/myapp
```

**When to use it:** Creating backups, packaging a project for transfer, extracting downloaded software.

**How to handle issues:**
- *Flag order confusion:* remember `c`reate, e`x`tract, `t`list, always paired with `v`erbose and `f`ile (which must come last since it's followed by the filename).
- *"Cannot open: No such file or directory" on extract:* check the exact filename/extension with `ls`, tar is strict about matching `.tar.gz` vs `.tgz` naming only cosmetically — the actual flags matter more than the extension.

---

## 23. ssh

**Definition:** "Secure Shell" — opens an encrypted remote terminal session to another machine.

**Syntax:**
```bash
ssh user@hostname
ssh user@hostname -p 2222        # custom port
ssh -i ~/.ssh/custom_key user@hostname   # specify a particular private key
```

**Example:**
```bash
ssh raj@192.168.1.50
# opens a remote shell session on that machine
```

**When to use it:** Remotely administering servers, deploying code, running commands on a different machine.

**How to handle issues:**
- *"Permission denied (publickey)":* your public key isn't in the remote server's `~/.ssh/authorized_keys`, or you're using the wrong private key.
- *"Connection refused":* SSH service (`sshd`) isn't running on the target, or a firewall is blocking port 22.
- *"Host key verification failed":* the remote server's identity changed (common after a server rebuild) — if expected, remove the old entry:
  ```bash
  ssh-keygen -R hostname
  ```

---

## 24. scp / rsync

**Definition:** `scp` ("secure copy") copies files between machines over SSH. `rsync` does the same but far more efficiently — it only transfers the DIFFERENCES between source and destination, making repeated syncs much faster, and supports resuming interrupted transfers.

**Syntax:**
```bash
scp file.txt user@host:/remote/path/
scp -r folder/ user@host:/remote/path/
scp user@host:/remote/file.txt ./local/

rsync -avz source/ user@host:/remote/destination/
rsync -avz --delete source/ dest/    # also delete files at destination that no longer exist at source
```

**Example:**
```bash
rsync -avz ./build/ raj@server:/var/www/myapp/
```

**When to use it:** `scp` for quick one-off file transfers; `rsync` for anything repeated (deployments, backups) since it's dramatically faster on subsequent runs.

**How to handle issues:**
- *`rsync` transfer stops midway:* just re-run the same command — rsync resumes/skips already-matching files automatically.
- *Trailing slash confusion (`source/` vs `source`):* a trailing slash on the SOURCE means "copy the contents of this folder"; no trailing slash means "copy the folder itself" — this trips up almost everyone at least once.

---

## 25. wget / curl

**Definition:** Both download content from URLs from the command line. `wget` is simpler, built for straightforward downloading (including recursive site downloads). `curl` is more versatile — supports every HTTP method, headers, and is the standard tool for testing/interacting with APIs.

**Syntax:**
```bash
wget https://example.com/file.zip
curl -O https://example.com/file.zip       # save with the remote filename
curl -X POST https://api.example.com/data -H "Content-Type: application/json" -d '{"key":"value"}'
curl -I https://example.com                 # headers only, check status/response
```

**Example:**
```bash
curl -s https://api.github.com/users/octocat | grep "login"
```

**When to use it:** Downloading files/installers, testing an API endpoint quickly, checking if a website/service is up (`curl -I`).

**How to handle issues:**
- *"Could not resolve host":* DNS issue or no internet connectivity — test with `ping 8.8.8.8` to isolate DNS vs full connectivity.
- *SSL certificate errors:* usually a genuine expired/misconfigured certificate — avoid `-k`/`--insecure` (which skips verification) except for deliberate local/dev testing, since it defeats the purpose of HTTPS.

---

## 26. apt / yum / dnf (package management)

**Definition:** Package managers that install, update, and remove software, automatically resolving dependencies. `apt` is used on Debian/Ubuntu-based systems; `yum`/`dnf` on RHEL/CentOS/Fedora-based systems.

**Syntax:**
```bash
sudo apt update                  # refresh the list of available packages/versions
sudo apt upgrade                 # upgrade all installed packages
sudo apt install packagename
sudo apt remove packagename
sudo apt autoremove              # clean up unused dependency packages

sudo dnf install packagename     # RHEL/Fedora equivalent
```

**Example:**
```bash
sudo apt update && sudo apt install nginx
```

**When to use it:** Installing/updating/removing any software on a Linux server.

**How to handle issues:**
- *"Unable to locate package":* run `sudo apt update` first — your local package index is stale.
- *"E: Could not get lock /var/lib/dpkg/lock":* another package operation is already running (or crashed mid-way):
  ```bash
  sudo lsof /var/lib/dpkg/lock       # check if a process is genuinely still using it
  sudo rm /var/lib/dpkg/lock         # only if confirmed no process is actually running — otherwise risks a corrupted package state
  sudo dpkg --configure -a
  ```

---

## 27. sudo / su

**Definition:** `sudo` ("superuser do") runs a single command with elevated (root) privileges, then returns to your normal user. `su` ("switch user") starts an entirely new shell session AS another user (commonly root), staying elevated until you exit that shell.

**Syntax:**
```bash
sudo command
sudo -i              # start an interactive root shell
su - username        # switch to another user's shell entirely
```

**Example:**
```bash
sudo apt install htop
sudo systemctl restart nginx
```

**When to use it:** Any command that touches system-level files/services/ports below 1024, installing software, managing services.

**How to handle issues:**
- *"user is not in the sudoers file":* your user account hasn't been granted sudo privileges — must be added by an admin/root user via `usermod -aG sudo username` (Debian/Ubuntu).
- *Habit warning:* prefer `sudo command` over `sudo -i`/full root shells whenever possible — staying in a root shell longer than needed increases the chance of an accidental destructive command.

---

## 28. Environment Variables / export

**Definition:** Named values available to your shell and any programs it launches — used for configuration like `PATH` (where the shell looks for commands), API keys, and app settings.

**Syntax:**
```bash
echo $HOME                 # view a variable
export MY_VAR="value"      # set for current session (and any child processes)
echo 'export MY_VAR="value"' >> ~/.bashrc   # make it permanent (persists across sessions)
unset MY_VAR                # remove it
env                          # list ALL current environment variables
```

**Example:**
```bash
export NODE_ENV=production
node server.js
```

**When to use it:** Configuring an application without hardcoding values into the code (API keys, database URLs, environment mode).

**How to handle issues:**
- *Variable "disappears" in a new terminal window:* `export` only affects the CURRENT shell session — add it to `~/.bashrc` (or `~/.zshrc`) for persistence across sessions.
- *"command not found" for a program you just installed:* its install location isn't in your `$PATH` — check with `echo $PATH`, add the missing directory: `export PATH=$PATH:/new/directory`.

---

## 29. Pipes and Redirection

**Definition:** Redirection sends a command's output to a file instead of the screen. Pipes (`|`) send one command's output directly as another command's input — this is what makes small Linux tools genuinely powerful when combined.

**Syntax:**
```bash
command > file.txt        # redirect output to file (OVERWRITES the file)
command >> file.txt       # redirect output, APPEND to file instead of overwriting
command 2> errors.txt     # redirect only error output
command > out.txt 2>&1    # redirect both normal output AND errors to the same file

command1 | command2       # pipe: command1's output becomes command2's input
```

**Example:**
```bash
ps aux | grep node | grep -v grep      # find node processes, excluding the grep command itself from results
cat access.log | grep "500" | wc -l    # count how many log lines contain "500" (server error) status codes
```

**When to use it:** Chaining simple tools together to answer a specific question quickly, without writing a script — this is the core "Unix philosophy" workflow.

**How to handle issues:**
- *Used `>` and accidentally erased a file's previous content:* `>` always overwrites — use `>>` if you meant to append. There is no undo for this; if the file matters, keep it in version control or backed up.
- *Pipe seems to do nothing:* check that the first command actually produces output on its own before piping (test each stage separately by running it alone first).

---

## 30. alias

**Definition:** Creates a shortcut/nickname for a longer command.

**Syntax:**
```bash
alias ll='ls -la'
alias gs='git status'
# add to ~/.bashrc or ~/.zshrc to make permanent
unalias ll
```

**Example:**
```bash
echo "alias ll='ls -la'" >> ~/.bashrc
source ~/.bashrc     # reload the config file to activate it immediately
ll
```

**When to use it:** Speeding up frequently-typed commands in your daily workflow.

**How to handle issues:** Alias added but not working in a new terminal — you edited the file but forgot to `source` it, or added it to the wrong file (check which shell you're using with `echo $SHELL`, then edit the matching `.bashrc`/`.zshrc`).

---

## 31. systemctl

**Definition:** Controls `systemd`-managed services (start, stop, restart, enable-at-boot) — the standard service manager on most modern Linux distributions.

**Syntax:**
```bash
sudo systemctl start servicename
sudo systemctl stop servicename
sudo systemctl restart servicename
sudo systemctl status servicename
sudo systemctl enable servicename    # start automatically on boot
sudo systemctl disable servicename
journalctl -u servicename -f          # view/follow that service's logs
```

**Example:**
```bash
sudo systemctl restart nginx
sudo systemctl status nginx
```

**When to use it:** Managing any background service — web servers, databases, your own deployed applications configured as a service.

**How to handle issues:**
- *Service won't start:* check the actual error with:
  ```bash
  sudo systemctl status servicename
  journalctl -u servicename -n 50    # last 50 log lines for that service
  ```
- *Changed a service's config file but it's not taking effect:* you likely need to restart the service (`systemctl restart`), and if you edited the actual `.service` unit file itself, also run `sudo systemctl daemon-reload` first.

---

## 32. cron / crontab

**Definition:** `cron` is a time-based job scheduler; `crontab` is the command to edit a user's scheduled jobs — used for anything that needs to run automatically on a recurring schedule (backups, cleanup scripts, reports).

**Syntax:**
```bash
crontab -e          # edit your scheduled jobs
crontab -l          # list current scheduled jobs
```

**Cron time format:** `minute hour day month weekday command`
```bash
0 2 * * * /home/raj/backup.sh          # every day at 2:00 AM
*/15 * * * * /home/raj/check.sh         # every 15 minutes
0 0 1 * * /home/raj/monthly-report.sh   # midnight on the 1st of every month
```

**When to use it:** Automated backups, log rotation, scheduled reports/health checks, any recurring maintenance task.

**How to handle issues:**
- *Cron job doesn't seem to run:* cron uses a MINIMAL environment (no `$PATH` from your normal shell, no loaded profile) — always use absolute paths inside cron scripts (`/usr/bin/python3` instead of just `python3`), and redirect output to a log file to debug: `* * * * * /path/script.sh >> /home/raj/cron.log 2>&1`.
- *Wrong time triggered:* double check server timezone with `timedatectl` — cron uses the SYSTEM's timezone, not necessarily yours.

---

## 33. man / --help

**Definition:** `man` (manual) shows the full, detailed documentation page for a command. Most commands also support a quick `--help` flag for a shorter summary of options.

**Syntax:**
```bash
man ls
ls --help
```

**Example:**
```bash
man grep
# opens the full grep manual — press 'q' to exit, '/' to search within it
```

**When to use it:** Any time you forget a specific flag's exact behavior — this is always more reliable than guessing or relying on memory, since flag behavior can vary subtly by Linux distribution/version.

**How to handle issues:** If `man` isn't installed on a minimal server image: `sudo apt install man-db`, or fall back to `command --help` which is usually built in regardless.

---

## 34. which / whereis / type

**Definition:** `which` shows the full path of the executable that would run for a given command name. `whereis` also shows related binary/man-page/source locations. `type` shows whether a command is a shell builtin, alias, or external binary.

**Syntax:**
```bash
which python3
whereis python3
type ll
```

**Example:**
```bash
which node
# /usr/local/bin/node
```

**When to use it:** Debugging "wrong version running" issues — e.g. multiple Python/Node installations where the wrong one is being picked up from `$PATH`.

**How to handle issues:** If `which` shows an unexpected path, check the order of directories in `$PATH` (`echo $PATH`) — the first matching directory wins, so an old version earlier in the path shadows a newer one installed elsewhere.

---

## 35. history

**Definition:** Shows a list of previously run commands in your current shell session (and often persisted across sessions in a history file).

**Syntax:**
```bash
history
history | grep "docker"     # search your command history
!123                          # re-run command number 123 from history
!!                            # re-run the previous command
!!:s/old/new/                 # re-run previous command, replacing "old" with "new"
```

**Example:**
```bash
history | grep "ssh"
# 245  ssh raj@192.168.1.50
```

**When to use it:** Recalling a long/complex command you ran earlier instead of retyping it.

**How to handle issues:** History missing commands from a previous session — some shells limit history size (`$HISTSIZE`) or don't save it properly on abrupt terminal closure; increase `HISTSIZE`/`HISTFILESIZE` in `~/.bashrc` if this happens often.

---

## 36. useradd / passwd / usermod

**Definition:** `useradd` creates a new user account. `passwd` sets/changes a user's password. `usermod` modifies an existing user's settings (like adding them to a group).

**Syntax:**
```bash
sudo useradd -m newusername       # -m creates their home directory too
sudo passwd newusername
sudo usermod -aG groupname username   # add user to an additional group (-a = append, don't remove existing groups)
```

**Example:**
```bash
sudo useradd -m deploy
sudo passwd deploy
sudo usermod -aG sudo deploy
```

**When to use it:** Setting up a new service/deploy account on a server, granting a user additional permissions via group membership.

**How to handle issues:**
- *Forgot `-a` when adding a group with `usermod -G`:* without `-a`, `-G` REPLACES all of the user's existing groups with only the ones listed — a common, disruptive mistake. Always use `-aG`, never plain `-G`, unless you genuinely intend to wipe existing group memberships.
- *New group membership doesn't take effect:* the user needs to log out and back in (or run `newgrp groupname`) for group changes to apply to their current session.

---

## 37. ln (symlinks)

**Definition:** Creates links between files. A **symbolic link (symlink)** is a pointer/shortcut to another file's path — if the original is deleted, the symlink breaks. A **hard link** is a second directory entry pointing to the exact same underlying data, and remains valid even if the original path is deleted.

**Syntax:**
```bash
ln -s /path/to/original /path/to/symlink    # symbolic link (most common)
ln /path/to/original /path/to/hardlink       # hard link
```

**Example:**
```bash
ln -s /var/www/myapp/releases/v2.1.0 /var/www/myapp/current
```

**When to use it:** Common in deployment setups — symlink a "current" folder to whichever release version is active, so switching versions/rollbacks is just re-pointing the symlink instead of moving files.

**How to handle issues:**
- *"Too many levels of symbolic links":* a circular reference (a symlink that eventually points back to itself).
- *Symlink shows as broken (red, in `ls -la` output) after moving the original file:* symlinks store a path, not the actual data — if the target moves, the link must be recreated pointing at the new location.

---

## 38. netstat / ss

**Definition:** Show active network connections and listening ports. `ss` ("socket statistics") is the modern, faster replacement for the older `netstat` (which may not even be installed by default on newer systems).

**Syntax:**
```bash
ss -tulnp        # TCP/UDP listening ports, numeric, with the owning process
netstat -tulnp   # older equivalent, if installed
```

**Example:**
```bash
ss -tulnp | grep 3000
# tcp LISTEN 0 128 0.0.0.0:3000 0.0.0.0:*  users:(("node",pid=4521))
```

**When to use it:** "Port already in use" errors — finding exactly which process is holding a port so you can decide whether to kill it or pick a different port.

**How to handle issues:**
```bash
ss -tulnp | grep :3000     # find the PID using port 3000
kill -9 <PID>               # free it up
```

---

## 39. Real-Time Troubleshooting Scenarios

### "Disk is full, application crashed"
```bash
df -h                                       # confirm which partition is full
du -sh /var/log/* | sort -rh | head -10     # find what's consuming space (logs are the usual culprit)
# clear old logs, or configure log rotation (logrotate) going forward
```

### "Port already in use" when starting an app
```bash
ss -tulnp | grep :3000
kill -9 <PID>
```

### "Permission denied" running a script you just wrote
```bash
chmod +x script.sh
./script.sh
```

### "Service won't start after a config change"
```bash
sudo systemctl daemon-reload      # if you edited the .service unit file itself
sudo systemctl restart servicename
journalctl -u servicename -n 50   # see the actual error
```

### "Server is slow, need to find the culprit"
```bash
top                    # or htop — check CPU/MEM usage live
ps aux --sort=-%cpu | head -10     # top 10 CPU-consuming processes
ps aux --sort=-%mem | head -10     # top 10 memory-consuming processes
```

### "Need to check if a remote host/service is reachable"
```bash
ping example.com          # basic reachability
curl -I https://example.com   # check HTTP status specifically
ssh -v user@host           # verbose SSH connection attempt, shows exactly where it fails
```

### "Deployed new code but old version still running"
```bash
sudo systemctl restart yourapp.service
journalctl -u yourapp.service -f      # watch it start up live, confirm no errors
```

### "Accidentally ran a long-running command in the foreground, need my terminal back"
```bash
# Ctrl+Z to suspend it
bg          # resume it in the background
jobs        # list background jobs
fg          # bring it back to the foreground
```

---

*Keep this file updated as you hit new commands or new real production issues — a living reference beats a one-time read.*
