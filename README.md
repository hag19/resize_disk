# resize VM disk in Proxmox
# If you want to resize your physical disk while it's in use, you need to boot into a live ISO. This is because you cannot resize a physical disk that is currently in use.
# thers two ways to do it either you going to with cfdisk/fdisk or LVM(Red Hat tool)
## cfdisk/fdisk solution 

### Partition Resizing Using cfdisk
```bash
sudo cfdisk /dev/sdb
```
* Steps:
  * Select the partition you need.
  * Choose Resize.
  * Adjust the size (default is often the maximum available free space).
  * Write changes to the disk.
### Partition Resizing Using fdisk
```bash
sudo lsblk
sudo cfdisk /dev/sdb
```
* Steps:
  * Use n to create a new partition.
  * Assign a partition number and size.
  * Use w to write changes.
---
## lvm solution
### Find and Prepare New Disk
```bash
sudo echo "- - -" | sudo tee /sys/class/scsi_host/host*/scan
sudo pvcreate /dev/sdb1
```

### Create Volume Group and Logical Volumes
```bash
sudo vgcreate <group_name> /dev/sdb1
sudo lvcreate -L <size_in_G> -n <volume_name> <group_name>
sudo lvcreate -l <percentage>%FREE -n <volume_name> <group_name>

```
* Details:
  * Use vgcreate to create a volume group from the initialized physical volume (/dev/sdb1).
  * Create logical volumes with specific sizes or percentages of free space.

### Prepare Filesystem and Update fstab
```bash
echo "#[name of volume]" >> /etc/fstab
echo "/dev/mapper/<volume_name> /name_of_volume ext4 defaults 0 2" >> /etc/fstab
sudo mkfs.ext4 /dev/mapper/<volume_name>
sudo mkdir /<volume_name>
sudo mount -a
```
* Steps:
  * Format the logical volume with mkfs.ext4.
  * Create a directory as the mount point.
  * Add the volume to /etc/fstab for persistent mounting.
  * Run mount -a to apply changes.

### reduce or extend volum 
* Use - for reducing and + for extending.
* The -r flag resizes the filesystem along with the logical volume.
* -l for procentage -L amount in M/G/T
```bash
sudo lvreduce -r -L -<amount_in_G> /dev/mapper/<volume_name>
sudo lvextend -r -L +<amount_in_G> /dev/mapper/<volume_name>
```
* Using Percentage of Free Space 
```bash
sudo lvreduce -r -l -<percentage>%FREE /dev/mapper/<volume_name>
sudo lvextend -r -l +<percentage>%FREE /dev/mapper/<volume_name>
```
