# resize VM disk in Proxmox
---
## find new disk if you are adding
```bash
sudo echo "- - -" | sudo tee /sys/class/scsi_host/host*/scan
sudo pvcreate /dev/sdb2
```
---
## create the volumes and goup
```bash
sudo vgcreate replace_with_your_name /dev/sdb1
sudo lvcreate -L chose_the_sizeG -n replace_with_your_name replace_with_group_name
sudo lvcreate -l chose_the_procent%FREE -n replace_with_your_name
```
---
## create now direction and change fstab file
```bash
echo "#[name of volume]" >> /etc/fstab
echo "/dev/mapper/name_of_volume /name_of_volume ext4 defaults 0 2" >> /etc/fstab
sudo mkfs.ext4 /dev/mapper/name_of_vomulumes
sudo mkdir /name_of_volume
sudo mount -a
```
---
# reduce or extend volum 
## reduce and extend with amount of G
```bash
sudo lvreduce -r -L -replace_with_amount_of_G /dev/mapper/name_of_volume
sudo lvextend -r -L -replace_with_amount_of_G /dev/mapper/name_of_volume
```
## reduce and extend with procent of free memory
```bash
sudo lvreduce -r -l -replace_with_procent_%FREE /dev/mapper/name_of_volume
sudo lvextend -r -l -replace_with_procent_%FREE /dev/mapper/name_of_volume
```
