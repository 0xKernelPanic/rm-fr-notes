---
title: Crontab
tags:
  - cron
  - bash_script
  - automation
created: 2025-02-19
---

# Crontab
## Description
`Crontab` is a scheduler for running commands at specified intervals on Unix-like systems.
### Crontab syntax
```txt
* * * * * <command_to_execute>
| | | | |
| | | | +---- Day of the week (0 - 7, Sunday = 0 or 7)
| | | +------ Month (1 - 12)
| | +-------- Day of the month (1 - 31)
| +---------- Hour (0 - 23)
+------------ Minute (0 - 59)
```

#### Special characters
 - `-` Used to specify ranges (e.g "10-12" in the hour field means "the hours 10, 11 and 12").
 - `/` Used to specify increments (e.g "5/15" in the seconds field means "the seconds 5, 20, 35, and 50)
 - `,` Used to specify additional values(e.g "MON,WED,FRI" in the day-of-week field means "the days Monday, Wednesday, and Friday").

####  Syntax instead of asterisks
```txt
@hourly
@weekly
@reboot
@Daily
```

For example
```txt
@Daily <command_to_execute>
```

### Files
- `/etc/crontab` - main system crontab file. 
- `/var/spool/cron/` - directory for storing crontabs defined by users.
- `/etc/cron.d/` - directory for storing system crontabs.

## Use Cases
- Automating system maintenance tasks
- Scheduling backups
- Running scripts at specific times
## Commands
```bash
crontab -l #list current cronjobs
crontab -e #edit crontab file
crontab -r #remove all cronjobs
crontab -u <username> --all-of-above #do action by specific user
```

## Examples
#### Backup files at 18:00
```bash
0 18 * * * tar -czf /home/jollyroger/Backup/Notebook_Backup_$(date +\%F).tar.gz -C /home/jollyroger/Documents/obsidian Notebook
```

> [!NOTE]
Please note the `\` character before `%`. Unescaped % is treated as a **newline** separator for command input.

## Sources
- man 5 crontab
- https://help.qlik.com/en-US/compose/December2024/Content/ComposeDWDL/Main/DW/Cron%20Format%20and%20Examples/cron_format_and_example.htm

