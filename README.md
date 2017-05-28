# ARCH_linux installation

# pre-install 

[HTTP Direct Downloads] select your country or choose some place to near your country


https://www.archlinux.org/download/ 

*get iso file convert to image and make bootable disk or sd card

# installation
```
select menu > boot linux arch (x86_64)

```
### set partition [3 partition : boot partition , swap partition , user partition] 
*Example have memory 8 GB [3GB for boot partition , 2GB for swap partition , and All remaining for user partition]


*swap partition is a some kind like RAM memmory , helpful for work more speed (like a visual memmory in window)
```
root@archiso # fdisk -l : check all memmory in device plugin
root@archiso # fdisk dev/sdxX : set partition mode 

# create bootable partition
command (m for help) : o : for create partition mode
command (m for help) : n : for create new partition 
command (m for help) : p : select primery type
skip first sector
determine size of last sector Ex. +3G [mean 3 GB]
command (m for help) : a : set flag to bootable partition

# create swap partition
command (m for help) : n : for create new partition [swap partition]
command (m for help) : p : select primery type
skip first sector
determine size of last sector Ex. +2G [mean 2 GB]
command (m for help) : type : for select type for swap partition
partition number : 2
partition type (type L to list all types) : 82 : 82 is a swap type

# create user partition 
command (m for help) : n : for create new partition [swap partition]
command (m for help) : p : select primery type
skip first sector
skip last sector [default : all remain in the drive]

# save partition table
command (m for help) : w

#check partition 
root@archiso # fdisk -l

#format drive 
root@archiso # mkfs.ext4 /dev/sdxX (bootable drive) Ex.1
root@archiso # mkfs.ext4 /dev/sdxX (user drive) Ex.3
```

### set internet access
```
# check ip
root@archiso # ifconfig 
or
root@archiso # ip a

# check internet connection 
root@archiso # ping www.google.com -c 5 [ping google testing 5 time.]

# [in case] not have ip address
root@archiso # dhcpcd : for generate ip

# [in case] use wireless
root@archiso # cp /etc/netctl/examples/wireless-wpa /etc/netctl/[your-wireless-name] : move ex. for build config for wireless setting
root@archiso # nano /etc/netctl/[your-wireless-name] : set interface , security , ESSID=[network name] , keys='secret-password' , Hidden = yes and save [ctrl + x]

# check ip address again 
```

### mount the root partition [have been created]
```
#check drive 
root@archiso # fdisk -l

#mount drive
root@archiso # mount /dev/sdxX /mnt [ex.sda1] : mount boot drive
root@archiso # mkdir /mnt/home : create directoty for user drive
root@archiso # mount /dev/sdxX /mnt/home [ex.sda3] : mount user drive

#check mount
root@archiso # mount
```

### install base [basic software in arch]
```
root@archiso # pacstrap -i /mnt base
```

### create files system mapping
```
root@archiso # genfstab -U -p /mnt >> /mnt/etc/fstab
```

### change root directory for current running process
```
root@archiso # arch-chroot /mnt : change root directory to [/mnt]
```

### wireless configuration
```
# install resource for build cross-platform wireless standard (libraries)(lib hardware legacy)(communicate kernel abount wifi)
root@archiso # pacman -S wpa_supplicant

# install wireless adapter software (wifi manager framework)
root@archiso # pacman -S iw
   basic command iw 
      iw dev 
      iw dev [network interface] link : get link status
      *[Ex.network interface = wlan0]
      iw dev wlan0 scan : scanning for available access point
      iw dev wlan0 set type ibss : set the operation mode to ad-hoc
      iw dev wlan0 connect [your_essid] : connect to open network
      iw dev wlan0 connect your_essid key0:your_key : connecting to wep encrypted network using hexadecimal key
      iw dev wlan0 set power_save on : Enabling power save
```

### time setting
```
root@archiso # nano /etc/locale.gen 
 *find your country
root@archiso # nano locale-gen

#setting hotlink [hardlink]
root@archiso # ln -s /usr/share/zoneinfo/[your country]/[your city]

#sync clock [hardware level]
root@archiso # hwclock --systohc --utc
```

### set root password
```
root@archiso # passwd : set your password
```

### set bootloader for boot system [in rpi not use]
```
# install grub
root@archiso # pacman -S grub

# install bootloader 
root@archiso # grub-install --target=i386-pc --recheck /dev/sdx
root@archiso # cp /usr/share/locale/[your country]/LC_MESSAGES/grub.mo /boot/grub/locale/[your country].mo
*[your country] = check with /usr/share/locale/[tab]

# configuration grub to know
root@archiso # grub-mkconfig -o /boot/grub/grub.cfg
```

### unmount stage
```
#exit chroot
root@archiso # exit 

#unmount drive for prepare reboot
root@archiso # umount /mnt/home
root@archiso # umount /mnt
```
