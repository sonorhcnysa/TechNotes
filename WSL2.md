## Mount New/External Drive for use in WSL
Assuming a .vhdx file, inserted USB storage, optical drive, network share or similar is already mounted to Windows drive letter **E:** (for example), we can mount it for use in Linux with sudo mount but don’t forget we need an existing directory to mount upon:
```
$ sudo mkdir /mnt/e
$ sudo mount -t drvfs E: /mnt/e
```
