
## Good to know - prepare for evaluation

> 🌳
> 
> 🟧 *Package Management tools*
> - `apt` is a command-line package management tool that simplifies the process of installing, updating, and removing packages. It is a high-level interface to the underlying Advanced Package Tool (APT) libraries.
> - `apt-get` has a more traditional and verbose command-line interface. It was the original package management tool on Debian-based systems and is known for its extensive set of options. It and `apt` share the same underlying package management libraries. It is still widely used and supported, especially in scripts and automation. Use the `-y` option for automatic `yes`.
> - `aptitude` is also a command-line package management tool, but it provides a more interactive and text-based interface compared to `apt`. It is designed to be more user-friendly and includes additional features beyond package management.
>


🟧 Change the VM hostname
- change the name in both files and reboot the machine
```
$ sudo nano /etc/hostname
$ sudo nano /etc/hosts
$ sudo reboot
```



- 


## Good to know - but maybe too much

### 🟨 Init systems
`sudo service ssh status` vs `sudo systemctl status ssh`

The commands achieve similar outcomes, but they are associated with different init systems commonly used in Linux.
A init system is a system and service manager for Linux, which is designed to manage the system and its processes, including services, daemons, and other system-related tasks.

`sudo service ssh status`:
- This command is typically used in systems that use the traditional SysV init system (init.d scripts) for managing services.
It checks the status of the SSH service using the `service` command and the service name `ssh.`

`sudo systemctl status ssh`:
- This command is used in systems that use the systemd init system.
It checks the status of the SSH service using the `systemctl` command and the service name `ssh.`
In modern Linux distributions, there is a trend toward adopting systemd, and as a result, `sudo systemctl status ssh` is more commonly used on systems that use systemd. However, some older systems or distributions still use the traditional SysV init system, where `sudo service ssh status` may be applicable.

It's worth noting that both commands essentially provide information about the status of the SSH service, but they are associated with different init systems. If you are unsure which one to use, you can check the init system your system is using by running the following command:

```bash
ps -p 1 -o comm=
```
If the output is `systemd,` you should use `sudo systemctl status ssh`; otherwise, you might use `sudo service ssh status.`
