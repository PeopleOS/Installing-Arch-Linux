# Why?
I'm installing Arch Linux and documenting my process here for my own learning and, hopefully, to help other people too. The documents in this repository will mostly be specific to my hardware, but I hope to branch out into more educational territory as I gain my experience.

My goal for this install is to establish my long-term environment built on Wayland.

# Overview

*A general overview.*

1. [**Partitioning**](#Partitioning) - *Physical drive setup.*
2. [**Boot Strapping**](#Bootstrapping) - *Installing base system from Archiso.*

# Specification Table

| Specification             | Decision   | Explanation | Rationale |
| ------------------------- | ---------- | ----------- | --------- |
| Operating System (Kernel) | GNU/Linux  |             |           |
| Distribution              | Arch Linux |             |           |
| Init process              |            |             |           |
| Graphics stack            |            |             |           |
| Desktop Environment       |            |             |           |
| Partitioning              |            |             |           |
| Network time              |            |             |           |
| Hostname                  |            |             |           |



# Installation

## Partitioning

*Physical drive setup.*



| Num  |   Label    | Type  |          Purpose          | Arch Mount Point |
| :--: | :--------: | :---: | :-----------------------: | :--------------: |
|  1   |    ESP     | fat32 |   EFI System Partition    |   `/boot/efi`    |
|      | Windows 10 | NTFS  |    Windows 10 C drive     |                  |
|      |            | swap  |        Linux Swap         |                  |
|      |            |       |   Arch root filesystem    |                  |
|      |            |       |     Home directories      |                  |
|      |            |       | Files shared between OS's |                  |

## Bootstrapping

*Installing base system from Archiso.*

From within an Archiso environment:

1. Mount the root somewhere accessible

   ```Bash
   (root) mount /dev/sda4 /mnt
   ```

2. Make the directory that the ESP will be mounted to, and mount it there

   ```Bash
   (root) mkdir /mnt/boot; mkdir /mnt/boot/efi; mount /dev/sda1 /mnt/boot/efi
   ```

3. Establish a LAN connection ***HOW!?***

4. Rank mirror list (`/etc/pacman.d/mirrorlist`) ***HOW!?***

5. Use `pacstrap` to install the following packages to `/mnt`

<u>Base Packages</u>

| Package Name |                          Motivation                          |
| :----------- | :----------------------------------------------------------: |
| base         | Core of OS: GNU/Linux with Systemd, Arch Build System, and Pacman |
| base-devel   |                Programming and customization                 |
| btrfs-progrs |        My home directories live in a BTRFS partition         |
| ntfs-3g      |                  Reading Windows partitions                  |
| exfat-utils  |                  Reading Windows partitions                  |
| intel-ucode  |                   Patches for intel chips                    |

6. When all the [partitions](#Partitioning) have been mounted as they should at boot, generate the `fstab` file.

```Bash
(root) genfstab -U /mnt > /mnt/etc/fstab
```

7. Setup Bootloader ***HOW!?***

* *Hint*: Arch provides a wonderful tool for repairing damaged systems that extends the GNU/Linux `chroot` command with `arch-chroot`.

8. Reboot to setup root account

## Creating Root Account

1. Login as root and set root password ***Will it auto-login?

   ```bash
   (root) passwd
   ```

## Configuring Time

1. Both Windows 10 and Linux should be configured to read the hardware clock as UTC.

   1. For Linux:

      ```bash
      (root) timedatectl set-local-rtc 0
      ```

   2. For Windows 10: Add `DWORD` registry value at `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\TimeZoneInformation\RealTimeIsUniversa` with hexadecimal value `1`
      * If the above appears to have no effect, and a 64-bit variant of Windows is being used, using a `QWORD` value instead of a `DWORD` value may resolve the issue.
      * Should Windows ask to update the clock due to DST changes, let it. It will leave the clock in UTC as expected, only correcting the displayed time.

2. The time zone must be set

   1. List available zones

      ```
      timedatectl list-timezones
      ```

   2. To set your time zone: 

      ```bash
      (root) timedatectl set-timezone Zone/SubZone
      ```

3. Set the system clock

   ```bash
   (root) timedatectl set-time "yyyy-MM-dd hh:mm:ss"
   ```

6. Update the hardware clock

   ```bash
   (root) hwclock --systohc
   ```

## Creating User Account

1. Create user and add to groups wheel and disk

   ```bash
   (root) useradd -m -G [name]
   ```

2. Set user's password

   ```bash
   (root) passwd [name]
   ```

3. Include User's full name

   ```bash
   (root) usermod -c "Full Name" [name]
   ```

4. Enable sudo

   1. Open the sudoers file for safe editing

   ```bash
   (root) EDITOR=nano visudo
   ```
   2. Uncomment the section for wheel



# Old stuff...

## Configuring Wireless Internet

Connect over temporary LAN connection.

1. Set hostname

```bash
(root) echo my_hostname > /etc/hostname
```

1. 



Identify adapter with: ip link

Ip link set [device] up

Used: systemctl start dhcpcd@[device].service

Uncomment locale (en_US…) (/etc/locale.gen)

Locale-gen

Localectl set-locale LANG=(en_US…)

ln -sf /usr/share/zoneinfo/America/Chicago /etc/localtime

Timedatectl set-local-rtc 1

Enable options in pacman.conf and connect to wireless

Uncomment color and multilib (saved to file in git now)

Add lines for yaourt (Saved in git)

Install:

yaourt

I have started using aurman instead of yaourt because its solver is better, it's maintained, and it's more secure. I think I will miss yaourt's ability to show AUR comments, but I bet I can work out a solution for that myself. I don't yet understand the build process well enough to install aurman on my own, but I really think I should learn how to do that soon.

(Optional) downgrader



Note: I have systemd check the root filesystem and not the kernal.

Copy the files /usr/lib/systemd/system/



systemd-fsck-root.service

systemd-fsck@.service



to /etc/systemd/system/ and edit them, configuring StandardOutput and StandardError like this:





[Service]

Type=oneshot

RemainAfterExit=yes

ExecStart=/usr/lib/systemd/systemd-fsck (with or without a %f here)

StandardOutput=null

StandardError=journal+console

TimeoutSec=0



CLI Basics

Install:

tmux

neovim + mkpdf(for latex integration - though I want to replace this with a more minimal distribution) + nodejs,python-neovim,python2-neovim(for web development integration) + (>pip install neovim) for deoplete

openssh

git

gdb

lsof

ranger

w3m

p7zip

Ensure that vi is removed as it is aliased to nvim. Make sure the do files repo is cloned with submodules. Run the dotfiles installation script.

Ensure that git password saving and name (system user comment for full name or config file) are configured properly.



I think it would be convenient to swap the caps lock and escape keys. Ideally, the caps lock indicator would still work.



Internet Connectivity

Install:

wpa_supplicant

Make sure that xinitrc links WiFiStartup script properly. Use NOPASSWD in visudo to provide specific permissions for the following commands to all accounts:

keith ALL=(ALL) NOPASSWD: /usr/bin/ip link set dev wlp1s0 up

keith ALL=(ALL) NOPASSWD: /usr/bin/wpa_supplicant –B -I wlp1s0 –c /etc/wpa_supplicant/wpa_supplicant-wlp1s0.conf

keith ALL=(ALL) NOPASSWD: /usr/bin/dhcpcd wlp1s0



also add

Defaults env_keep += "PAGER TERM VISUAL"

 Install:

insync

dropbox



Graphics and Audio Support

Graphics Support

Install:

xf86-video-intel

xorg-server

xorg-xinit

xorg-xbacklight -> can be replaced with acpilight(?)



An ICC color profile should be generated on the Windows system and then uploaded to the dotfiles repo for use in Linux as well.



Audio Support

Install:

pulseaudio

pavucontrol

pulseaudio-ctl



Now proceed to Graphical Environment Design and Installation Steps.



Updating Bios:

<http://www.dell.com/support/article/us/en/19/sln284433/what-is-bios-and-how-to-update-the-bios-on-your-dell-system?lang=en>

http://www.dell.com/support/article/us/en/19/sln305230/flashing-the-bios-from-the-f12-one-time-boot-menu?lang=en





Laptop-Specific Tools

Install:

acpi (used by i3blocks as of 29-Dec-2017)



(Future) Backup System

Cloud Interface

Consider installing:

rclone

Goal

Proper file management scripts for using syncing software - use test-directories first

Copies of (or actually, linked to) system files, configurations for command line tools, and configurations for the gui are kept in a dotfiles GitHub repo



Progress

Concerning Synchronization Method

I reason that using something like insync is totally fine as long as I can occasionally choose to exercise more control over it.  If I want some stuff to be more secure, I can encrypt individual files or have separate encrypted file systems synced securely somewhere else, some other way.



I conclude then that using rclone to migrate from one synced system to another is good for learning about the process, and using insync is also a wise time-saving decision.



Active Steps

Running |> rclone check | to compare my google drive with the filesystem kept in sync by insync on my windows machine

Unfortunately, I forgot about the conversions performed by insync.

My hope now is to figure out how to use the command line insync to preserve my previous synchronization.

Happily, insync does this automatically!! Cool :)



Next Steps

Next I would like to use rclone to configure my OneDrive and Dropbox services. Keeping them in one place seems attractive, and I would do the same with Drive if rclone could perform the conversion that insync does.



I probably don't need Mega anymore now that I have discovered git and use onenote for all of my text-based system notes, but as long as I can trust their encryption it could be really cool to make a repository of sorts of large and useful iso files that can be synchronized easily with a system-tools thumb drive.



[Random Header]: 