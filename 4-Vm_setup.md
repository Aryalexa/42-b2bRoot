## 4. Set up

We're going to set up some tools, sevices and configurations in our Debian VM.

What we want:
- A SSH service
- A firewall
- Managing users and groups
- Change some `sudo` configurations
- A strict password policy
- A script (we'll leave this for step-5)

So we need some system changes to add and configure all this, we need some elevated privileges. Safer than using the root account, the `sudo` command is used for these tasks. 

### 🟪 `sudo` installation

> 🌳 
> 
> `sudo` (Superuser Do) is a command-line utility that lets you run a command as a "superuser".
> - It needs to be installed and configured.
> - It allows authorized users to execute commands with elevated privileges.
> - It provides fine-grained access control, allowing administrators to define which users or groups can run specific commands with elevated privileges.
> - It maintains a log (audit trail) of each command executed with elevated privileges, (who, when and what).
> - It adheres to the *principle of least privilege*, a security best practice.
> 


Let's install `sudo` on Debian. For this we'll use the package manager again (`apt` in our case). 
- Installing tools requires high privileges so this time we are going to use the root account: we *substitute user* to root with the command `su -`/`su - root` and introduce the root password.
- Update (fetch) and upgrade (pull) installed packages and start installation.
```
$ su -
# apt update
# apt upgrade
# apt install sudo
```

> 🌳 
> 
> `su` (substitute user) command
> - root is used if no username is provided
> - After authentication, your shell will be switched to that user's environment.
> - The hyphen (`-`) option sets up the environment as if you had logged in directly as the specified user. Preserving the environment variables of the target user. We need this to unlock all root capabilities.
> - To return to the original user's session, simply type `exit` or press Ctrl+D.
> - When switching from normal user to root, the prompt changes from `$` to `#`.
>


### 🟪 users and groups management

Unix-like systems support multiple users. Each user has their own home directory, their own files, and their own permissions.
We want:
- two users: root (this one exists by default and is the ultimate superuser) and a user with your login.
- Your user must be part of the groups `user42` and `sudo` (the users that can use sudo).

> 🌳 
> 
> *Commands for users and groups management*

> - create new users and groups, and create user into a group. The GID (group ID) is shown when a group is created.
> ```
> $ sudo adduser USERNAME
> $ sudo addgroup GROUPNAME
>
> $ sudo adduser USERNAME GROUPNAME
> // existing user and group:
> $ sudo usermod -aG GROUPNAME USERNAME 
> ```
> - To check if group exists and its users
>  ```
>  $ getent group GROUPNAME
>  ```
>  - check if your current user has `sudo` privileges. If you are a `sudo` user, the answer must be `root`
>  ```
>  sudo whoami
>  ```
>  

Now we can create our user while adding it to `sudo`, exit root session, create the `user42` group and include our user there too.

```
# adduser my_login sudo
# exit
$ sudo addgroup user42 
$ sudo adduser my_login user42

$ getent group sudo
$ getent group user42
```

### 🟪 `sudo` configuration

Let's change some `sudo` configurations. We are going to use the `visudo` command (that let's you edit sintax-wise safely the sudo config file `/etc/sudoers` using a tmp file).
- Access to edit the sudo config file with the command `sudo visudo`
- Find the "default" lines. We want these settings:
  - Three tries at most to authenticate as `sudo`
  - Show message if authentication password is wrong
  - Save input and output for every command executed as `sudo` in the directory `/var/log/sudo/`. ( ☢️ If `var/log/sudo` directory does not exist, `mkdir var/log/sudo`)
  - `TTY` mode must be active for security reasons.  
  - The usage of `sudo` must be restricted for some directories:
    - `/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin`
- To achieve the above, we need to add/edit the following lines:
```	
Defaults  passwd_tries=3
Defaults  badpass_message="Wrong password. Try again!"
Defaults  logfile="/var/log/sudo/sudo.log"
Defaults  log_input, log_output
Defaults  iolog_dir="/var/log/sudo"
Defaults  requiretty
Defaults  secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
```


> 🌳 
> 
> *`visudo` command* 
> - Running the command will open the `/etc/sudoers` file in the system's default text editor, allowing you to make and save changes to the sudo configuration.
> - It is used to edit the file while ensuring no syntax errors.
> 
> *the `/etc/sudoers` file*
> - The file determines which users or groups are granted sudo privileges and what commands they are allowed to run with elevated privileges.
> - It contains lines like `username  ALL=(ALL:ALL) ALL` "hosts=(users:groups) commands" -> the username is allowed to run any command on any host as any user or group.
> - It also contains a defaults section where you can configure some `sudo` settings.
> 	- `iolog_dir` configures the input/output (I/O) logging directory for `sudo`. It is specifically used for I/O logging (capturing the command input and output).
> 	- `log_file` : we specify the filename for the sudo session logs.
> 	- `requiretty` means that the user must run sudo command from a real terminal session (no through processes or scripts). It forces sudo to ensure we are in a logged-in TTY session. If we set the option, but the conditions are not met, we get an error `TTY` (teletype writer) is a terminal interface (for entering commands and receiving text-based output). One of the main reasons we use requiretty is protection from scripts using sudo. If requiretty is disabled, we allow any sudo user to be remotely and automatically exploited to acquire full access to the system. Still, although requiretty can provide valuable protection, its use is fairly limited and limiting.
>  


