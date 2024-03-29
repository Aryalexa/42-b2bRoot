### 5. Script
Let's create the bash script called `monitoring.sh` that prints to all terminals every ten minutes.

script = sequence of commands in a file to be executed in sequencial order

So let's create it and write some commands in it using the bash programming language.

#### Steps
1. Create the file
2. Learn how to extract info from other commands: `grep` and `awk`
3. Content
4. Make it work every 10 mins in all terminals
5. Check if it works

#### 🟦 1. Create the file

> Where to save the script?
> - If the scripts are used by multiple users or if they are system-wide scripts, you might consider placing them in a directory such as `/usr/local/bin` or `/opt/scripts`.
> - If a user owns the `cron` job, storing the scripts in the home directory of the user is a common practice. For example, `/home/user1` or `/home/user1/scripts`
>
> Ensure that the directories you choose are included in the `PATH` environment variable or provide the full path to the script when configuring the `cron` job.

Create the file `monitoring.sh` and give it execution permissions.
```
$ cd /usr/local/bin
$ sudo touch monitoring.sh
$ sudo chmod 755 monitoring.sh
```
> 🌳
> Some Bash scripting basics:
> - bash variables.
> Variable names are case-sensitive.
> Use the equal sign `=` to assign a value to a variable.
> We use `echo` to print.
> ```bash
> name="John"
> age=25
> echo "Name: $name"
> echo "Age: ${age}yo"
> ```
> - command substitution.
> Use `$(command)` to capture the output of a command.
> ```bash
> current_date=$(date)
> echo "Current date: $current_date"
> ```
> - conditional statements.
> Use `-eq`, `-lt`, `-ge`, .. to compare if necessary.
> ```bash
> if [ $age -eq 99 ]; then echo "Yes"
> else echo "No"
> fi
> ```

#### 🟦 2. How to extract info from other commands: `grep` and `awk`


`grep` -> select line

```bash
grep [options] pattern [file(s)]
cat file | grep [options] pattern
```
- Its name stands for "Global Regular Expression Print"
- It is a command-line utility, used for searching and matching patterns within text files.
- The pattern can be a regular expression or plain text.
- By default, it returns all lines from a file (or input) that match the specified pattern.
- `grep "example" myfile.txt` prints all lines in myfile.txt that contain the word "example".

`awk` -> select columns in line

```bash
awk 'pattern { action }' input_file
cat input_file | awk 'pattern { action }'
```
- It is a text processing tool, designed for pattern scanning and processing.
- It processes the input line by line, performing the action to each line that matches the pattern given.
- The pattern can be omitted, applying the action to every line.
- `awk '{print $1, $3}' filename` prints the first and third columns of each line in the given file.
- In the action part you can print the column(s) directly or apply math operation and formatting if you need: `{printf("%.2f"), $1/$2}` 1.33, `{printf("%.1f%%"), $2}` 30.5%, `{print $3 " " $4}` 1 2, ...
- It can modify a variable for each line that matches the pattern: `cat lines.txt | awk 'PATTERN {total += $1} {total2 += $2} END {printf "%.1f", total+total2}'` will sum all first words in each line and print the total.
- And much more.

Using both you can filter specific words:
- `wc -l *.c | grep total | awk '{print $1}'` prints the number of lines for all .c files (use more than one file to make `wc` print the total calculations) in the current directory.

#### 🟦 3. Content
We want our script to show something like in this image:

![script message](script_output.png)

The idea is gathering all the information in variables and then printing them in order to reproduce the image above.
For the printing we can use `echo` or `wall`. `wall` not only prints, it broadcasts (the script should print to all terminals).
> 🌳
> `wall` command
> - It is used to send a message to all users who are currently logged in. A broadcast!
> - The word "wall" stands for "write all."
> ```shell
> $ wall "Attention to all users"
> $ echo "This is a broadcast message" | wall
> $ script_with_echo | wall
> ```
Something like this:
```bash
#!/bin/bash

INFO1=$(command_for_info1)
INFO2=$(command_for_info2)
wall "
      Info1    : $INFO1
      Info2    : $INFO2"
```

So let's gather all the information to show:

> Idea: use as many extra terminals as you want to try the commands or edit the script using ssh.

