# b2bR

The 42 project Born2beroot explores the fundamentals of system administration by inviting us to install and configure a virtual machine with VirtualBox (or UTM).

Target: configure your first server using a VM

## Table of contents
- Subject
  - Requirements
  - Evaluation
- COMMANDS
- The project
    1. Create new VM
    2. Install Debian
    3. Start the VM
    4. Set up tools, services and configurations
       - `sudo`  & users and groups management
       - Tools installation
       - SSH configuration
       - UFW Firewall configuration
       - Test SSH communication with your VM
       - Password policy
    5. The script
    6. Bonus
    7. Save state and signature
- Good to know - prepare for evaluation
- Sources/Credits
  
## **Requirements**  
  
- SO: last stable version of *Debian*  
- Partitions: at least 2 *encrypted partitions* created with LVM  
- Main service: The *SSH* service should run only on port `4242`. For security reasons, it should not be possible to connect via SSH as root.  
- Configure you SO with the *firewall* UFW, leaving only port `4242` open. (The FW should be active when executing the VM)  
- `hostname` of your VM: `{42login}42`.  
- Use a strong *password policy*  
	- passwords expire after 30 days  
	- the minimum number of days allowed before changing the password is 2  
	- the user is notified with a message 7 days before the password expires  
	- passwords..  
		- consists of minimum 10 characters,  
		- must contain a capital letter and a number.  
		- cannot have the same character consecutively repeated more than 3 times.  
		- cannot contain the user name.  
		- must contain at least 7 new characters not included in the old password (this rule does not apply for the root password)  
- Install and configure `sudo` following strict rules  
- *Users*: `root` and a user with your login must exist  
- Your user must be part of the *groups* `user42` and `sudo`.  

In order to configure a strong password for the group `sudo`:  
- Authenticate yourself as `sudo`, 3 tries at most  
- when using `sudo` and password is wrong, a message must be shown  
- for every command executed with `sudo`, the input and the output must be archived in the directory `/var/log/sudo/`.  
- `TTY` mode must be active for security reasons.  
- For security reasons, usable directories by `sudo` should be restricted. Example:  
	- `/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin`  
  
BASH SCRIPT `monitoring.sh`  
When the server starts, the script should show some information every 10 minutes in all terminals (see `wall`). The wall banner is optional. No errors should seen.  
The script may be interrupted during evaluation (see `cron`).  
Information to show:  
- Your OS architecture and its kernel version  
- Number of physical cores  
- Number of virtual cores  
- Current RAM memory available in your server and its usage as a percentage  
- Current memory available in your server and its usage as a percentage (disk)  
- Current percentage of core/cpu load  
- Date and time of last reboot  
- Whether LVM is active or not  
- Number of active connections  
- Number of users in the server  
- IPv4 address and MAC (Media Access Control) of your server  
- Number of commands executed as `sudo`  

BONUS ✨

  
  
## **Evaluation**  
  
During evaluation  
- Change the hostname of the VM  
- Create a new user for SSH  
- Create a new user and assign them to a group  
- Answer questions about the OS selected (aptitude vs apt, SELinux, AppArmor)  
- The script may be interrupted (see `cron`)  
  
(!) - Después de preparar tus *archivos de configuración*, deberás cambiar la contraseña de todas las cuentas presentes en la máquina virtual, root incluida.  
  
> _We have to UNDERSTAND..._  
> - _sobre el sistema operativo que has elegido._  
> - _las diferencias entre aptitude y apt,_  
> - _qué son SELinux y AppArmor._  
> - _¡entiende lo que estás utilizando!_  
> _SSH (a new user is created during defense)_  
  
## **COMMANDS**  
  
command `lsblk`.  
- It lists information about all available or the specified block devices.  
- It reads the sysfs filesystem and udev db to gather information.  
- It prints all block devices (except RAM disks) in a tree-like format by default.  
- Use `lsblk --help` to get a list of all available columns.  

command `hostname` or `cat /etc/hostname`
- get hostname (or use nano to edit)

command `cat /etc/hosts`
- list all hosts: localhost, vm_hostname, ..
- use nano to edit
  
command `head -n 2 /etc/os-release` (for debian)  
- names the debian version (pretty and normal names)  

command `cat /etc/group` 
- see all groups and its users .

command `cut -d: -f1 /etc/passwd`
- see all local users
  
command `/usr/sbin/aa-status` (for debian)  
- says if `apparmor` module is loaded  
  
