## Bootable USB

```bash
lsblk
sudo wipefs --all /dev/sdc
sudo dd bs=4M if=/home/a/archlinux-2020.05.01-x86_64.iso of=/dev/sdc status=progress oflag=sync
```

## Arch-Linux Installation

disable fast boot if on windows from control panel and also from system bios

enable UEFI for x86_64

```bash
su root #if not root
lsblk
wipefs -all /dev/sdX
cfdisk /dev/sdX

label gpt
/dev/sda1 512M linux boot EFI
/dev/sda2 12G linux swap
/dev/sda3 25G linux root
/dev/sda4 remaining linux home

mkfs.vfat /dev/sdx1
mkfs.ext4 /dev/sdx3
mkfs.ext4 /dev/sdx4

mount /dev/sdx3 /mnt
mkdir /mnt/boot
mkdir /mnt/home
mount /dev/sdx1 /mnt/boot
mount /dev/sdx4 /mnt/home

pacstrap /mnt base base-devel linux linux-firmware intel-ucode grub efibootmgr os-prober neovim networkmanager

mkswap /dev/sdx2
swapon /dev/sdx2

genfstab -U /mnt>>/mnt/etc/fstab
cat /mnt/etc/fstab
arch-chroot /mnt
```

## ar-chroot /mnt

```bash
ln -sf /usr/share/zoneinfo/Asia/Karachi /etc/localtime
hwclock --systohc
nvim /etc/locale.gen
locale-gen
nvim /etc/locale.conf
echo pc > /etc/hostname
echo "127.0.0.1        localhost" >> /etc/hosts
echo "::1              localhost" >> /etc/hosts
echo "127.0.1.1        pc.localdomain pc" >> /etc/hosts

passwd
useradd -mG wheel a
passwd a

mkdir /boot/efi && grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
grub-mkconfig -o /boot/grub/grub.cfg

systemctl enable NetworkManager
exit
umount -a
reboot
```

## Login as pc@a

```bash
su root
nmtui
pacman -Syu
pacman -Syy
pacman -Syyy
curl -LO https://larbs.xyz/larbs.sh
pacman -S xf86-video-intel
yay redshift
redshift -O 3700
redshift -x 
```
##(intel-error-sol)

```bash
nvim /etc/default/grub
GRUB_CMDLINE_LINUX_DEFAULT="nomce"

#mkgrubconfig
sudo exec grub-mkconfig -o /boot/grub/grub.cfg
```

##(30-touchpad.conf)

Section "InputClass"
    Identifier "touchpad"
    Driver "libinput"
    MatchIsTouchpad "on"
    Option "Tapping" "on"
    Option "TappingButtonMap" "lmr"
EndSection
