## Arch Linux installation guide for UEFI 16.11.2021
![Arch Linux Logo](https://archlinux.org/static/logos/archlinux-logo-dark-90dpi.ebdee92a15b3.png)  

#### Important links
- [Arch Linux Homepage](https://archlinux.org/ "Arch Linux Homepage")
- [Arch Linux Wiki](https://wiki.archlinux.org/ "Arch Wiki")

#### Default Prerequisites
This installation guide assumes that you've downloaded Arch Linux installation .ISO and made it e.g. a bootable USB media stick. This installation also assumes that you have already booted to the Arch Linux bash screen that looks something like [Image of Arch Linux installation](https://www.lifewire.com/thmb/ZoZhXYbhH8FqeYUzcDBxDoADMgc=/774x0/filters:no_upscale():max_bytes(150000):strip_icc():format(webp)/archlinux1-5bc615bac9e77c005184ea55.jpg "This")

## Chapter 1: Preparing your system
#### 1. Make sure you are connected to the internet
- Check connection with **ping 8.8.8.8**
- If connection works, continue. Troubleshoot with Arch Wiki  
> If you are using wireless, set up wireless using command **iwctl**

#### 2. Check that your system supports UEFI and set system clock
- Check that your system supports UEFI, if the following command prints nothing it means your system does not support UEFI **ls /sys/firmware/efi/efivars**
- Update system clock and check that it works with **timedatectl set-ntp true** & **timedatectl status**
- *(Optional)* Set keyboard layout using **loadkeys _(layout)_**, for example **loadkeys en**

#### 3. Partition disk
- Check the device name you want to install Arch Linux to with **fdisk -l**
- Partition your device with **fdisk _(device name)_**
- Use partition scheme below and write down which partition is which (e.g. /dev/sda2 for swap)
> Atleast 260 MiB of EFI system partition  
> More than 512 MB of linux swap  
> Rest of the system as linux file system  

#### 4. Format partitions
- EFI partition with **mkfs.fat -F 32 _(EFI partition)_**
- Enable swap partition with **mkswap _(swap partition)_**
- Linux file system with **mkfs.ext4 _(linux file system partition)_**

#### 5. Mount linux file system
- Mount the filesystem to /mnt using **mount _(linux file system)_ /mnt**
- Turn on swap with **swapon _(swap partition)_**

## Chapter 2: Installing and setting up your system
#### 6. Pacstrapping
- Pacstrap essential files to your system with example code **pacstrap /mnt base linux linux-firmware linux-headers sudo nano networkmanager wireless_tools wpa_supplicant netctl base-devel**

#### 7. Generate filesystem table
- Write **genfstab -U /mnt >> /mnt/etc/fstab**

#### 8. chroot into the new system
- Write **arch-chroot /mnt**

#### 9. Set localization (optional)
- Open locale.gen with nano and uncomment the locale you want to use, command is nano **/etc/locale.gen**
- Generate locale with command **locale-gen**
- Edit locale.conf and write in LANG name accordingly, command is **nano /etc/locale.conf** and content example is **_LANG=en_US.UTF-8_**
- If you set keyboard layout in part 2, make the changes permanent with **nano /etc/vconsole.conf** with content **_KEYMAP=en_**

#### 10. Network configuration
- Set hostname with **hostnamectl set-hostname _(myhostname)_**
- Set hosts -file content to what is said below with command **nano /etc/hosts**
> 127.0.0.1		localhost  
> ::1				localhost  
> 127.0.1.1	(myhostname) <-- This is the hostname set with hostnamectl set-hostname

#### 11. Set root password and add a standard user
- Set root password with command **passwd**
- Add standard user  
-- **useradd -m -g users -G wheel _(username)_**  
-- **passwd _(username)_**  
-- Open sudo config file with command **EDITOR=nano visudo**  
-- Uncomment the line that says *#%wheel ALL=(ALL) ALL*  
-- Repeat process if you want to add more users

## Chapter 3: Bootloader (GRUB) and important device drivers
#### 12. Installing GRUB
- Download necessary files with **pacman -S grub efibootmgr dosfstools os-prober mtools**
- Create EFI directory using command **mkdir /boot/EFI**
- Mount created directory to efi partition with **mount _(EFI PARTITION)_ /boot/EFI**
- Install grub with **grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck**
- Copy locale to grub with this command **cp /usr/share/locale/en\@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo**
- Set up grub config with **grub-mkconfig -o /boot/grub/grub.cfg**

#### 13. Install drivers
- Install microchip driver for AMD processors with **pacman -S amd-ucode**
- Install microchip driver for Intel processors with **pacman -S intel-ucode**
- Install graphics driver for Intel or AMD GPU with **pacman -S mesa**
- For NVIDIA drivers check the [Arch Linux Wiki Nvidia](https://wiki.archlinux.org/title/NVIDIA "Arch Wiki for your correct GPU driver")

## Chapter 4: Enabling services and installing a desktop environment
#### 14. Enable important services and set correct timezone
- Enable network manager with **systemctl enable NetworkManager**
- List available timezones with **timedatectl list-timezones**
- Set correct timezone with **timedatectl set-timezone _(your timezome)_**  
- Enable timezone sync with **systemctl enable systemd-timesyncd**

#### 15. Install xfce4 with lightdm
- *Skip this step if you want clean Arch Linux without the xfce4 desktop environment*
- Command to download necessary files **pacman -S xorg-server xfce4 xfce4-goodies lightdm lightdm-gtk-greeter**
- Enable login screen with **systemctl enable lightdm**
- Reboot by writing **exit** and then **shutdown now**

#### 16. Reboot and your Arch Linux should be installed and working
- Reboot by writing **exit** and then **shutdown now**