command `ss -tunlp` (for debian)  
- data: netid, state, recv-q, sen-q, local address:port, peer address:port, users  
- ex: tcp, listen, 0, 128, 0.0.0.0:4242, 0.0.0.0:\*, users:(("sshd", pid=523,fd=3))  
  
command `/usr/sbin/ufw status` (for debian)  
- UFW firewall status data: active or not  
- more data: to, action, from  
- ex: 4242, allow, anywhere

command `dpkg -s <pkgname>`
- check if package is installed

## **The project**
### 1. Create new VM
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳
> 
> *What is a VM? What is virtualization?*
> - A Virtual Machine is a software that emulates a physical computer system, allowing you to run other OSs on your machine with its own isolated environment. This is done by creating a virtual machine monitor (VMM) that manages the hardware resources of the physical machine and allocates them to the virtual machines.
> - Uses: running multiple OSs, isolating applications, as testing environment, 
> 
> *What about containers?*
> - A containers is a lightweight and portable application that packages an applicacitons and its dependencies into a single unit. These units can run on any machine that has the necessary container runtime environment. Container share the underlying OS kernel of the host machine. They are used to deploy and manage microservices applications. They can be created and managed using container orchestration tools like Docker and Kubernetes, which simplifies the process of deploying and managing containerized applications.
>
> *VMs vs Containers*
> - VMs have higher resource isolation
> - Containers are more portable
> - Containers are more scalable
> - Containers have excellent resource utilization
> - Containers are less complex than VMs
>
> VMs -> applications that require high isolation and portability 
> Containers -> applications that need to be scalable and resource-efficient
> 
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳

We need
- tool: Oracle VirtualBox
- files: Debian ISO image
  - Download the latest stable Debian ISO image from debian.org/download. Better to save it in your personal `sgoinfre` folder.

> 🌳 🌳 🌳 🌳
> 
> *Downloading the Debian ISO Image...*
> - The ISO image contains the Debian OS files.
> - Debian is a Linux distribution based on the Unix OS.
> 
> 🌳 🌳 🌳 🌳 
- Open VirtualBox
- Create **new** VM
- Give the VM a **name**, a **location**, a type and a version.
	- name: `Born2beroot`, for example
	- location: inside your `sgoinfre` folder`sgoinfre/42login/vmdirname`
	- type: linux
	- version: debian
- Reserve some **RAM** memory for the VM. 1024 MB is good.
> 🌳 🌳 🌳 🌳
> 
> *Creating a virtual disk...*
> - We need to create a virtual (hard) disk where the guest OS and applications will be installed.
> - Virtual disks come in different formats. VDI is the native format for VirtualBox.
> 
> 🌳 🌳 🌳 🌳 
- Create a virtual **Hard disk** *now*.
	- type: VDI (Virtual Disk Image)
	- storage: select `dynamically allocated` so the allocation is done as is used until reaching the limit
	- location: give the vdi a location `42login/vmdirname/Born2beroot/Born2beroot.vdi`
	- size: `12GB` as recommended or `30GB` if we are doing the ✨bonus✨. 🔴#todo: mcombeau says 10-13 for both mandatory and bonus.
```
🔹General
name: Born2beroot
SO: oracle 64 bit
🔹System
mem: 1024 MB
boot order: disquette, optic, hard disk
acc: ..
🔹Display ...
🔹Storage:
controller: IDE, master secondary IDE: \[optic unit\] Empty
controller SATA, SATA port 0: Born2beroot.vdi (Normal, 12GB)
🔹Audio ..
```
- Almost done, now lets go to **Settings**
- In `Settings` >> `Storage`
	- `Controller IDE` >> `💿 Empty`
	- select an optic unit from file on disk: the downloaded ISO

> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳
> 
> *Selecting the ISO Image as Optical Unit...*
> - Once the virtual hard disk is created, we can attach the Debian ISO image to the VM as an optical drive. This will allow the virtual machine to boot from the ISO image and install Debian. "optical drive" is used to simulate the presence of a CD/DVD drive, as if you had inserted a real installation disc.
> 
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳
- Finally you can **start** the VM and begin to install the OS.

### 2. Install Debian

> - Make the display bigger by clicking the `blue display at the bottom` > `Virtual screen` > `Scale to 200%`
> - Use the `cmd` key so the machine captures your mouse and vice versa.
> - Use arrows keys and `enter` to navigate during the installation (except otherwise specified) 🔴#todo: check when experienced

> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳
> 
> *Debian* is a free and open-source OS. It is a Linux distribution based on the Unix operating system. It is known for its stability, security, and wide range of software packages. Debian is developed by a large community of volunteers and is available in over 100 languages. Many popular Linux distributions, such as Ubuntu, are based on Debian. These distributions inherit Debian's package management system and other features while adding their own unique characteristics.
> 
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳

- Select **Install**, no graphic interface.
- Select *language*, english or the one you want.
- Select *country*, select `Other` if you cannot see yours.
	- If `Other` is selected, Select *continent* and then you will find your country.
	- If the combination of language and country selected is not defined, just choose one country from the shown list to use their settings and your selected country together.
- Configure the keyboard. Select a *keymap* to configure the keyboard.
	- ANSI standard -> American English
#### Configure the network
- Configure the network. Set a **hostname** for the VM: `{42login}42`. And leave the **domain** empty.
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳
> 
> *Configuring the network...*
> - The hostname is a unique name assigned to a particular machine on the network to identify it and distinguish it from other devices. Easier to remember than IP addresses. Useful to communicate with other devices within the network.
> - A FQDN (fully qualified domain name) is a hostname with its domain included (`myname.mydomain.com`). They are used for more formal network addressing, often employed in server environments. We are not going to use a domain in this project.
> 
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳

