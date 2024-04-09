<div align="center">
  <img src="https://i.imgur.com/SZRtuUM.png" width="500"><br/>
  <a href="README.pt-BR.md">
    <img src="https://img.shields.io/badge/Read%20in-Portuguese-blue"/><br/>
  </a>
  <p>📔 A little guide to install the Arch Linux for me</p>
</div>


## Keyboard 
First, check if my keyboard is in the correct layout, since by default the Arch installation comes with the keyboard in en-US layout.

To change the layout, use the following command:

```bash
loadkeys br-abnt2
```

## Internet Connection
I usually use my computer directly wired, but there are some cases where I will need to use Wi-Fi. In that case, I use the [iwd](https://wiki.archlinux.org/title/iwd) package.

To use it, start with the command:

```bash
iwctl

```
Find out the name of the Wi-Fi network device on your computer with the command:

```
[iwd]# device list
```

<div align="center">
  <img src="https://i.imgur.com/GGxULsZ.png">
</div>

Now that we know our device is called wlan0, we use the following command to scan for nearby networks:

```
[iwd]# station wlan0 get-networks
```

Now to connect to a network, use:

```
[iwd]# station wlan0 connect REDE-2.4G
```

Enter the password and the Wi-Fi will be connected.

To check, just ping any website.

## Disk Partitioning
First, run the **fdisk** command to list the partitions:

```bash
fdisk -l
```

It will list all the storage devices on the computer, the disk that is normally used is **/dev/sda**

<div align="center">
  <img src="https://i.imgur.com/62SPwt3.png">
</div>

Now to partition the disk we use the **cfdisk** tool because it is easy to use.

In my case, I always create 3 partitions:
- /dev/sda1 (500MB for /boot/efi for UEFI systems)
- /dev/sda2 (2GB for swap)
- /dev/sda3 (all the rest for /)

<div align="center">
  <img src="https://i.imgur.com/JK2LpVA.png">
</div>

Always set the partition type for their respective functions.

Then, make cfdisk write the partitions by clicking on the "Write" option and exit by going to the "Quit" option.

## Formatting the partitions
After creating all the partitions, we will format them to their respective formats.

To format the boot partition (/boot/efi):
```bash
mkfs.fat -F 32 /dev/sda1
```
To create the Swap partition:
```bash
mkswap /dev/sda2
```
To format the system root partition:
```bash
mkfs.ext4 /dev/sda3
```

## Mounting the system
After formatting, the next step is to mount the partitions, for this we initially mount the system root partition with:
```bash
mount /dev/sda3 /mnt
```
Then, we create the **/boot/efi** directory of the system using the command:
```bash
mkdir -p /mnt/boot/efi
```
We mount the boot partition in this created directory:
```bash
mount /dev/sda1 /mnt/boot/efi
```
We activate the Swap partition:
```bash
swapon /dev/sda2
```

## Installing Arch Linux installation packages
Now we will install the base packages and the Arch Linux kernel as well as some text editor tools (vim) and network management tool (dhcpcd):
```bash
pacstrap -K /mnt base base-devel linux linux-firmware vim dhcpcd
```
This is the most time-consuming process of the installation as it depends on the download speed.

After the installation, we generate our **fstab** table which tells the boot where each of the partitions is mounted with the command:
```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

## Inside the system
To enter the system environment to make the configurations we will use the command:
```bash
arch-chroot /mnt
```
This part is optional for me, but we can set the system date and time by creating a symbolic link from our region's file to our localtime.

In my case, using Local Time in Brasilia would be:

```bash
ln -sf /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime/
```

The next step would be to configure the system language, in this case I like to leave the system in English, so let's edit the **locale.gen** file:

```bash
vim /etc/locale.gen
```
Within it, there will be various languages commented out with a **#**, so we will go to the language we want and uncomment the language we want by removing the **#** from the beginning of the line.

In my case, it's **en_US.UTF-8 UTF-8**

<div align="center">
  <img src="https://i.imgur.com/7XZFpq3.png">
</div>

After that, save the file and generate the language configuration using the command
```bash
locale-gen
```
Now we will configure the computer name on the network, to do this, edit the file **/etc/hostname** and put the computer name, in my case, I always put it as **arch**

## Setting Users

First, set the password for the **root** user (very important)
```bash
passwd
```

Create your user, I will use my username *mdxv* as an example with the command
```bash
useradd -m -g users -G wheel -s /bin/bash mdxv
```
Then set a password for this user

```bash
passwd mdxv
```

Now we will install some useful packages and then install GRUB
```bash
pacman -S dosfstools os-prober mtools dialog grub efibootmgr sudo
```

If I have installed using Wi-Fi, I may need to install these additional programs
```bash
pacman -S wpa_supplicant wireless_tools
```

## GRUB Installation

To install GRUB on my system that uses the UEFI system, install GRUB with the command
```bash
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=arch_grub --recheck
```
And generate the GRUB configuration file
```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

If I am installing GRUB on a BIOS-Legacy system, the following commands will be used
```bash
grub-install --target=i386-pc --recheck /dev/sda
```
And generate the configuration file
```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

Finally, restart the system and if all goes well, it will start with GRUB

<div align="center">
  <img src="https://i.imgur.com/5NEF1vW.png">
</div>

## Post-Installation

After starting the system, log in as our user and we will see that we do not have permission to use the sudo command because we have to add the user to the *sudoers* file and configure the network with *dhcpcd*

For this, we need to log in as **root** with the command
```bash
su
```
Enter the root user's password.

Now we will edit the sudoers file, for this, we will use the visudo command defining the text editor with **vim**
```bash
EDITOR=vim visudo
```
Look for the line `%wheel ALL=(ALL:ALL) ALL` uncomment it by removing the "#" from the front of the text

Now exit the root user and check if it is already possible to use the root command using the **dhcpcd** command to configure the Internet
```bash
sudo dhcpcd
```
And that's it, Arch Linux is installed and configured, from there you just need to configure your system the way you want.

---
<div align="center">
  <img src="https://i.imgur.com/GSschky.jpeg">
</div>
