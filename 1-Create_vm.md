### 1. Create new VM
> 🌳
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
> 
> Containers -> applications that need to be scalable and resource-efficient

We need
- tool: Oracle VirtualBox
- files: Debian ISO image
  - Download the latest stable Debian ISO image from debian.org/download. Better to save it in your personal `sgoinfre` folder.

> 🌳
>  
> *Downloading the Debian ISO Image...*
> - The ISO image contains the Debian OS files.
> - Debian is a Linux distribution based on the Unix OS.

Open VirtualBox and create **new** VM.

Give the VM a **name**, a **location**, a type and a version.
- name: `Born2beroot`, for example
- location: inside your `sgoinfre` folder`sgoinfre/42login/vmdirname`
- type: linux
- version: debian

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
- size: `12GB` as recommended or `30GB` if we are doing the ✨bonus✨ (the manual partitions need 30GB).
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
