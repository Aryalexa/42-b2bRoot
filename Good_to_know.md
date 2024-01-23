
## Good to know - prepare for evaluation

> 🌳

### 🟧 *Package Management tools*
- `apt` is a command-line package management tool that simplifies the process of installing, updating, and removing packages. It is a high-level interface to the underlying Advanced Package Tool (APT) libraries.
- `apt-get` has a more traditional and verbose command-line interface. It was the original package management tool on Debian-based systems and is known for its extensive set of options. It and `apt` share the same underlying package management libraries. It is still widely used and supported, especially in scripts and automation. Use the `-y` option for automatic `yes`.
- `aptitude` is also a command-line package management tool, but it provides a more interactive and text-based interface compared to `apt`. It is designed to be more user-friendly and includes additional features beyond package management.



### 🟧 Change the VM hostname
- change the name in both files and reboot the machine
```
$ sudo nano /etc/hostname
$ sudo nano /etc/hosts
$ sudo reboot
```

### 🟧 What is AppArmor?
- AppArmor (Application Armor) is a Linux security module that provides Mandatory Access Control (MAC) by confining programs to a limited set of resources and actions.
- why: to restrict the capabilities of individual programs to minimize the potential damage they can cause if compromised.
- MAC works based on policies set by the system administrator or security policies defined by the organization (the organization's security team or IT administrators). Unlike discretionary access control (DAC), where users have control over their own objects, MAC is enforced by the operating system and restricts users' ability to change access controls.
- AppArmor is commonly used on Ubuntu and other Debian-based distributions, while SELinux is often found on Red Hat and CentOS systems.

### 🟧 Check installed packages

Several ways, sudo package example:
```
# which sudo

# dpkg -l | grep sudo
# dpkg -s sudo

# apt list --installed | grep sudo
```
### 🟧 Cron

Cron stop and start
```
$ sudo /etc/init.d/cron stop
$ sudo /etc/init.d/cron start
```
- Keep in mind that stopping the cron service will prevent new jobs from starting, but it won't necessarily terminate jobs that are currently running.

For a specific job, delete the line int he cron table to stop the job from running.
- `sudo crontab -e` to edit the root's cron jobs.

### 🟧 SSH config files

SSH config files: `/etc/ssh/ssh_config` vs `/etc/ssh/sshd_config`

- `ssh_config` is for configuring the behavior of the OpenSSH client.
- `sshd_config` is for configuring the behavior of the OpenSSH server (`sshd`).

OpenSSH client and server
- OpenSSH client: it is the program you use to connect from your machine to other machines using SSH.
- OpenSSH daemon (`sshd`): it is the server-side component of OpenSSH, that handles incoming SSH connections.

## Good to know - but maybe too much


### 🟨 Network Addresses
1. IP Address:
An IP (Internet Protocol) address is a numerical label assigned to each device participating in a computer network. It serves two main functions:
Identifying the host or network interface.
Providing the location of the host in the network.
2. Subnet Mask:
The subnet mask helps in dividing an IP address into network and host portions. It is often expressed in dotted-decimal format like an IP address (e.g., 255.255.255.0). The subnet mask designates the network and host portions of an IP address.
3. Network Address:
The network address represents the beginning of a specific network. It is used to identify the network itself. For example, in the network 192.168.1.0 with a subnet mask of 255.255.255.0, the network address is 192.168.1.0.
4. Broadcast Address:
The broadcast address is a special address that is used to send a message to all devices on a specific network. It is the last address in the range for a given network. For example, in the network 192.168.1.0 with a subnet mask of 255.255.255.0, the broadcast address is 192.168.1.255.
5. Local Address (Loopback Address):
The local address, often referred to as the loopback address, is used to test network connectivity on the local machine. It is represented by the IP address 127.0.0.1.
6. Virtual Machine (VM) Address:
A virtual machine running on your computer, often in a virtualized environment like VirtualBox, VMware, or others, will typically have its own IP address assigned. This IP address is usually within the range of the host machine's local network.

Example:
If your host machine has an IP address of 192.168.1.10 with a subnet mask of 255.255.255.0, your VM might be assigned an IP address like 192.168.1.11. The network address for this example is 192.168.1.0, and the broadcast address is 192.168.1.255.

Your ip iddress: 
- MacOS: `ipconfig getifaddr en0`
- Linux: `ip addr`

Scan network
- Linux: `sudo arp-scan --localnet`
- MAcOS: `arp -a`, this command displays the ARP table, which includes a mapping of IP addresses to MAC addresses. It can give you a list of devices that your Mac has recently communicated with.

### 🟨 Init systems
`sudo service ssh status` vs `sudo systemctl status ssh`

The commands achieve similar outcomes, but they are associated with different init systems commonly used in Linux.
A init system is a **system and service manager for Linux**, which is designed to manage the system and its processes, including services, daemons, and other system-related tasks.

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

### 🟨 The ISO model
The ISO model is a conceptual framwork that stardardizes the functions of a system into 7 abstraction layers.
These layers  help facilitate communication between different systems by breaking down the communication process into manageable and modular components.Each layer has a specific set of functions and interacts with adjacent layers. 
The OSI model is not a practical implementation but a theoretical framework for understanding network protocols and communication.

These are the layers, from lowest (physical) to highest (application):
1. Physical Layer (L1)
2. Data Link Layer (L2)
3. Network Layer (L3)
4. Transport Layer (L4)
5. Session Layer (L5)
6. Presentation Layer (L6)
7. Applicacion Layer (L7)

**SSH communication example**

SSH operates primarily at the application layer (Layer 7) but interacts with lower layers for encryption, transport, and network functionality. 
- An SSH session involves interaction primarily with the application, presentation, and session layers for secure communication. (L7-5)
- The transport layer ensures reliable communication, while the network layer handles routing between different IP addresses. (L4-3)
- The lower layers (data link and physical) are managed by the underlying network infrastructure and are less directly influenced by the SSH protocol. (L2-1)

A little more in detail
- When a user initiates an SSH connection, they typically use an SSH client application (e.g., OpenSSH on the command line). The client initiates a request to establish a secure connection to an SSH server. SSH operates as an application layer (L7) protocol.
- SSH includes its own presentation layer (L6) for encoding and decoding data. It manages the encryption, compression, and integrity checking of the data being exchanged between the client and server. This layer ensures that the data is presented in a secure and readable format.
- The session layer (L5) manages the establishment, maintenance, and termination of the SSH session. It handles the negotiation of encryption algorithms, authentication methods, and other parameters that define the characteristics of the session.
- The transport layer (L4) is crucial for SSH. It is responsible for secure and reliable end-to-end communication. SSH can operate over both TCP (Transmission Control Protocol) and, to a lesser extent, UDP (User Datagram Protocol). Most SSH implementations use TCP as the underlying transport protocol.
- The network layer (L3) is where IP addressing and routing come into play. In the context of SSH, it involves the routing of data packets between the client and server across different networks. SSH relies on the Internet Protocol (IP) for addressing and routing.
- The data link layer (L2) handles framing and addressing within the local network segment (or LAN). In the case of SSH over the internet, this layer is less directly involved, as the data link layer functionalities are typically handled by routers and switches.
- The physical layer (L1) deals with the actual transmission of bits over the physical medium, such as cables or wireless signals. SSH is agnostic to the physical layer, as it relies on the underlying network infrastructure for physical connectivity.

And a firewall in this communication?

A firewall operates primarily at the network layer and above in the OSI model (L3-7).
- L3: They can examine the IP addresses and route information of packets, to filter and control which devices are allowed to communicate which each other.
- L4: They can inspect traffic and make decisions based on protocols and port numbers.
- L7: They can include application-layer filtering capabilities. They can analyze and control traffic based on specific applications or protocols, providing a more granular level of control.

In the context of an SSH session... a firewall inspects traffic at multiple layers:
- L3: Filtering based on IP addresses.
- L4: Filtering based on port numbers.
- L7: Deep packet inspection, looking into the content of the packets to identify specific applications or protocols. This allows for more sophisticated filtering based on the actual content of the communication.
