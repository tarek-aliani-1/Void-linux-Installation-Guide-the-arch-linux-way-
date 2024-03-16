# Void-linux-Installation-Guide-the-arch-linux-way-
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