#### Configure users and passwords
- Configure users and passwords. We'll set up the root and a new user.
> - Save all passwords as they will be needed later.
> - To see the password you have typed before confirming, go to `Show Pawssword in Clear` and press the space bar.
- First set a password for the **root account**, we will need to re-enter it to confirm.
- Then a **user account** will be created for us to use instead of the root account for non-administrative activities. We'll be asked for the user *full name* and a *username*: type your login for both. Lastly, choose a password for the new user.
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳
> 
> *User accounts...*
> - In a Unix-like operating system such as Debian, a "user account" refers to an account created for an individual user. Each person or system process that interacts with the system has its own user account.
> - A user account is associated with a unique username and a user ID (UID). It also has a `/home` directory where the user's files and settings are stored.
> - Users have their own permissions and access rights to files and directories on the system.
> - Normally, the full name would be something like `John Smith` and the username like `jsmith`.
>
> *Root account...*
> - The "root account" or "superuser" or "administrator" is a special user account with the highest privileges, often reserved for system administration tasks.
> - root file system: `/`.
> 
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳
- Configure the clock. Select your *time zone*.
#### Configure the partitions
- Configure the **partition disks**. Two options here.
	- `Guided - use entire disk and set up encrypted LVM` as we want encrypted partitions, or
	- `Manual` if we want to do the ✨bonus✨ (➡️ got to "bonus" and then go to "xxx" to continue).
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳
> 
> *Setting up encrypted LVM...*
> - We are going to configure an encrypted Logical Volume Manager during the partitioning process.
> - LVM is a system that manages logical volumes or partitions on a Linux system, it provides a layer of abstraction between the physical storage devices and the file systems, allowing you to create, resize and move logical volumes dynamically.
> - In this case, you are opting to encrypt the data stored on the logical volumes and you will likely be prompted to provide a passphrase or encryption key. This passphrase will be required to unlock the encrypted volumes each time the system boots.
> - Once the encrypted LVM is set up, you can proceed with creating logical volumes, assigning file systems, and completing the partitioning process.
> 
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳
- After choosing encrypted LVM, select the only disk we have to set up its partitions.
- Now we have to choose a partitioning scheme, we choose `Separate /home partition`.
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳
> 
> *Partitioning schemes...*
> We are offered two strategies for organizing the file system partitions
> - All Files in One Partition - your primary partition will hold the entire operating system, including the root file system (`/`) and  the home directory (`/home`). Convenient for for users who don't need to separate their data from the operating system.
> - Separate /home partition - user data is separated from the system files. Two partitions: the root file system (`/`) and the `/home` directory are placed on separate partitions. The root partition contains the OS's core files, system files, binaries, and configuration files, while the /home partition stores user home directories.
> 
> *File system*
> A file system is a way of organizing and storing files. It provides a structure for organizing data, managing access to files, and maintaining metadata (information about the files, such as permissions and timestamps). The file system dictates how files are named, stored, and organized on the storage medium. 
> *Root file system*
> The foot file system (`/`) is the base directory of the Linux file hierarchy. It contains essential system files, configuration files, binaries, and directories. All other directories stem from the root directory. The root file system is mounted at the beginning of the boot process, and all other file systems are attached as subdirectories or mount points under the root.
> *Home directory*
> The home directory (`/home` or `~` or `/Users/username`) is a user-specific directory where a user's personal files, settings, and configurations are stored. Each user has their own home directory. The home directory provides a dedicated space for the users files and allows them to customize their environment.
> 
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳
- The partitioning scheme has to be written to disk before we can configure the LVM. We confirm and the erasing of the data starts. The erasing can be cancel if you want, as it is done to ensure any previous data on the partition is not recoverable.
- Now the *encryption* begins, and we need to choose a passphrase to encrypt the partition. Remember to save this passphrase.
- The next step is setting an amount of volume group to use for the partitioning. We are also informed of the maximum size available and minimum size to use. We use the max.
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳
> 
> A volume group (VG) is a collection of physical volumes (PVs) that can be used to create logical volumes (LVs).
>
> PVs are the basic units of storage space in LVM and they represent physical disks or partitions on a disk.
> 
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳
- Now we see the overview and select `Finish partitioning and write changes to disk`.
```
LVM VG hostname1-vg, LV home - 4.0 GB Linux device-mapper (linear) 
	#1 4.0 GB f ext4 /home
LVM VG hostname1-vg, LV root - 3.0 GB Linux device-mapper (linear)
	#1 3.0 GB f ext4 /
LVM VG hostname1-vg, LV swap_1 - 1.0 GB Linux device-mapper (linear)
	#1 1.0 GB f swap swap
encrypted volume (sda5_crypt) - 8.1 GB Linux device-mapper (crypt)
	#1 8.1 GB K lvm
SCDI2 (0,0,0) (sda) - 8.6 GB ATA VBOX HARDDISK
	#1 primary 510.7 MB F ext2 /boot
	#5 logical 8.1 GB K crypto (sda5_crypt)
```
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳
> 
> *Understanding the Partition disks overview...*
> - The setup is using LVM to manage logical volumes within the `hostname1-vg` Volume Group.
> - The system has 3 logical volumes (`root`, `home`, `swap_1`): a LV for the root (`/`), one for home (`/home`) directories, and a LV for swap space, which is a temporary storage area for data when physical RAM is full.
> - The first line indicates a LV name `home` within a VG called `hostname1-vg`, the LV size is 4 GB. It uses the ext4 file system. The mount point for the LV is `/home`.
> - These LVs are contained within the encrypted volume named `sda5_crypt`
> - The encrypted volume resides on the physical hard drive (`sda`).  The volume uses the `crypt` device-mapper target for encryption. It is configured with the `K` flag, indicating that it is used as an LVM Physical Volume (PV).
> - Lastly, the last line describes the physical hard drive (`sda`), which has a total capacity of 8.6 GB. 
> 	- It has one primary partition (partition #1) of 510.7 MB, formatted as ext4 and mounted at the `/boot` directory, which contains boot loader files. 
> 	- The remaining 8.1 GB of space is used for the encrypted LV `sda5_crypt`, which is described in the previous entry.
> 
> ```
> hard disk sda
> |- standard partition /boot
> |- encrypted partition sda5_crypt
> 	|- root LV
> 	|- home LV
> 	|- swap LV
> ```
> 
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳

- After the overview we have to confirm the formats for the three LVs: ext4 for home and root and swap for swap_1.
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳
> 
> ext4 (Fourth Extended Filesystem) is a widely used and matured file system for Linux-based OSs. It's the fourth int he ext file system family. It provides a good balance of performance, reliability, and backward compatibility with earlier ext file system versions.
> 
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳

#### Configure the package manager
- Configure the **package manager (apt)**. Now we are asked if we want to add more media, apart from the Debian we have downloaded and selected, to be used by the package manager. We say no.
- Now the package manager needs to find a mirror* The mirror is use to keep the Debian system updated, secure, and to install additional software. We select our *country*, and then the archive *mirror* `deb.debian.org`.
- As the system is going to connect to the mirror, the installer ask us if we need an *HTTP proxy*, let's just leave this blank and continue.
- Do we want to participate in Debian *stats*? No, this is just a one time project.
- Now we are asked which *additional software* we want the package manager to install. We need none, so deselect everything and continue.
#### Boot loader
- Right after, the installer recommend us to install *GRUB boot loader* as we are installing an OS, and to install it in the primary partition (`/boot`). We say yes and proceed to install it in the bootable device we were recommended: the non-manual option (`/dev/sda (ata_VBOX_HARDDISK)`).
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳
> 
> The GRUB (Grand Unified Bootloader) is a boot loader from the GNU project. It provides a menu from which you can select the operating system to boot if you have multiple operating systems installed on your computer.
> 
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳

- The Debian installation is complete!

### 3. Start the VM
- It's time to boot the recent installed Debian (or not, you will have to do this every time you start the VM). 
- The GRUB boot loader menu appears and we select `Debian GNU/Linux` to boot.
- As we have an encrypted partition, we have to introduced the passphrase to unlock the volumes.
- We see the Debian version and the hostname.
- Introduce your user and password to login (safer than using root).
- Type some commands to verify the Debian installation and the partitions:
```bash
$ lsblk
$ cat /etc/os-release
```

### 4. Set up tools, services and configurations
What we want:
- A SSH service
- A firewall
- Managing users and groups
- Change some sudo configurations
- A strict password policy
- A script

So we need to do some system changes to add and configure them, we need some elevated privileges. Safer than using the root account, the `sudo` command is used for these tasks. 

#### `sudo`  & users and groups management

> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳
> 
> `sudo` (Superuser Do) is a command-line utility. It needs to be installed and configured, and it allows authorized users to execute commands with elevated privileges.
> - It provides fine-grained access control, allowing administrators to define which users or groups can run specific commands with elevated privileges.
> - It maintains a log (audit trail) of each command executed with elevated privileges, (who, when and what).
> - It adheres to the *principle of least privilege*, a security best practice.
> 
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳

- Let's install `sudo` on Debian. For this we'll use the package manager again (`apt` in our case). 
	- Installing tools require the use of the root account, so we *substitute user* to root with the command `su`/`su -`/`su root` and introduce the root password.
	- Update (fetch) and upgrade (pull) installed packages and start installation.
	```
	$ su -
	# apt update
	# apt upgrade
	# apt install sudo
	```

> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳
> 
> `su` (substitute user) command
> - root is used if no username is provided
> - After authentication, your shell will be switched to that user's environment.
> - The hyphen (`-`) option sets up the environment as if you had logged in directly as the specified user. Preserving the environment variables of the target user.
> - To return to the original user's session, simply type `exit` or press Ctrl+D.
> - When switching from normal user to root, the prompt changes from `$` to `#`.
>
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳

> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳
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
>  🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳

- Now we can add our user to `sudo`, exit root session, create the `user42` group and include our user there too.
```
# adduser my_login sudo
# exit
$ sudo addgroup user42 
$ sudo adduser my_login user42

$ getent group sudo
$ getent group user42
```

- And lastly, let's change some `sudo` configurations. We are going to use `visudo` for that.
	- Access to edit sintax-wise safely: `sudo visudo`
	- Find the default lines and change/add these settings:
		- Three tries at most to authenticate as `sudo`
		- Show message if authentication password is wrong
		- Save input and output for every command executed as `sudo` in the directory `/var/log/sudo/`. ( ☢️ If `var/log/sudo` directory does not exist, `mkdir var/log/sudo`)
		- `TTY` mode must be active for security reasons.  
		- The usage of `sudo` must be restricted for some directories:
			- `/usr/local/sbin:/usr/local/bin`
			- `/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin`
	```	
	Defaults  passwd_tries=3
	Defaults  badpass_message="Wrong password. Try again!"
	Defaults  logfile="/var/log/sudo/sudo.log"
	Defaults  log_input, log_output
	Defaults  iolog_dir="/var/log/sudo"
	Defaults  requirett
	Defaults  secure_path="...:..."
	```


> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳🔴
> 
> *`visudo` command and the `sudoers` file*
> - Running the command will open the `/etc/sudoers` file in the system's default text editor, allowing you to make and save changes to the sudo configuration.
> - The command is used to edit the file while ensuring no syntax errors. 
> - The file determines which users or groups are granted sudo privileges and what commands they are allowed to run with elevated privileges.
> - It contains lines like `username  ALL=(ALL:ALL) ALL` "hosts=(users:groups) commands" -> the username is allowed to run any command on any host as any user or group.
> - It also contains a defaults section where you can configure some `sudo` settings.
> 	- `iolog_dir` configures the input/output (I/O) logging directory for `sudo`. It is specifically used for I/O logging (capturing command input and output). 🔴
> 	- `log_file` :location and filename for the sudo session logs. It is used for general session logging.🔴
> 	- `requiretty` means that the user must run sudo command from a real terminal session (no processes or scripts). `TTY` (teletype writer/terminal) is a terminal interface that allows users to interact with the system by entering commands and receiving text-based output.
>  
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳

#### Tools installation
You can install what you see necessary. These are all optional.
- (if using `apt-get` update and upgrade just in case: `apt-get update -y`, `apt-get upgrade -y`)
- git: `apt-get install git -y` , `git --version`
- wget: `sudo apt-get install wget` (a free and open source tool for downloading files from web repositories.)
- vim: `sudo apt-get install vim` or `sudo apt install vim` to edit files.
- oh my zsh

#### SSH configuration
- Lets install and configure a SSH service.

> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳
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
> 🌳 🌳 🌳 🌳 --------------------------------------------------------------------------------- 🌳 🌳 🌳 🌳

- Install OpenSSH `sudo apt install openssh-server`
- Check SSH server status: `sudo service ssh status` or `sudo systemctl status ssh`, see if it appears active/running. (Execute `sudo service ssh restart` if it's not active).
- Let's change some configurations by editing (use nano or vim) its configuration file: `sudo nano /etc/ssh/sshd_config`.
	- The SSH listening port should be `4242`. Uncomment and change port line from`#Port 22` to `Port 4242`.
	- It should not be possible to connect via SSH as root, so we must change the line `#PermitRootLogin...` to `PermitRootLogin no`
- Restart the service `sudo service ssh restart` and check status `sudo service ssh status`. We must see that the server is active and listening through port 4242.

#### 📝UFW Firewall configuration
> 🌳 🌳 🌳 🌳 
> UFW (Uncomplicated Firewall)
> 🌳 🌳 🌳 🌳 

...


#### Test SSH communication with your VM

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

#### 📝Password policy
...
#### 📝The script
...
### 📝5. Bonus
### 6. Save state and signature

Once the VM is configured as you like, you have to submit a file `signature.txt` with the VM signature as its only content. Good moment to save the VM current state. Don't forget to do a backup, just in case.

You can find your virtual disk signature on the installation default path for your VMs.
- Windows: `%HOMEDRIVE%%HOMEPATH%\VirtualBox VMs\`
- Linux: `~/VirtualBox VMs/`
- MacM1: `~/Library/Containers/com.utmapp.UTM/Data/Documents/`
- MacOS: `~/VirtualBox VMs/`
There you will see the `.vdi` file (or `.qcow2` for UTM users - MacM1)
Get its signature on `sha1` format:
- Windows: `certUtil -hashfile VDI_FILE.vdi sha1`
- Linux: `sha1sum VDI_FILE.vdi`
- MacM1: `shasum UTM_FILE.utm/Images/disk-0.qcow2`
- MacOS: `shasum VDI_FILE.vdi`

> Every change on your VM alters its signature. You must always have a VM that matches the signature of your final (and submitted) VM until all evaluations are finished.
> Be careful, as evaluations may alter its signature, please duplicate your VMs or use "save state".

## Good to know - prepare for evaluation

> 🌳 🌳 🌳 🌳
> 
> *Package Management tools*
> - `apt` is a command-line package management tool that simplifies the process of installing, updating, and removing packages. It is a high-level interface to the underlying Advanced Package Tool (APT) libraries.
> - `apt-get` has a more traditional and verbose command-line interface. It was the original package management tool on Debian-based systems and is known for its extensive set of options. It and `apt` share the same underlying package management libraries. It is still widely used and supported, especially in scripts and automation. Use the `-y` option for automatic "yes".
> - `aptitude` is also a command-line package management tool, but it provides a more interactive and text-based interface compared to `apt`. It is designed to be more user-friendly and includes additional features beyond package management.
>
> 🌳 🌳 🌳 🌳

Change the VM hostname: change the name in both files and reboot the machine
```
$ sudo nano /etc/hostname
$ sudo nano /etc/hosts
$ sudo reboot
```



- 



## **Bibliography**
- https://github.com/gemartin99/Born2beroot-Tutorial
- https://github.com/pasqualerossi/Born2BeRoot-Guide
- https://github.com/mcombeau/Born2beroot
- https://baigal.medium.com/born2beroot-e6e26dfb50ac
- 
