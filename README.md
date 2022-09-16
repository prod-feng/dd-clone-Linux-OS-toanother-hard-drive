# dd-clone-Linux-OS-toanother-hard-drive

```text

dd if=/dev/sda of=/dev/sdb bs=512 count=2048

mkfs.xfs /dev/sdb1
mkswap /dev/sdb2
mkfs.xfs /dev/sdb3


mount /dev/sdb3 /mnt/

rsync -a -A -X / --exclude=/boot --exclude=/mnt --exclude=/proc --exclude=/sys /mnt/


mkdir /mnt/proc /mnt/sys /mnt/boot


mount /dev/sdb1 /mnt/boot 

#keep the ACL and Selinux context of all the files when copying
rsync -a -A -X /boot/ /mnt/boot/

vim /boot/grub2/grub.cfg


sed -i 's/uuidxxxxx/uuidyyyy/g'  /mnt/boot/grub2/grub.cfg

vim /boot/grub2/grubenv

Update the new UUID of the partition, and/or lvm mappers.

vim /etc/fstab

change the mount point to use label or update with the new UUIDs, or LVM. Can change from LVM to partition, or vise versa.


vim /etc/selinux/config

make sure selinux is disabled or in permissive mode for easy handle first. Can turn selinux on later.

```