##### 🔸 1. Your OS architecture and its kernel version
The command `uname -a` gives us:
- System Kernel Name: The name of the operating system kernel (Linux)
- Node Name: The network node (hostname) of the machine on the network.
- Kernel Release: The release level of the operating system kernel. (6.1.0-18-amd64)
- Kernel Version: The version of the operating system kernel. (#1 SMP Debian 6.1.76-1 (2024-02-01))
- Machine Hardware: The hardware type of the machine. (x86_64)
- Operating system name. (GNU/Linux)

##### 🔸 2. Number of physical cores
See 3.
##### 🔸 3. Number of virtual cores
For physical and virtual cores we can use the file `/proc/cpuinfo`. See the lines with "physical id" and "processor". There will be one line per core, so counting the lines with the keyword will give us the number of cores (`wc -l` word count command counts lines with option `-l`).
##### 🔸 4. Current RAM memory available in your server and its usage as a percentage
Use `free` to show RAM data. See option `--mega`. Format: "Used/{Total}MB (percentage%)"
- So we'll need al least 3 variables: `ram_used`, `ram_total` and `ram_pct`.
- Use `grep` or `awk` to select the line with "Mem".
- Use `awk` to select the column we need for each var. For the percent we can do 
##### 🔸 5. Current memory available in your server and its usage as a percentage (disk)
Use `df` to show disk data. See options `--total`, `-h`. Format: "{Used}/{Total}GB (percentage%)"
- `df` (disk free) displays information about the disk space usage on filesystems. Each row is a filesystem (device or partition) mounted in the system.
      - The first column `1K-blocks` are the total size of the filesystem in blocks of 1KB. Use `-m` to see megas.
      - tmpfs is a temporary filesystem that resides in memory, it does not consume physical disk space. So better exclude it.
      - If you're interested in total disk space usage, you should include /boot. If you're only interested in filesystems that store user data, you may choose to exclude it.
- Use `grep` to select only lines that start with "/dev": `grep "^/dev"`
- Use `awk` to select the correct column and sum them all.
##### 🔸 6. Current percentage of core/cpu load
CPU usage is typically defined as the percentage of time the CPU spends executing non-idle tasks compared to the total time.
Format: {pct}%
🔘 Options 1 and 2: using `vmstat` (virtual memory stats) or `mpstat` (multiple processor stats). Both utilities provide information about CPU utilization, including user, system, idle, and wait times, which can be used to calculate CPU usage percentages.
- `vmstat 1 3`/`mpstat 1 3` shows the stats every 1 second, 3 times. See for yourself if the last row here is better than `vmstat`/`mpstat` output.
      - the first provides usage statistics over a brief period of time, allowing you to see how usage changes over time.
      - the second displays average usage statistics since the last reboot.
- Select the last row with `tail -1`.
- Then select the columns representing the idle time (check the man)
      - The idle time represents the percentage of time the CPU is not actively executing any tasks. It includes the time when the CPU is idle and waiting for work to do.
      - usage % = 100% - idle %
- Code:
      - `vmstat 1 1 | tail -n1 | awk '{print 100 - $15 "%"}'`, the 15th column typically corresponds to the idle CPU time
      - `mpstat 1 1 | tail -n1 | awk '{print 100 - $NF "%"}'`, $NF refers to the last column, which typically corresponds to the idle CPU time.
🔘 Option 3:
- `top -bn1 | grep "Cpu(s)" | awk '{print 100 - $8"%"}'` - this runs top in batch mode (-b) for one iteration (-n1), then filters the output to extract the CPU usage percentage.
##### 🔸 7. Date and time of last system boot
See `who -b`. Format: YYYY-MM-DD HH:mm
##### 🔸 8. Whether LVM is active or not
We want to print "yes" or "no" based on the LVM status.
Regarding LVM (Logical Volume Management), lsblk typically displays information about LVM volumes and their associated devices.
If LVM (Logical Volume Management) is not active or configured on the system, the lsblk command will only display information about physical disks and their partitions.
See `lsblk` (option `-o`?), it will show lvm info if it is active.
```
lvm_val=$(...)
lvm_active=$(if [ $lvm_val -gt 0 ]; then echo yes; else echo no; fi)
```
##### 🔸 9. Number of active connections
Let's count TCP connections.
See `ss -ta` or the file `/proc/net/sockstat`
##### 🔸 10. Number of users in the server
See `users` or `who`
##### 🔸 11. IPv4 address and MAC (Media Access Control) of your server
For IP address: see `hostname -I`.

For MAC address: see `ip link `.
##### 🔸 12. Number of commands executed as sudo
Remember the log? what was the name of the file?
`SUDO_LOG=$(grep COMMAND /var/log/sudo/sudo.log | wc -l)`

#### 🟦 4. Make it work every 10 mins in all terminals
1. All terminals
The `wall` command allows us to broadcast a message to all users in all terminals. This can be incorporated into the `monitoring.sh` script or added later in `cron`.
(If wall is not in the script we can use `bash /root/monitoring.sh | wall` as the command to schedule with `cron`.)

2. Schedule
> 🌳
>
> `cron`
> - It is a time-based job scheduler in Unix-like operating systems. The cron daemon enables cron functionality and runs in background.
> - It allows users to schedule tasks (commands or scripts) to run automatically at specified intervals or specific times.
> - These scheduled tasks are often referred to as "cron jobs".
> - The scheduling is stored in a configuration file called the "crontab" (cron table). You can see the table with `crontab -u <owner> -l`.
>    - Each user on a Unix system can have their own crontab file, and system-wide cron jobs may also be defined.
>    - Each line represents a scheduled cron job, showing the timing and command to be executed (when, who, what).
>    - A job line is like `minute hour day month day_of_week usr <command_or_/path/to/script_to_execute>`, where the user can be ommited and the owner of the crontab is the default user.
> - The cron daemon automatically starts at boot time and reads the crontab files to schedule and execute the specified tasks.

To schedule the execution of the script every 10 minutes:
- Edit the the root's crontab file with `sudo crontab -u root -e`.
- Add a new rule at end of the file: `*/10 * * * * /usr/local/bin/monitoring.sh` (`*/10` for "every" 10 minutes)

- (check cron service `sudo systemctl status cron.service`)
- (enable cron?? `# systemctl enable cron`)
