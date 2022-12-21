# This is a step-by-step guide on how to expand Linux root filesystem / at AWS EC2

## 1. Backup the existing filesystem  ==

This can be archive by creating a volume snapshot

 1. AWS Console -> EC2 -> Instances -> <Desired Instance ID> -> Storage -> <Desired Volume ID> 
 2. Right click at the Volume ID and select "Create Snapshot"
 3. Enter the description and click "Create Snapshot" 
 4. Wait until the snapshot to be "Completed". To check go to   AWS Console -> EC2 - > Snapshots -> look for the snapshot description.

## 2. Modify the EC2 storage size ==
 1. AWS Console -> EC2 -> Instances -> <Desired Instance ID> -> Storage -> <Desired Volume ID> 
 2. Right click at the Volume ID and select "Modify Volume"
 3. Enter the "Size"
 4. Click "Modify"
 5. Wait for the  "Volume State" to be "In-Use"

## 3. Get to know the filesystem format and EC2 type ==
Execute the following  to know filesystem format. Look under the "Type" column. 
```bash
df -hT
```
 
To know EC2 type. If the disk name starts from letter `n` then it is Nitro. If the disk name starts from letter `x` then it is type Xen
```bash
lsbk
```
 
## 4. Extend the partition ==
A Nitro disk might look like this nvme0n1p1 . nvme0n1 is the disk name and p1 is the partition number. To extend the partition p1 
```bash
growpart /dev/nvme0n1 1
```
 
A Xen disk might look like this xvda1. xvda is the disk name and 1 is the partition number. To extend the partition 1
```bash
growpart /dev/xvda 1
```
 
To verify the the partition has been extended issue the following command
```bash
lsblk
```
 
## 5. Extend the filesystem ==
To extend root filesystem with xfs filesystem
```bash
xfs_growfs -d /
```
 
To extend root filesystem with ext4 filesystem at Nitro
```bash
resize2fs /dev/nvme0n1p1
```
 
To extend rott filesystem with ext4 filesystem at Xen
```bash
resize2fs /dev/xvda1
```
