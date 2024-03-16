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

you don't have to use nano as your text editor, (likevim or neovim)

