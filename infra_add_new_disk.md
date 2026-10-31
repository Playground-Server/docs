# Add New Disk
Bought second hand hdd and then installed at util-02 for extra storage

```bash
# Partitioning using cfdisk
sudo cfdisk /dev/sda1
# Delete existing partition then recreate using "Linux filesystem". Write and exit

# Make sure disk type is ext4
sudo mkfs.ext4 /dev/sda1
```

Create `/etc/fstab` entry

```
/dev/sda1 /opt/hdd ext4 defautls 0 0
```

Create new directory,  then mount disk

```
sudo mkdir /opt/hdd
mount /opt/hdd
```