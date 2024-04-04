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

see envronment variable DISPLAY: `echo $DISPLAY`
- If you are not using a graphical environment (i.e. you are logging in on the system console with no windows etc; or you are logging in remotely from a text-only terminal over SSH or similar, such as from a Windows computer running PuTTY) then no GUI is involved, and DISPLAY will typically be unset. Your only means of communicating with the computer is the command line (though there may be ways to pivot into a GUI session if you know how).
- If you are logging in on the console with a graphical interface (on Ubuntu, typically the GDM greeter is used) or using a graphical terminal (such as from a Windows computer running eXceed or mobaX, or remote desktop software like a VNC client) the DISPLAY variable is set up by the program which manages your graphical session to indicate to graphical clients which I/O devices to connect to.
