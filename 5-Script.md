### 5. Script
Let's create the bash script called `monitoring.sh`.

script = sequence of commands in a file to be executed in sequencial order
#### Contents
1. Create the file
2. How to extract info from other commands: `grep` and `awk`
3. Content
4. Make it work every 10 mins in all terminals
5. The script

#### 🟦 1. Create the file
Write `monitoring.sh` file as root and put it in `/root` directory.
give the script execution permissions, i.e.:
```
$ touch monitoring.sh
$ chmod 755 monitoring.sh
```
> 🌳
> Some Bash scripting basics:
> - bash variables.
> Variable names are case-sensitive.
> Use the equal sign (=) to assign a value to a variable.
> We use `echo` to print.
> ```bash
> name="John"
> age=25
> echo "Name: $name"
> echo "Age: $age"
> ```
> - command substitution.
> Use $(command) to capture the output of a command.
> ```bash
> current_date=$(date)
> echo "Current date: $current_date"
> ```

#### 🟦 2. How to extract info from other commands: `grep` and `awk`
#### 🟦 3. Content
#### 🟦 4. Make it work every 10 mins in all terminals


The wall command allows us to broadcast a message to all users in all terminals. This can be incorporated into the monitoring.sh script or added later in cron.

To schedule the broadcast every 10 minutes, we need to enable cron:

`# systemctl enable cron`

Then start a crontab file for root:

`# crontab -e`

And add the job like this:

`*/10 * * * * bash /root/monitoring.sh`

Or, if the wall command isn't incorporated into the monitoring script:

`*/10 * * * * bash /root/monitoring.sh | wall`

From here, monitoring.sh will be executed every 10th minute. To make it execute every ten minutes from system startup, we can create a sleep.sh script that calculates the delay between server startup time and the tenth minute of the hour, then add it to the cron job to apply the delay.

`*/10 * * * * bash /root/sleep.sh && bash /root/monitoring.sh`