### 🟪 Tools installation
You can install what you see necessary. This is optional.

- vim: `sudo apt-get install vim` or `sudo apt install vim` to edit files. Nano is installed by default in Debian.

> 💡 Always run `sudo apt update` before installing new packages to avoid issues. However, you don’t always need to `run apt upgrade` unless you want to update your entire system.
> 
> Same for `apt-get`.



### 🟪 SSH configuration
- Let's install and configure a SSH service.

> 🌳 
> 
> **SSH (Secure Shell)** is a cryptographic network communication protocol that enables two computers to communicate.
> - It is used for secure *communication* over an unsecured network. 
> - It provides a secure way to *access and manage remote systems*, allowing users to 
> 	- *log* into a remote machine, 
> 	- *execute* commands in a remote machine, and
> 	- *transfer* files from or to a remote machine.
> - It utilizes a client-server paradigm, in which clients and servers communicate via a secure channel.
> - Use: Connect to a host as a specific user ``ssh username@hostname``
> - OpenSSH is the most common implementation of SSH and is available on various Unix-like operating systems, including Linux and macOS.
> 


- Install OpenSSH `sudo apt install openssh-server`
- Check SSH server status: `sudo service ssh status` or `sudo systemctl status ssh`, see if it appears active/running. (Execute `sudo service ssh restart` if it's not active).
- Let's change some configurations by editing (use nano or vim) its configuration files
	- Edit server (daemon): `sudo nano /etc/ssh/sshd_config`.
		- The SSH listening port should be `4242`. Uncomment and change port line from `#Port 22` to `Port 4242`.
		- It should not be possible to connect via SSH as root, so we must change the line `#PermitRootLogin...` to `PermitRootLogin no`
	- Edit client: `sudo nano /etc/ssh/ssh_config`. 
 		- The SSH port the connection goes out from should be `4242`: `#Port 22` to `Port 4242`.
- Restart the service `sudo service ssh restart` and check status `sudo service ssh status`. We must see that the server is active and listening through port 4242.

### 🟪 UFW Firewall configuration
> 🌳
> Firewall
> - It is a network security software (or device) that monitors, filters and controls incoming and outgoing network traffic.
> - It works based on predetermined security rules.
> - It stablishes a barrier between a secure internal network and a potentially untrusted external networks.
> - They form the first line of defense against various cyber threats.
>
> *UFW (Uncomplicated Firewall)*
> It's a user-friendly front-end for managing iptables. 
> - iptables is a traditional powerful and flexible firewall program in Linux.
> - UFW simplifies the process of configuring and managing the iptables firewall on Linux.
> - UFW is primarily managed through the command line.
> - UFW follows the principle of "default deny":
>     - Incoming traffic is denied unless explicitly allowed by configured rules.
>     - While outgoing traffic is allowed by default, so you can connect to other servers without any problems.
> - UFW includes application profiles that allow users to enable predefined sets of rules for common services and applications (like OpenSSH, Apache, or Nginx).

Let's install UFW and enable it.
```shell
$ sudo apt install ufw
$ sudo ufw enable
```
Check UFW status with `sudo ufw status`, `sudo ufw status verbose` or `sudo ufw status numbered` (where the rules appear numbered for reference).

Now lets add/change some rules: `sudo ufw allow 4242` in order to allow 4242 for SSH to work.

> 🌳
> Some useful UFW commands
> - Allow/deny ports:
>   ```shell
>   $ sudo ufw allow <port>
>   $ sudo ufw deny <port>
>   ```
>   - Remove port rules:
>   ```shell
>   $ sudo ufw delete allow <port>
>   $ sudo ufw delete deny <port>
>   $ sudo ufw delete <rule index number>
>   ```
>   Careful with the numbered method, the index numbers change after a deletion, check between deletes to get the correct index number!

### 🟪 Test SSH communication with your VM

We have installed SSH in our VM to make it accesible remotely. So let's login to our VM from our machine. The SSH server in the VM needs a port for when someone connects from outside. So the VM need a port, but because this is a virtual machine (guest), it needs to use our machine (host) ports. We make a port available for the VM by redirecting ports using "port forwarding" in our virtualization software (Virtual Box).
Let's forward the host port 4242 to the guest port 4242, so that connections to port 4242 on the host will be directed to port 4242 on the virtual machine.
- In VirtualBox:
	- go to VM >> `Settings` >> `Network` >> `Adapter 1` >> `Advanced` >> `Port Forwarding`.
	- add a rule: TCP protocol, host port 4242 and guest port 4242.
- Restart SSH service after this change `sudo service ssh restart` or `sudo systemctl restart ssh`.
- Check if SSH is active and which port is listening (in the VM(guest))
	- Linux: `sudo systemctl status ssh` or `sudo service ssh status` (ssh status)
	- Linux: `sudo ss -tlnp | grep sshd` or `cat /etc/ssh/sshd_config` (ssh server port)

To connect, use the `ssh` command (option `-p`: Port to connect to on the remote host).

🔸 Let's connect from our machine (host) to the VM (guest). 
- In the host terminal (your computer), connect like this and introduce your created password. (use `localhost` as hostname). Create a directory and see how it appears in the VM!
```shell
$ ssh <username>@vm_hostname -p <vm_port>
```
- To quit the ssh connection, type `exit`.

🔸 If we would want to connect from the VM (guest) to our machine (host): This will be possible only if SSH is enable in the host and let any user connect remotely (default ssh (listening) port is 22). 
- 🔴 This is not part of the subject and it does not work in 42 campuses as we are not permitted to connect remotely to the machines
- You can see remote connection settings in your MacOS: Open System Preferences, go to Sharing, and enable Remote Login. 42 machines with Mac shows you "To log in to this computer remotely, type `ssh yourlogin@c1r1s1.42madrid.com`" with your user, desk number and campus. But as I said early, students don't have access, only administrators.
- You might want to find out the IP address of your host machine.
	- get IP address on MacOS: `ipconfig getifaddr en0`
 		- This command retrieves the IP address of the primary network interface (en0), which is usually the Wi-Fi interface. If you're using a different network interface, replace en0 with the appropriate interface name (e.g., en1 for Ethernet).

🔸🔸 As the VM is running on the host machine, we can use "localhost" as both host and guest (vm) hostnames/IP addresses.
- When you use "localhost" or "127.0.0.1" from within the VM, it refers to the network interface of the VM itself, which is the same physical machine that hosts the VM. Therefore, any services or processes running on the host machine that are configured to listen on localhost or 127.0.0.1 can be accessed from within the VM using these addresses.
```shell
$ ssh <username>@localhost -p <target_port>
$ ssh <username>@127.0.0.1 -p <target_port>
```
--> `ssh user@localhost -p 4242` from our machine

### 🟪 Password policy
Two steps to fulfill the subject:
#### 1. 🔹 Change password and accounts aging settings 🔹

We do this by editing the `/etc/login.defs` file, don't forget to use sudo.
Look for `password aging controls` in the file, let's modify the file so all new users get this configuration. 
- `PASS_MAX_DAYS 30`: passwords expire after 30 days
- `PASS_MIN_DAYS 2`: the minimum number of days allowed between password changes is 2
- `PASS_WARN_AGE 7`: the user is notified with a message 7 days before the password expires

Let's change this for current users too. You can use `chage -l <username>` (`chage`: change age) to see current settings and the following to update them.
```
$ sudo chage -M 30 <username/root>
$ sudo chage -m 2 <username/root>
$ sudo chage -W 7 <username/root>
```
#### 2. 🔹 Change passwords requirements 🔹

Install Password Quality Checking Library and edit its settings.
```
$ sudo apt install libpam-pwquality
$ sudo nano /etc/pam.d/common-password
```
Change the line `password  requisite     pam_pwquality.so` to 
```
password  requisite     pam_pwquality.so  retry=3 minlen=10 ucredit=-1 lcredit=-1 dcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root
```
so the passwords..
- gives you three retries before error (`retry=3`)
- consists of minimum 10 characters (`minlen=10`)
- must contain a capital letter, a lower letter and a number (`ucredit=-1 lcredit=-1 dcredit=-1` for uppercase, lowercase and digits, `-` for minimum and `+` for maximum)
- cannot have the same character consecutively repeated more than 3 times (`maxrepeat=3`)
- cannot contain the user name (`reject_username`)
- must contain at least 7 new characters not included in the old password (`difok=7`). This rule does not apply for the root password.
- The policy applies to root (`enforce_for_root`)
  - "Note that root is not asked for an old password so the checks that compare the old and new password are not performed." [enforce_for_root doc](https://man.archlinux.org/man/pam_pwquality.8#enforce_for_root)

✨ Use `sudo passwd <username>` to change your password and make it comply with the new policy (for root and your user).
