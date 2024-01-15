### 4. Set up

We're going to set up some tools, sevices and configurations.

What we want:
- A SSH service
- A firewall
- Managing users and groups
- Change some sudo configurations
- A strict password policy
- A script (we'll leave this for step-5)

So we need to do some system changes to add and configure all this, we need some elevated privileges. Safer than using the root account, the `sudo` command is used for these tasks. 

#### 🟪 `sudo` installation

> 🌳 
> 
> `sudo` (Superuser Do) is a command-line utility. It needs to be installed and configured, and it allows authorized users to execute commands with elevated privileges.
> - It provides fine-grained access control, allowing administrators to define which users or groups can run specific commands with elevated privileges.
> - It maintains a log (audit trail) of each command executed with elevated privileges, (who, when and what).
> - It adheres to the *principle of least privilege*, a security best practice.
> 


Let's install `sudo` on Debian. For this we'll use the package manager again (`apt` in our case). 
- Installing tools requires high privileges so this time we are going to use the root account: we *substitute user* to root with the command `su`/`su -`/`su root` and introduce the root password.
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
> - The hyphen (`-`) option sets up the environment as if you had logged in directly as the specified user. Preserving the environment variables of the target user.
> - To return to the original user's session, simply type `exit` or press Ctrl+D.
> - When switching from normal user to root, the prompt changes from `$` to `#`.
>


#### 🟪 users and groups management

> 🌳 
> 
> *Commands for users and groups management*
> - add existing users to the `sudo` *group*. Two ways. 
> ```
> # usermod -aG sudo username
> # adduser username groupname(sudo)
> ```
> - create new users and groups, and include users into groups. GID = group ID.
> ```
> $ sudo adduser username
> $ sudo addgroup groupname
> 
> $ sudo adduser username groupname
> ```
> - To check if group exists and its users
>  ```
>  $ getent group groupname
>  ```
>  - check if your current user has `sudo` privileges. If you are a `sudo` user, the answer must be `root`
>  ```
>  sudo whoami
>  ```
>  


Now we can add our user to `sudo`, exit root session, create the `user42` group and include our user there too.

```
# adduser my_login sudo
# exit
$ sudo addgroup user42 
$ sudo adduser my_login user42

$ getent group sudo
$ getent group user42
```

#### 🟪 `sudo` configuration

Let's change some `sudo` configurations. We are going to use `visudo` for that.
- Access to edit sintax-wise safely: `sudo visudo`
- Find the default lines and change/add these settings:
  - Three tries at most to authenticate as `sudo`
  - Show message if authentication password is wrong
  - Save input and output for every command executed as `sudo` in the directory `/var/log/sudo/`. ( ☢️ If `var/log/sudo` directory does not exist, `mkdir var/log/sudo`)
  - `TTY` mode must be active for security reasons.  
  - The usage of `sudo` must be restricted for some directories:
    - `/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin`
```	
Defaults  passwd_tries=3
Defaults  badpass_message="Wrong password. Try again!"
Defaults  logfile="/var/log/sudo/sudo.log"
Defaults  log_input, log_output
Defaults  iolog_dir="/var/log/sudo"
Defaults  requirett
Defaults  secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
```


> 🌳 🔴
> 
> *`visudo` command* 
> - Running the command will open the `/etc/sudoers` file in the system's default text editor, allowing you to make and save changes to the sudo configuration.
> - Is is used to edit the file while ensuring no syntax errors.
> 
> *the `/etc/sudoers` file*
> - The file determines which users or groups are granted sudo privileges and what commands they are allowed to run with elevated privileges.
> - It contains lines like `username  ALL=(ALL:ALL) ALL` "hosts=(users:groups) commands" -> the username is allowed to run any command on any host as any user or group.
> - It also contains a defaults section where you can configure some `sudo` settings.
> 	- `iolog_dir` configures the input/output (I/O) logging directory for `sudo`. It is specifically used for I/O logging (capturing command input and output). 🔴
> 	- `log_file` :location and filename for the sudo session logs. It is used for general session logging.🔴
> 	- `requiretty` means that the user must run sudo command from a real terminal session (no processes or scripts). `TTY` (teletype writer/terminal) is a terminal interface that allows users to interact with the system by entering commands and receiving text-based output.
>  


#### 🟪 Tools installation
You can install what you see necessary. These are all optional.

>if using `apt-get` update and upgrade just in case: `apt-get update -y`, `apt-get upgrade -y`

- git: `apt-get install git -y` , `git --version`
- wget: `sudo apt-get install wget` (a free and open source tool for downloading files from web repositories.)
- vim: `sudo apt-get install vim` or `sudo apt install vim` to edit files.
- oh my zsh

#### 🟪 SSH configuration
- Lets install and configure a SSH service.

> 🌳 
> 
> **SSH (Secure Shell)** is a cryptographic network protocol.
> - It is used for secure *communication* over an unsecured network. 
> - It provides a secure way to *access and manage remote systems*, allowing users to 
> 	- *log* into a remote machine, 
> 	- *execute* commands in a remote machine, and
> 	- *transfer* files from or to a remote machine.
> - It utilizes a client-server paradigm, in which clients and servers communicate via a secure channel.
> - Use: Connect to hostname as username ``ssh username@hostname``
> - OpenSSH is the most common implementation of SSH and is available on various Unix-like operating systems, including Linux and macOS.
> 


- Install OpenSSH `sudo apt install openssh-server`
- Check SSH server status: `sudo service ssh status` or `sudo systemctl status ssh`, see if it appears active/running. (Execute `sudo service ssh restart` if it's not active).
- Let's change some configurations by editing (use nano or vim) its configuration file: `sudo nano /etc/ssh/sshd_config`.
	- The SSH listening port should be `4242`. Uncomment and change port line from`#Port 22` to `Port 4242`.
	- It should not be possible to connect via SSH as root, so we must change the line `#PermitRootLogin...` to `PermitRootLogin no`
- Restart the service `sudo service ssh restart` and check status `sudo service ssh status`. We must see that the server is active and listening through port 4242.

#### 🟪 UFW Firewall configuration
> 🌳
> UFW (Uncomplicated Firewall)


...


#### 🟪 Test SSH communication with your VM

Prepare the VM. We need to forward the host port 4242 to the guest port 4242. 
- In VirtualBox:
	- go to VM >> `Settings` >> `Network` >> `Adapter 1` >> `Advanced` >> `Port Forwarding`.
	- add a rule: TCP protocol, host port 4242 and guest port 4242.
- Restart SSH service after this change `sudo service ssh restart` or `sudo systemctl restart ssh`.
- check if ssh active and the port (for both the host and the VM(guest))
	- `sudo systemctl status ssh` or `sudo service ssh status`
	- `sudo ss -tlnp | grep sshd` or `cat /etc/ssh/sshd_config`

See `man ssh` for more details on ssh command. Option `-p`: Port to connect to on the remote host.🔴#todo

Let's connect from host to VM (guest).
- In the host terminal (physical), connect like this:
```shell
$ ssh <username>@vm_hostname -p <vm_port>
```

- To quit the ssh connection, type `exit`.

Let's connect from VM (guest) to host.
- In a terminal within the VM, connect like this:
```shell
$ ssh <username>@host_ip_address -p <host_port>
```
- You might need to find out the IP address of your host machine using a command like `ifconfig`/`ip a` (on Linux).
- To close the ssh connection, type `exit`.

As we are using the same machine as both the host and the guest (e.g., running the VM locally on your computer), we can use `localhost` as the host IP address and as the vm hostname.
```shell
$ ssh <username>@localhost -p <target_port>
$ ssh <username>@127.0.0.1 -p <target_port>
```

#### 🟪 Password policy
...
