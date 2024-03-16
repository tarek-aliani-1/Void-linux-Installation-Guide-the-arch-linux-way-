# Void linux Installation Guide (arch linux installation way)
" Go into Void Linux with our 'Arch Linux-style' guide! 💻🐧"

## Pre-Installation:

1. **Acquiring the Installation Image:**
   - Visit the [Void Linux Download page](https://voidlinux.org/download/) and obtain the base image for x86_64 Architecture (glibc). If using a different version, adjustments may be necessary.
   
2. **Prepare an Installation Medium:**
   - Use a tool such as:
     - Rufus: [Download Rufus](https://rufus.ie) (for Windows users 😂)
     - Etcher: [Download Etcher](https://etcher.balena.io) (recommended for its cross-platform compatibility 💀💙)
     - Ventoy: [Download Ventoy](https://ventoy.net) (useful for burning multiple .iso files, but may encounter errors)

3. **Boot the Live Environment:**
- Depending on your PC/laptop, press {Esc, F9, F12, ...} to bring the boot menu and boot from the
installation medium you set up. If you have the boot menu disabled, you can change the boot order from the
BIOS. Once you boot into Void, log in using root as the username and voidlinux as the password. Start the
bash shell using the command `PS1='[ W ] # ' /bin/bash`.

## System Configuration:

### 1.5 Setting the Console Keyboard Layout and Font:

- **Keyboard Layout:** To change the layout, use `loadkeys "*Layout*"`. You can find layouts in

`/usr/share/kbd/keymaps`.

- **Changing Font:** Adjust the font with `setfont "*FONT*"`. Reset to default with `setfont`, and enlarge

with `setfont -d`. Terminal fonts are in `/usr/share/kbd/consolefonts/`.

### 1.6 Verifying the Boot Mode:

- Run `cat /sys/firmware/efi/fw_platform_size` to check.

 - **64-bit UEFI:** If it returns 64, it's UEFI with a 64-bit x64 UEFI.

 - **32-bit UEFI:** If it returns 32, it's UEFI with a 32-bit IA32 UEFI.

 - **BIOS/CSM:** If the file doesn't exist, it's BIOS/CSM mode.

### 1.7 Connecting to the Internet:

- **Wireless Setup:** Use `void-installer` to set up the network, then stop the installation. Confirm with

`ping voidlinux.org`.

## Disk Partitioning Guide

### 1.8 Partitioning the Disks:

Before we start, let's identify our disks by listing them with the command `lsblk`. While there are many

resources available on disk partitioning, I'll simplify it by outlining two common setups:

#### EFI GPT Partitioning:

- **/dev/sda1: ROOT (220GB) Linux Filesystem**

- **/dev/sda2: EFI (583MB) EFI System**

- **/dev/sda3: SWAP (3GB) Linux Swap**

#### BIOS MBR Partitioning:

- **/dev/sda1: ROOT (220GB) Linux**

- **/dev/sda2: BOOT (583MB) Linux**

- **/dev/sda3: SWAP (3GB) Linux Swap / Solaris**

Feel free to adjust the partition sizes based on your needs, although these sizes should suffic.

### 1.10 Prepare the Partitions:

- **ROOT:** Format the main partition where your system files will live. Let's give it a fresh coat of paint

with:

 `mkfs.ext4 /dev/sda1`

- **EFI/BOOT:** This partition is like the front door of your computer. Let's spruce it up with:

 `mkfs.vfat /dev/sda2`

- **SWAP:** Think of this as a little extra space for your computer to think. We'll set it up with:

 `mkswap /dev/sda3`

### 1.11 Mount the Partitions:

- **ROOT:** Now, let's hang up that main partition where our system will live. Use:

 `mount /dev/sda1 /mnt`

- **EFI/BOOT:** Time to set up the entrance! We'll make a spot for it and put up a sign with:

 `mount /dev/sda2 /mnt/boot --mkdir`

- **SWAP:** Finally, let's activate that extra thinking space with:

 `swapon /dev/sda3`

To make sure everything's in its place, give `lsblk` try!

### 2.1 Choose Mirrors:

For a list of mirrors, you can visit the [Mirrors page](https://repo-fastly.voidlinux.org/). We'll go with the Tier

1 globally available mirror: https://repo-fastly.voidlinux.org/

Add the following to the link:

- For glibc: `/current`

- For musl: `/current/musl`

Run the following commands:
1- `REPO=https://repo-fastly.voidlinux.org/current`
2- `ARCH=x86_64`

### 2.2 Install essential packages:

run the command:
`XBPS_ARCH=$ARCH xbps-install -S -R "$REPO" -r /mnt base-system linux-mainline nano` \

you don't have to use nano as your text editor (like Vim or neovim)

## 3.Configure the system:

### 3.1 Fstab:

find out the UUID of your partitions: \

`ROOT_UUID=$(blkid -s UUID -o value /dev/sda1)` \

`EFI_UUID=$(blkid -s UUID -o value /dev/sda2)` or `BOOT_UUID=$(blkid -s UU -o value /dev/sda2)` \

`SWAP_UUID=$(blkid -s UUID -o value /dev/sda3)`

write the fstab: \

`cat << EOF > /mnt/etc/fstab` \

`# /dev/sda1` \

`UUID=$ROOT_UUID / ext4 rw,relatime 0 1` \

`# /dev/sda2` \

`UUID=$EFI_UUID /boot vfat defaults,noatime 0 2` or `UUID=$BOOT_UUID /boot vfat defnoatime 0 2` \

`# /dev/sda3` \

`UUID=$SWAP_UUID none swap defaults 0 0`

### 3.2 Chroot:
to chroot into the newly setup envirenment run the commands: \
`for dir in dev proc sys run; do mount --rbind /$dir /mnt/$dir; mount --make-rslave /mnt/$dir; done` \
`cp /etc/resolve.conf /mnt/etc/` \
`PS1='(chroot) # ' chroot /mnt/ /bin/bash`

### 3.3 Time:

To view all available timezones, you can run the following command:

`ls /usr/share/zoneinfo`

This will give you a list of regions and cities representing different timezones.To set up your preferred

timezone, run the command:

`ln -sf /usr/share/zoneinfo/Region/City /etc/localtime`

Replace "Region" and "City" with the appropriate values for your timezone. For example, if you're in Algiers, you would run:

`ln -sf /usr/share/zoneinfo/Africa/Algiers /etc/localtime`

This will configure your system to use the selected timezone.

### 3.4 Localization:
Edit the `/etc/default/libc-locales` and uncomment your proper locales and then run the command to reconfigure 
`xbps-reconfigure -f glibc-locales`

### 3.5 Network configuration:
Create the hostname file:
`echo "<$HOSTNAME>" > /etc/hostname` 

Add the following lines to `/etc/hosts` 
`127.0.0.1        localhost` 
`::1              localhost` 
`127.0.1.1        <$HOSTNAME>.localdomain        <$HOSTNAME>`

### 3.7 Root password
Set the root password
`passwd`

### Other:
#### Add a user:
`useradd -mg users -G wheel -s /bin/bash <$USERNAME>`

#### Set the user password:
`passwd <$USERNAME>`

#### change the root shell
`chsh -s /bin/bash root`

#### give the user root privileges
uncomment the line `%wheel ALL=(ALL:ALL) ALL` from visudo, run the command: 
`EDITOR=nvim visudo`

#### Sync your repo:
`xbps-install -S` 

This step is not required unless you want to be able to access software that does not have free licenses or if you are a gamer or need 32bit packages 
`xbps-install void-repo-nonfree` 
`xbps-install -S` 
`xbps-install void-repo-multilib` 
`xbps-install -S`

#### NOTE: You can edit your fstab `/etc/fstab` if you made a typo mistake earlier.

### 3.8 Boot loader
We would be installing grub 
BIOS: 
`xbps-install -S grub` 
`grub-install --bootloader-id="Void" /dev/sda` 
NOTE: this command usuelly works, no other options should be passed to the command 
`grub-mkconfig -o /boot/grub/grub.cfg` 

UEFI: 
`xbps-install -S grub-x86_64-efi` 
`grub-install --efi-directory=/boot --bootloader-id="Void" /dev/sda` 
`grub-mkconfig -o /boot/grub/grub.cfg`

## 4.Finishing touches:
install any programs you want, 
if you are using wireless connection install NetworkManager 
`xbps-install -S NetworkManager` 
`ln -s /etc/sv/NetworkManager /var/service` 
`ln -s /etc/sv/dbus /var/service` 
#### NOTE: when using NetworkManager run it as sudo `sudo nmtui`


## 5.Reconfigure programs
run the following command to make sure all packages are configured correctly: 
`xbps-reconfigure -fa`

## 6.End
Exit Chroot: 
`exit`

Unmount Partitions: 
`swapoff /dev/sda3` 
`umount -R /mnt`

Reboot system: 
`reboot`

## BIG Note : This is the smaller version for beginners ir professionals from the hardest version of my friend @notpistooo ... [this is his repo link: ](https://github.com/notpistooo/Installing-Void-The-Arch-Way?tab=readme-ov-file#1pre-installation)
