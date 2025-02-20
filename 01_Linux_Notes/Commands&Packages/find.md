---
title: find
tags:
  - command
  - find
created: 2025-02-20
---

# find

## Description
Find everything on a filesystem.
## Use Cases
- Searching for files and directories by name, type, or size.
- Finding files based on permissions or ownership.
- Searching for files modified within a specific time range.
- Executing actions on found files (e.g., delete, move, change permissions).
## Commands
#### Basic search
```bash
# Find a file by name (case-sensitive)
find /path/to/search -name "filename.txt"

# Find a file by name (case-insensitive)
find /path/to/search -iname "filename.txt"
```

#### Searching by type
```bash
# Find only directories
find /path/to/search -type d

# Find only files
find /path/to/search -type f

# Find symbolic links
find /path/to/search -type l
```

#### Searching by size

```bash
# Find files larger than 100MB
find /path/to/search -size +100M

# Find files smaller than 1KB
find /path/to/search -size -1k
```

#### Searching by time

```bash
# Find files modified in the last 7 days
find /path/to/search -mtime -7

# Find files accessed in the last 24 hours
find /path/to/search -atime -1

# Find files changed in the last 2 hours
find /path/to/search -cmin -120
```

#### Searching by Permissions

```bash
# Find files with exact permissions (e.g., 644)
find /path/to/search -perm 644

# Find files writable by others
find /path/to/search -perm -o=w
```

#### Searching by ownership

```bash
# Find files owned by a specific user
find /path/to/search -user username

# Find files owned by a specific group
find /path/to/search -group groupname
```

#### Using Regular expressions

```bash
# Find files matching a regex pattern
find /path/to/search -regex ".*/pattern.*"
```

> [!NOTE]
> When using `find` with the `-regex` option, the regular expression operates on the **full path** of the file, not just the filename or directory name. This is why you often see the pattern `".*/pattern.*"` — it ensures that the regex matches the full path, which includes all directories leading to the file.

#### Executing commands on found files

```bash
# Delete all .tmp files
find /path/to/search -name "*.tmp" -exec rm {} \;

# Change permissions of all .sh files
find /path/to/search -name "*.sh" -exec chmod +x {} \;

# Find and compress large log files
find /var/log -name "*.log" -size +100M -exec gzip {} \;

# Find files modified in the last 7 days and move them
find /path/to/search -type f -mtime -7 -exec mv {} /backup/location \;

# Find and delete empty directories
find /path/to/search -type d -empty -delete

# Find files larger than 1GB and list them with human-readable sizes
find /path/to/search -type f -size +1G -exec ls -lh {} \;

# Find all symbolic links and verify their targets
find /path/to/search -type l -exec ls -l {} \;

# Find all files and in current directory and change perm to 600
find . -type f -exec chmod 600 {} +
```

##### The Difference Between `+` and `\;` in `-exec`
- **`\;` (Semicolon)**: This tells `find` to execute the command once for each file it finds. It essentially runs the command separately for each file or directory, which can be inefficient if you're working with many files.
- **`+` (Plus Sign)**: This tells `find` to execute the command once for all the matching files at once, passing the files as arguments to the command. This is much more efficient, as it reduces the number of times the command is executed.

## Sources
- https://www.geeksforgeeks.org/find-command-in-linux-with-examples/
- man find
