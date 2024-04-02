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
  
command `sudo /usr/sbin/aa-status` (for debian)  
- says if `apparmor` module is loaded  
  
command `ss -tunlp` (for debian)  
- data: netid, state, recv-q, sen-q, local address:port, peer address:port, users  
- ex: tcp, listen, 0, 128, 0.0.0.0:4242, 0.0.0.0:\*, users:(("sshd", pid=523,fd=3))  
  
command `sudo /usr/sbin/ufw status` or `sudo ufw status` (for debian)  
- UFW firewall status data: active or not  
- more data: to, action, from  
- ex: 4242, allow, anywhere

command `dpkg -s <pkgname>`
- check if package is installed
