# Arch Linux install guide

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
```mount /dev/sda2 /mnt```
```mkdir -p /mnt/boot```
```mount /dev/sda1 /mnt/boot```

### Install the base plus development and GRUB EFI
```pacstrap /mnt base base-devel grub-efi-x86_64```

### Facestab generation
```genfstab -U /mnt >> /mnt/etc/fstab```

### Chroot into the just created OS (with a bash console for GRUB installation)
```arch-chroot /mnt /bin/bash```

### Install efibootmgr (and intel-ucode if needed)
```pacman -S efibootmgr intel-ucode```

### Bootloader installation
```grub-install --efi-directory=/boot```

### Check UEFI firmware entries
```efibootmgr```

### Generate grub.cfg
```grub-mkconfig -o /boot/grub/grub.cfg```

### Set the time zone
```ln -sf /usr/share/zoneinfo/{Region}/{City} /etc/localtime```
e.a.
```ln -sf /usr/share/zoneinfo/Europe/Amsterdam /etc/localtime```

### Generate /etc/adjtime
```hwclock --systohc```

### Edit /etc/locale.gen (uncomment en_US.UTF-8 UTF-8 and other needed localizations)
```nano /etc/locale.gen```

### Generate localizations
```locale-gen```
```cat "LANG=en_US.UTF-8" >> /etc/locale.conf```
```cat "KEYMAP=us" >> /etc/vconsole.conf```
```cat "{myhostname}" >> /etc/hostname```

### Add hostname to /etc/hosts
``nano /etc/hosts```
```127.0.0.1	localhost.localdomain	localhost```
```::1		localhost.localdomain	localhost```
```127.0.1.1	{myhostname}.localdomain	{myhostname}```

### Set the root password
```passwd```

### Exit chroot
```exit```

### Reboot
```reboot```
