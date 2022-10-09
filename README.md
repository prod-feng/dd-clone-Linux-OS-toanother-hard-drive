# dd-clone-Linux-OS-to-another-hard-drive


Using dd to clone the boot loader and partition table to a new hard drive, and then using rsync to copy the data. Avoid to copy the whole disk, to do fast disk copy. From here, you can change the partition of the new hard drive, like change from partition to LVM, etc.

```text
1. Prepare the new hard drive.

Copy the partition table and boot loader rto the new hard drive

dd if=/dev/sda of=/dev/sdb bs=512 count=2048

#You can then change the partition table of the new hard drive, like using fdisk, parded, etc.

#make the file systems on the partitions. (can use different fs type from the original one)

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

vim /mnt/boot/grub2/grub.cfg

#change the UUID to the new hard drive, if any.
sed -i 's/uuidxxxxx/uuidyyyy/g'  /mnt/boot/grub2/grub.cfg

vim /mnt/boot/grub2/grubenv

Update the new UUID of the partition, and/or lvm mappers.

#May be you can use chroot to the mounted new hard drive, and use grub2-mkconfig command to update the the grub, but not sure.

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

Plus disk can also been connected to a raid card, set it as RAID0 logical drive, and work to boot. One issue is: When make partitions on the disk, make sure NOT use all of the disk space. The raid card seems need to write some logical format info to the end of the drive. 


