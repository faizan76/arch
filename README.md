su root
lsblk
cfdisk /dev/sda
mkfs.vfat /dev/sda1
mkfs.ext4 /dev/sda2
mount /dev/sda2 /mnt
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
ping google.com
pacman -Syy
basestrap /mnt base base-devel runit elogind-runit
basestrap /mnt linux linux-firmware
fstabgen -U /mnt >> /mnt/etc/fstab
artools-chroot /mnt
ln -sf /usr/share/zoneinfo/Asia/Karachi /etc/localtime
hwclock --systohc
pacman -Sy neovim
nvim /etc/locale.gen
locale-gen
pacman -S grub os-prober efibootmgr intel-ucode dhcpcd-runit
grub-install --recheck /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
passwd
useradd -m a
passwd a
usermod -a -G wheel a

nvim /etc/sudoers
#uncomment %wheel

nvim /etc/hostname
nvim /etc/hosts
#127.0.0.1	localhost
#::1		localhost
#127.0.1.1	pc.localdomain pc

#Set up static internet through dhcpcd

#interface enp3s0
#static ip_address=192.168.0.42
#static routers=192.168.0.1
#static domain_name_servers=192.168.0.1 8.8.8.8

#enable the service
ln -s /etc/runit/sv/dhcpcd /run/runit/service

nvim /etc/locale.conf
#export LANG="en_US.UTF-8"

sudo sv up dhcpcd
sudo sv stop dhcpcd
sudo sv restart dhcpcd
sudo sv status dhcpcd

cd /run/runit/service
find . -maxdepth 1 -type l -ls

#disable a service 
unlink /run/runit/service/dhcpcd

exit 
umount -R /mnt
reboot

