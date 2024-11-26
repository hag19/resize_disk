# resize VM disk in Proxmox

## resize the disk
```bash
qm resize id scsi0 120G
qm config id
```
---
## connect to the vm and then resize with cfdisk
## find new disk if you are adding
```bash
sudo echo "- - -" | sudo tee /sys/class/scsi_host/host*/scan
sudo pvcreate /dev/sdb2
```
---
```bash
sudo vgcreate esgivg /dev/sdb1
sudo lvcreate -L 5G -n lv_esgi esgivg
sudo lvcreate -l 90%FREE -n lv_esgi2 esgivg
```
### Important: When deleting a partition, ensure you're only deleting the partition between sda1 and the unallocated space (usually sda2), not the root partition (sda1) itself.
```bash
sudo cfdisk /dev/sda
```
## move to the portion and delete it sicne its going to be between new allocated space and the sda1 we will realocate them back
## now command to resize for the system and verifay it
```bash
sudo resize2fs /dev/sda1
df -h
lsblk
```
---
## now we will reallocat the memory
```bash
sudo mkswap /dev/sda5
sudo swapon /dev/sda5
echo '/dev/sda5 none swap sw 0 0' | sudo tee -a /etc/fstab
```
* mkswap: Initializes the swap space on /dev/sda5.
* swapon: Activates the swap partition.
* echo '/dev/sda5 none swap sw 0 0' | sudo tee -a /etc/fstab: This command ensures that the swap partition will be mounted automatically on boot by adding it to /etc/fstab.
