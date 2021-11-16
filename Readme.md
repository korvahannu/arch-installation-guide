## Arch Linux installation guide for UEFI 16.11.2021

This guide is for personal use, but if it helps anyone to install Arch Linux to their system I'd be happy.

#### 1. Make sure you are connected to the internet
- Check connection with **ping 8.8.8.8**
- If connection works, continue. Otherswise check wiki
-- If you are using wireless, set up wireless using command **iwctl**

#### 2. Check that your system supports UEFI and set system clock
- Check that your system supports UEFI, if the following command prints nothing it means your system does not support UEFI **ls /sys/firmware/efi/efivars**
- Update system clock and check that it works with **timedatectl set-ntp true** & **timedatectl status**
- *(Optional)* Set keyboard layout using **loadkeys <layout>**, for example **loadkeys en**

#### 3. Partition disk
- Check the device name you want to install Arch Linux to with **fdisk -l**
- Partition your device with **fdisk <device name>**
- Use partition scheme below  
>> Atleast 260 MiB of EFI system partition  
>> More than 512 MB of linux swap  
>>Rest of the system as linux file system  
>>*I recommend writing partition names down, such as /dev/sda1 for efi, /dev/sda2 for swap and /dev/sda3 for linux filesystem*

#### 4. Format partitions
- EFI partition with **mkfs.fat -F 32 <EFI partition>**
- Enable swap partition with **mkswap <swap partition>**
- Linux file system with **mkfs.ext4 <linux file system partition>**

#### 5. Mount linux file system
- Mount the filesystem to /mnt using **mount <linux file system> /mnt**
- turn on swap with **swapon <swap partition>**

#### 6. Pacstrapping
- pacstrap essential files to your system with example code **pacstrap /mnt base linux linux-firmware linux-headers sudo nano networkmanager wireless_tools wpa_supplicant netctl base-devel firefox**

#### 7. Generate filesystem table
- Write **genfstab -U /mnt >> /mnt/etc/fstab**

#### 8. chroot into the new system
- Write **arch-chroot /mnt**

#### 9. Set localization (optional)
- Open locale.gen with nano and uncomment the locale you want to use, command is nano **/etc/locale.gen**
- after saving file generate locale with command **locale-gen**
- edit locale.conf and write in LANG name accordingly, command is **nano /etc/locale.conf** and content example is **LANG=en_US.UTF-8**
- if you set keyboard layout in part 2, make the changes permanent with **nano /etc/vcconsole.conf** with content **KEYMAP=en**

#### 10. Network configuration
- Set hostname with **hostnamectl set-hostname <myhostname>**
- Set hosts content with command **nano /etc/hosts**, set content as:
> 127.0.0.1		localhost  
> ::1				localhost  
> 127.0.1.1	<myhostname> <-- This is the hostname set with hostnamectl set-hostname

#### 11. Set users
- Set root password with command **passwd**
- add standard user:
-- **useradd -m -g users -G wheel <username>**
-- **passwd <username>**
-- Open sudo config file with command **EDITOR=nano visudo**
-- Uncomment the line that says *#%wheel ALL=(ALL) ALL*

#### 12. Installing GRUB
- Download necessary files with **pacman -S grub efibootmgr dosfstools os-prober mtools**
- Create EFI directory using command **mkdir /boot/EFI**
- Mount created directory to efi partition with **mount <EFI PARTITION> /boot/EFI**
- Install grub with **grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck**
- Copy locale to grub with this command **cp /usr/share/locale/en\@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo**
- Set up grub config with **grub-mkconfig -o /boot/grub/grub.cfg**

#### 13. Install drivers
- type **pacman -S amd-ucode** for AMD processors
- type **pacman -S intel-ucode** for intel processors
- type **pacman -S mesa** for intel/amd CPU
- *if you use nvidia drivers, check Arch Wiki for your correct driver*

#### 14. Other VERY IMPORTANT STUFF
- Enable network manager with **systemctl enable NetworkManager**
- Set correct timezone with **timedatectl set-timezone <your timezome>**
- Enable timezone sync with **systemctl enable systemd-timesyncd**

#### 15. Reboot and your Arch Linux should be installed and working

#### 16. Install xfce4 with lightdm (Optional)
- **pacman -S xorg-server xfce4 xfce4-goodies lightdm lightdm-gtk-greeter**
- **systemctl enable lightdm**
- *reboot*