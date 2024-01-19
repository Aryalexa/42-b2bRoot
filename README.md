# b2bR

The 42 project Born2beroot explores the fundamentals of system administration by inviting us to install and configure a virtual machine with VirtualBox (or UTM).

Target: configure your first server using a VM

## Table of contents
- [Subject](https://github.com/Aryalexa/42-b2bRoot/blob/main/Subject.md#b2br-subject)
  - [Requirements](https://github.com/Aryalexa/42-b2bRoot/blob/main/Subject.md#requirements)
  - [Evaluation](https://github.com/Aryalexa/42-b2bRoot/blob/main/Subject.md#evaluation)
- [COMMANDS](https://github.com/Aryalexa/42-b2bRoot/blob/main/Commands.md#commands)
- The implementation
    1. [Create new VM](https://github.com/Aryalexa/42-b2bRoot/blob/main/1-Create_vm.md#1-create-new-vm)
    2. [Install Debian](https://github.com/Aryalexa/42-b2bRoot/blob/main/2-Install_Debian.md#2-install-debian)🟡
    3. [Start the VM](https://github.com/Aryalexa/42-b2bRoot/blob/main/3-Start_vm.md#3-start-the-vm)
    4. Set up
       - [`sudo` installation](https://github.com/Aryalexa/42-b2bRoot/blob/main/4-Vm_setup.md#-sudo-installation)
       - [users and groups management](https://github.com/Aryalexa/42-b2bRoot/blob/main/4-Vm_setup.md#-users-and-groups-management)
       - [`sudo` configuration](https://github.com/Aryalexa/42-b2bRoot/blob/main/4-Vm_setup.md#-sudo-configuration)
       - [Tools installation](https://github.com/Aryalexa/42-b2bRoot/blob/main/4-Vm_setup.md#-tools-installation)
       - [SSH configuration](https://github.com/Aryalexa/42-b2bRoot/blob/main/4-Vm_setup.md#-ssh-configuration)
       - [UFW Firewall configuration](https://github.com/Aryalexa/42-b2bRoot/blob/main/4-Vm_setup.md#-ufw-firewall-configuration)
       - [Test SSH communication with your VM](https://github.com/Aryalexa/42-b2bRoot/blob/main/4-Vm_setup.md#-test-ssh-communication-with-your-vm)
       - [Password policy](https://github.com/Aryalexa/42-b2bRoot/blob/main/4-Vm_setup.md#-password-policy)
    5. [The script](https://github.com/Aryalexa/42-b2bRoot/blob/main/5-Script.md#5-script)🟡
    6. Bonus🔴
    7. [Signature](https://github.com/Aryalexa/42-b2bRoot/blob/main/7-Signature.md#7-signature)
- [Good to know - prepare for evaluation](https://github.com/Aryalexa/42-b2bRoot/blob/main/Good_to_know.md#good-to-know---prepare-for-evaluation)🟡
- [Good to know - but maybe too much](https://github.com/Aryalexa/42-b2bRoot/blob/main/Good_to_know.md#good-to-know---but-maybe-too-much)



## **Sources/Credits**
- https://github.com/gemartin99/Born2beroot-Tutorial
- https://github.com/pasqualerossi/Born2BeRoot-Guide
- https://github.com/mcombeau/Born2beroot 
