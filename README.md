## Bootable USB

```bash
lsblk
sudo wipefs --all /dev/sdc
sudo dd bs=4M if=/home/a/archlinux-2020.05.01-x86_64.iso of=/dev/sdc status=progress oflag=sync
```

## Arch-Linux Installation for UEFI

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
## Arch-Linux Installation for Legacy Bios

enable fast boot from system bios
disable UEFI

```bash
    1  timedatectl set-ntp true
    2  lsblk
    3  mkfs.ext4 /dev/sdb1
    4  mkfs.ext4 /dev/sdb2
    5  mkfs.ext4 /dev/sdb3
    6  mount /dev/sdb2 /mnt
    7  mkdir /mnt/boot
    8  mkdir /mnt/home
    9  mount /dev/sdb1 /mnt/boot
   10  mount /dev/sdb3 /mnt/home
   11  lsblk /dev/sdb
   12  pacstrap /mnt base base-devel linux linux-firmware intel-ucode grub os-prober efibootmgr networkmanager neovim
   13  genfstab -U /mnt >> /mnt/etc/fstab

    #arch-root

    1  ln -sf /usr/share/zoneinfo/Asia/Karachi /etc/localtime
    2  hwclock --systohc
    3  nvim /etc/locale.gen
    4  locale-gen
    5  nvim /etc/locale.conf
    6  nvim /etc/hostname
    7  nvim /etc/hosts
    8  passwd
    9  nvim /etc/sudoers
   10  systemctl enable NetworkManager
   11  grub-install --target=i386-pc /dev/sdb
   12  grub-mkconfig -o /boot/grub/grub.cfg
   13  useradd -mG wheel a
   14  passwd a
   15  nmtui
   16  systemctl start NetworkManager
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

##(30-touchpad.conf) /etc/X11/xorg.conf.d/30-touchpad.conf
```bash
Section "InputClass"
    Identifier "touchpad"
    Driver "libinput"
    MatchIsTouchpad "on"
    Option "Tapping" "on"
    Option "TappingButtonMap" "lmr"
EndSection
```
