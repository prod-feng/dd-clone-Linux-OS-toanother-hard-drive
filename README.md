# dd-clone-Linux-OS-toanother-hard-drive

```text
1. Prepare the new hard drive.

Copy the partition table and boot loader rto the new hard drive

dd if=/dev/sda of=/dev/sdb bs=512 count=2048

mkfs.xfs /dev/sdb1
mkswap /dev/sdb2
mkfs.xfs /dev/sdb3


mount /dev/sdb3 /mnt/

mkdir /mnt/proc /mnt/sys /mnt/boot

mount /dev/sdb1 /mnt/boot 

2. Copy the files to the new hard drive.

#keep the ACL and Selinux context of all the files when copying

rsync -a -A -X / --exclude=/boot --exclude=/mnt --exclude=/proc --exclude=/sys /mnt/

rsync -a -A -X /boot/ /mnt/boot/


3. Update the configuration of booting/mounting on the new hard drive.

vim /boot/grub2/grub.cfg


sed -i 's/uuidxxxxx/uuidyyyy/g'  /mnt/boot/grub2/grub.cfg

vim /mnt/boot/grub2/grubenv

Update the new UUID of the partition, and/or lvm mappers.

vim /mnt/etc/fstab

change the mount point to use label or update with the new UUIDs, or LVM. Can change from LVM to partition, or vise versa.

...
/dev/sda3     /                       xfs     defaults        0 0
/dev/sda1     /boot                   xfs     defaults        0 0
/dev/sda2     none                    swap    defaults        0 0
...

vim /mnt/etc/selinux/config

make sure selinux is disabled or in permissive mode for easy handle first. Can turn selinux on later.

4. 
Shutdown the computer, and boot into the new hard drive.

```
