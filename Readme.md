## Arch Linux installation guide for UEFI 16.11.2021

#### 1. Make sure you are connected to the internet
- Check connection with **ping 8.8.8.8**
- If connection works, continue. Otherswise check wiki
-- If you are using wireless, set up wireless using command **iwctl**

###### 2. Check that your system supports UEFI and set system clock
- type **ls /sys/firmware/efi/efivars** and you should get list of jargon. If you dont get list of jargon, check the wiki because your system does not support UEFI installation
- update system clock with **timedatectl set-ntp true** and check that it works with **timedatectl status**

###### 3. Partition disk
- Write fdisk -l and find the device you want arch linux installed in
- Write fdisk <harddrive> and partition this layout:
-- Atleast 260 MiB of EFI system partition
-- More than 512 MB of linux swap
-- Rest of the system as linux file system
- Type W to write

###### 4. Format these partitions:
-- Linux file system with mkfs.ext4 <partition>
-- mkswap <swap partition>
-- mkfs.fat -F 32 <EFI partition>
! Write down your partition names so you dont mix them up

###### 5. Mount the file systems
- write mount <linux file system> /mnt
- turn on swap with swapon <swap partition>

###### 6. Pacstrapping
- pacstrap essential files to your system, recommended command:
pacstrap /mnt base linux linux-firmware linux-headers sudo nano networkmanager wireless_tools wpa_supplicant netctl base-devel firefox

###### 7. Generate filesystem table
- Write genfstab -U /mnt >> /mnt/etc/fstab

###### 8. chroot into the new system
- Write arch-chroot /mnt

###### 9. Set localization (optional)
- nano /etc/locale.gen, uncomment the locales you want and save
- after saving file write locale-gen to generate locales
- nano /etc/locale.conf and set LANG variable accordingly, for example if you uncommented en_US.utf-8 you write LANG=en_US.UTF-8
- nano /etc/vconsole.conf and write for example KEYMAP=de-latin1

###### 10. Network configuration
- hostnamectl set-hostname <myhostname>
- nano /etc/hosts, set content as:
127.0.0.1		localhost
::1				localhost
127.0.1.1	<myhostname> <-- This is the hostname set with hostnamectl set-hostname

###### 11. Set users
- write passwd and set root password
- add standard user:
-- useradd -m -g users -G wheel <username>
-- passwd <username> to set password
-- write EDITOR=nano visudo
-- Uncomment the line that says #%wheel ALL=(ALL) ALL

###### 12. Installing GRUB
- Download necessary files with pacman -S grub efibootmgr dosfstools os-prober mtools
- write mkdir /boot/EFI to create EFI director
- write mount <EFI PARTITION> /boot/EFI
- Install grub with grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck
- Copy locale to grub this long command, if you dont want grub in english check wiki:
-- cp /usr/share/locale/en\@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo
- write grub-mkconfig -o /boot/grub/grub.cfg

###### 13. Install drivers
- type pacman -S amd-ucode for AMD processors
- type pacman -S intel-ucode for intel processors
- type pacman -S mesa for intel/amd CPU
- if you use nvidia drivers, check arch wiki for your correct driver

###### 14. Other VERY IMPORTANT STUFF
- type systemctl enable NetworkManager to enable networkmanager on start
- set timezone with timedatectl set-timezone <your timezone>
- type systemctl enable systemd-timesyncd to enable time sync

###### 15. Now its time to reboot and test that you can log in. If it works, congratulations!

###### 16. Install xfce4 with lightdm (Optional)
- pacman -S xorg-server xfce4 xfce4-goodies lightdm lightdm-gtk-greeter
- systemctl enable lightdm
- reboot