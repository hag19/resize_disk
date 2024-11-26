# resize VM disk in Proxmox
---
## Find and Prepare New Disk
```bash
sudo echo "- - -" | sudo tee /sys/class/scsi_host/host*/scan
sudo pvcreate /dev/sdb1
```
---
## Create Volume Group and Logical Volumes
```bash
sudo vgcreate <group_name> /dev/sdb1
sudo lvcreate -L <size_in_G> -n <volume_name> <group_name>
sudo lvcreate -l <percentage>%FREE -n <volume_name> <group_name>

```
---
## Prepare Filesystem and Update fstab
```bash
echo "#[name of volume]" >> /etc/fstab
echo "/dev/mapper/name_of_volume /name_of_volume ext4 defaults 0 2" >> /etc/fstab
sudo mkfs.ext4 /dev/mapper/<volume_name>
sudo mkdir /<mount_point>
sudo mount -a
```
---
# reduce or extend volum 
## Using Size in Gigabytes
* -r for lvresize -l for size
```bash
sudo lvreduce -r -L -<amount_in_G> /dev/mapper/<volume_name>
sudo lvextend -r -L +<amount_in_G> /dev/mapper/<volume_name>
```
## Using Percentage of Free Space 
* -r for lvresize -l for procentage
```bash
sudo lvreduce -r -l -<percentage>%FREE /dev/mapper/<volume_name>
sudo lvextend -r -l +<percentage>%FREE /dev/mapper/<volume_name>
```
