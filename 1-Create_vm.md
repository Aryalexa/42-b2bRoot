### 1. Create new VM
> 🌳
> 
> *What is a VM? What is virtualization?*
> - A Virtual Machine is a software that emulates a physical computer system, allowing you to run other OSs on your machine with its own isolated environment. This is done by using a virtual machine monitor (VMM) or hypervisor that manages the hardware resources of the physical machine and allocates them to the virtual machines.
> - Uses: running multiple OSs, isolating applications, testing environments.
> - How: by using a hypervisor, which is a software layer that manages the execution of multiple VMs on a single host machine. The hypervisor acts as a middleman between the physical hardware and the VMs, allocating resources to each VM as needed. The hypervisor creates a VM environment that replicates the hardware and software of a physical computer and the operating system installed in the VM believes that it is running on a real physical machine.
> 
> *Are containers a form of virtualization too?*
> - VMs are complete virtual computers that run their own operating system and applications. They are isolated from each other and from the host machine. This makes them a good choice for running applications that need to be isolated, such as development environments or testing environments.
> - Containers, on the other hand, are lightweight virtual environments that share the host machine's operating system and kernel. This makes them more efficient than VMs, as they do not need to duplicate the operating system for each container. They are also more portable, as they can be easily moved from one machine to another.
>	- A container packages an application and its dependencies into a single unit. These units can run on any machine that has the necessary container runtime environment.
> 	- They can be created and managed using container orchestration tools like Docker and Kubernetes, which simplifies the process of deploying and managing containerized applications.
>
> *VMs vs Containers*
> - VMs have higher resource isolation
> - Containers are more portable
> - Containers are more scalable
> - Containers have excellent resource utilization
> - Containers are less complex than VMs
>
> VMs -> applications that require high isolation and portability (developing, testing)
> 
> Containers -> applications that need to be scalable and resource-efficient (production, microservices)

> 🌳
> 
> *Choosing a OS...*
> We have to choose a Operating System for our VM. We are given two options: Debian or Rocky.
> - Both are stable and reliable Linux distributions. Know for their stability and security.
> - Debian is a good choice for users who want a stable and secure distribution with the latest features.
> - Rocky Linux is a good choice for users who want a stable and secure distribution with excellent compatibility with RHEL and other enterprise distributions.
> 	- It is based on CentOS, which is a fork of Red Hat Enterprise Linux (RHEL)
> - If you are a beginner, Debian may be a better choice because it is more user-friendly. Rocky Linux is a good choice for experienced Linux users who are looking for a more powerful distribution.
>
> We decide to go with Debian.

What we need
- tool: Oracle VirtualBox
- files: Debian ISO image
  - Download the latest stable Debian ISO image from [debian.org/download](debian.org/download) or [debian.org/download.es](https://www.debian.org/download.es). Better to save it in your personal `sgoinfre` folder.

> 🌳
>  
> *Downloading the Debian ISO Image...*
> - The ISO image contains the Debian OS files.
> - Debian is a Linux distribution based on the Unix OS.

Open VirtualBox and create a **new** VM.

Give the VM a **name**, a **location**, a type and a version.
- name: `Born2beroot`, for example
- location: inside your `sgoinfre` folder`sgoinfre/42login/vmdirname`
- type: linux
- version: Debian

Reserve some **RAM** memory for the VM. 1024 MB is good.

> 🌳
> 
> *Creating a virtual disk...*
> - We need to create a virtual (hard) disk where the guest OS and applications will be installed.
> - Virtual disks come in different formats. VDI is the native format for VirtualBox.

Create a virtual **Hard disk** *now*.
- type: VDI (Virtual Disk Image)
- storage: select `dynamically allocated` so the allocation is done as is used until reaching the limit
- location: give the vdi a location `42login/vmdirname/Born2beroot/Born2beroot.vdi`
- size: `12GB` as recommended or `30GB` if we are doing the ✨bonus✨ (the manual partitioning needs 30GB, but `sgoinfre` allow 15GB max, so.. idk, I've read slack conversations saying to use less as the subject says "similar").
```
🔹General
name: Born2beroot
SO: Debian
🔹System
mem: 1024 MB
boot order: disquette, optic, hard disk
acc: ..
🔹Display ...
🔹Storage
controller: IDE, master secondary IDE: [optic unit] Empty
controller SATA, SATA port 0: Born2beroot.vdi (Normal, 12GB)
🔹Audio ...
```

Almost done, now lets go to `Settings` >> `Storage` and add the image to the VM.
	- `Controller IDE` >> `💿 Empty`
	- select an optic unit from file on disk: the downloaded ISO image

> 🌳
> 
> *Selecting the ISO Image as Optical Unit...*
> - Once the virtual hard disk is created, we can attach the Debian ISO image to the VM as an optical drive. This will allow the virtual machine to boot from the ISO image and install Debian. "optical drive" is used to simulate the presence of a CD/DVD drive, as if you had inserted a real installation disc.

Finally you can **start** the VM and begin to install the OS.
