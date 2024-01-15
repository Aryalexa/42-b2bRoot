### 3. Start the VM
It's time to boot the recent installed Debian - or not: you will have to do this every time you start the VM.

- The GRUB boot loader menu appears and we select `Debian GNU/Linux` to boot.
- As we have an encrypted partition, we have to introduced the passphrase to unlock the volumes.
- We see the Debian version and the hostname.
- Introduce your user and password to login (safer than using root)

We can type some commands to verify the Debian installation and the partitions:
```bash
$ lsblk
$ cat /etc/os-release
```
