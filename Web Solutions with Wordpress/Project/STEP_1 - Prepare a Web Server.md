# Step 1

Launch an EC2 instance that will serve as 'web server'. Create 3 volumes in the same AZ as your web server EC2, each of 10 Gib

![image](image/centos%20launch%203.jpg)
![image](image/centos%20launch%204.jpg)
![image](image/centos%20launch%205.jpg)

add 3 volumes here, so they'll be 4, ie, root volume and the other 3

![image](image/add%20volume.jpg)

Then launch your instance.

Confirm your volumes are up and in use.

![image](image/volumes%20in%20use.jpg)

Open your Linux terminal to begin configurations. SSH into your instance

```powershell
ssh -i keypair.pem ec2-user@public-ip
```

List attached volumes

```powershell
lsblk
```

![image](image/lsblk%201.jpg)

Use `df -h` to see all mounted drives and free spaces

Create a single partition on each of the 3 disks

```powershell
sudo gdisk /dev/nvme1n1
```

Type n to create a new partition.  
Enter the partition number (default is 1).  
Enter the first sector (default is fine).  
Enter the last sector or size.  
Type w to write the partition table and exit.

Use the `lsblk` utility to see all newly created partitions for the 3 volumes.

![image](image/lsblk%201.2.jpg)

Install lvm2 package. Lvm2 is used for managing disk drives and other storage devices

```powershell
sudo yum install lvm2
```

![image](image/install%20lvm2.jpg)

Run `sudo lvmdiskscan` to check for available partitions.  
Use the pvcreate utility tool to mark each of the volumes as physical volumes

```powershell
sudo pvcreate /dev/nvme1n1p1
sudo pvcreate /dev/nvme2n1p1
sudo pvcreate /dev/nvme3n1p1
```

Verify that the physical volume has been created `sudo pvs`

![image](image/sudo%20pvs.jpg)

Add all 3 PVs to a volume group called `webdata-vg`

```powershell
sudo vgcreate webdata-vg /dev/nvme1n1p1  /dev/nvme2n1p1  /dev/nvme3n1p1
```

Verify the setup by running `sudo vgs`

![image](image/sudo%20vgs.jpg)

Create 2 logical volumes; app-lv and logs-lv.

```powershell
sudo lvcreate -n app-lv -L 14G webdata-vg
sudo lvcreate -n logs-lv -L 14G webdata-vg
```

Verify that the logical volumes has been created `sudo lvs`

![image](image/sudo%20lvs.jpg)

Verify the entire setup to be sure all has been configured properly

```powershell
sudo vgdisplay -v #view complete setup - VG, PV, and LV
sudo lsblk
```

![image](image/sudo%20lsblk.jpg)

format the logical volumes using ext4 filesystems

```powershell
sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
```

Create a directory to store website file

```powershell
sudo mkdir -p /var/www/html
```

Create another directory for the log files

```powershell
sudo mkdir -p /home/recovery/logs
```

Mount the newly created directory for website files on the app logical volume we earlier created

```powershell
sudo mount /dev/webdata-vg/apps-lv/ /var/www/html/
```

Back up all the files on the logs logical volume before mounting, this is done using rsync utility

```powershell
sudo rsync -av /var/log/. /home/recovery/logs/
```

![image](image/html%20logs.jpg)

Mount the .var/logs on the log-lv

```powershell
sudo mount /dev/webdata-vg/logs-lv/   /var/log/
```

Restore the log files back into /var/log/ directory

```powershell
sudo rsync -av /home/recovery/logs/. /var/log
```

Ensure that the mount configurations persist after server restart, this can be done by updating the UUID of the /etc/fstab

```powershell
sudo blkid
```

![image](image/sudo%20blkid.jpg)

Copy the logs and apps UUID then eplace the UUID for the log-lv with the one you copied , save and exit.

```powershell
sudo vi /etc/fstab
```

test the configuration

```powershell
sudo mount -a
```

Reload the daemom

```powershell
sudo systemctl reload daemon
```

Verify the setup

```powershell
df -h
```

![image](image/daemon.jpg)
