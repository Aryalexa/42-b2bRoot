### 7. Signature

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
> Be careful, as evaluations may alter its signature, please duplicate your VMs or use "save state". ☢️

