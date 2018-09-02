# Arch Linux install guide

## Partitions

### Use cfdisk to format the partitions
```cfdisk /dev/sda```

* Create a 100MB EFI partition (100M) (sda1)
* Create a {MAX_SIZE} minus {MEM_SIZE} Linux partition (sda2)
* Create a {MEM_SIZE} swap partition (e.a. 16G) (sda3)


### Create ESP filesystem
```mkfs.vfat -F32 -n EFI /dev/sda1```


### Create ext4 filesystem
```mkfs.ext4 /dev/sda2```


### Create swap
```mkswap /dev/sda3```


### Mount the partitions
```
mount /dev/sda2 /mnt
mkdir -p /mnt/boot
mount /dev/sda1 /mnt/boot
swapon /dev/sda3
```


## Installation

### Install the base plus development
```pacstrap /mnt base base-devel```


### Facestab generation
```genfstab -U /mnt >> /mnt/etc/fstab```


### Chroot into the just created OS (with a bash console for GRUB installation)
```arch-chroot /mnt /bin/bash```


## Bootmanager

### Install efibootmgr (and intel-ucode if needed)
```pacman -S efibootmgr intel-ucode iproute2 sudo nano```


### List all UUIDs
```blkid```


### Bootloader installation
```efibootmgr --disk /dev/sdX --part Y --create --label "Arch Linux" --loader /vmlinuz-linux --unicode 'root=PARTUUID=XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX rw initrd=\intel-ucode.img initrd=\initramfs-linux.img' --verbose```
Where /dev/sdX and Y are the drive and partition number where the ESP is located. Change the root= parameter to reflect your Linux root partition.


## Localization

### Set the time zone
```ln -sf /usr/share/zoneinfo/{Region}/{City} /etc/localtime```

e.a.
```ln -sf /usr/share/zoneinfo/Europe/Amsterdam /etc/localtime```


### Generate /etc/adjtime
```hwclock --systohc```


### Edit /etc/locale.gen
```nano /etc/locale.gen```

Uncomment ```en_US.UTF-8 UTF-8``` and other needed localizations

### Generate localizations
```
locale-gen
echo "LANG=en_US.UTF-8" >> /etc/locale.conf
echo "KEYMAP=us" >> /etc/vconsole.conf
```


## Networking

### Activate the network interface
```ip link set dev {interface} up```

e.a. ```ip link set dev eno1 up```

### Activate network interface with DHCP
```cp /etc/netctl/examples/ethernet-dhcp /etc/netctl```

Rename eth0 (e.a. to eno1) if needed
```nano /etc/netctl/ethernet-dhcp```


### Set the hostname
```hostnamectl set-hostname {myhostname}```


### Add hostname to /etc/hosts
```nano /etc/hosts```

Add:
```
127.0.0.1 localhost
::1       localhost
127.0.1.1 {myhostname}.localdomain {myhostname}
```


## Users

### Ctreate user
```
useradd {myusername}
passwd {myusername}
mkdir /home/{myusername}
chown {myusername}:{myusername} /home/{myusername}
```

### Add user to sudoers
```
export EDITOR=nano
visudo
```

Add: ```{myusername} ALL=(ALL) ALL```


## Finalizing

### Set the root password
```passwd```


### Exit chroot and reboot
```exit```

```reboot```
