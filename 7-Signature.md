### 7. Signature

Once the VM is configured as you like, you have to submit a file `signature.txt` with the VM signature as its only content. Good moment to save the VM current state and turn it off. Don't forget to do a backup, just in case.

You can find your virtual disk signature on the installation default path for your VMs.
- Windows: `%HOMEDRIVE%%HOMEPATH%\VirtualBox VMs\`
- Linux: `~/VirtualBox VMs/`
- MacM1: `~/Library/Containers/com.utmapp.UTM/Data/Documents/`
- MacOS: `~/VirtualBox VMs/`

There you will see the `.vdi` file (or `.qcow2` for UTM users - MacM1).

Get its signature on SHA-1 format:
- Windows: `certUtil -hashfile VDI_FILE.vdi sha1`
- Linux: `sha1sum VDI_FILE.vdi`
- MacM1: `shasum UTM_FILE.utm/Images/disk-0.qcow2`
- MacOS: `shasum VDI_FILE.vdi`

> ☢️
> 
> Every change on your VM alters its signature. You must always have a VM that matches the signature of your final (and submitted) VM until all evaluations are finished.
> Be careful, as evaluations may alter its signature, please duplicate your VM or use "save state". 

> 🌳
> 
> Checksums
> - A checksum is a value derived from the digital data of a file or a set of data, generated through a specific algorithm.
> - The purpose of a checksum is to verify the integrity of the data, ensuring that it has not been corrupted or tampered with during transmission or storage.
> - Checksums work by producing a fixed-size output based on the content of the data. Even a small change in the data should result in a significantly different checksum.
> 
> SHA-1 format
> - SHA-1 (Secure Hash Algorithm 1) is a cryptographic hash function that produces a fixed-size (160-bit) hash value, typically represented as a hexadecimal number (40 hex-digits).
> - It is designed to be a one-way function, meaning that it should be computationally infeasible to reverse the process and obtain the original data from its hash value.
> - SHA-1 is widely used for **integrity verification and ensuring data integrity**.
> - Its considered deprecated for cryptographic security due to vulnerabilities that allow for collision attacks. Recommended: SHA-256 or SHA-3.
> - The hash value produced would be the checksum.
> - Commands like `shasum` are used to compute and check SHA (Secure Hash Algorithm) checksums.
