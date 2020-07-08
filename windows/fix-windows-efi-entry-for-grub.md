GPT-partitioned system disk - installing a Windows UEFI bootloader

Since the Linux installer has set up a UEFI bootloader for us, there should also be an EFI system partition (ESP for short) on the disk. In the fdisk -l output, it is the partition that has its Type listed as EFI System and its size would typically be something in between 100M and 512M. In Linux, it might be mounted at /boot/efi. You might want to run sudo ls -l /boot/efi or take a peek at it with some GUI file manager so that you'll recognize the contents of this partition - you'll be seeing it at Windows command prompt for a bit. Typically, if ESP is mounted at /boot/efi, there should be a sub-directory like /boot/efi/EFI/ubuntu that contains the actual UEFI bootloader of Ubuntu.

Now, get yourself a Windows 10 installation media. You won't need the license code. Boot your computer from that installation media. Since your Ubuntu installed in UEFI mode, your computer will probably boot the Windows installer in UEFI mode automatically too, but in case you see two boot options for the installation media, only one of those options boots it in UEFI mode, and that's the one you should pick. (Hopefully the boot menu will say which is which - it depends on your system's UEFI firmware.)

When the Windows installer displays the initial screen with an "Install now" button in the middle of the screen, select instead "Repair your computer" near the bottom.

From the next menu, choose "Troubleshoot" and then "Command Prompt".

Now, type in these commands:

diskpart
list vol

This should display a list of partitions. Find the partition that has FAT32 in the "Fs" column - it should be your ESP you saw in Linux before. Note its volume number (Volume ### in the leftmost column) and assign an unused drive letter (like X:) for it. For example, if ESP is listed as Volume 2:

sel vol 2
assign letter=x:

It should say DiskPart successfully assigned the drive letter or mount point. at this time. Then type exitto quit the DiskPart utility.

Switch to the EFI directory on the ESP:

cd /d x:\EFI
dir

If you see the ubuntu directory, you're in the right place.

Now create a directory or two for the Windows UEFI bootloader:

mkdir Microsoft
cd Microsoft
mkdir Boot
cd Boot

Install the Windows UEFI bootloader to the ESP and re-create the Windows BCD registry:

bcdboot c:\Windows /l en-us /s x: /f UEFI /addlast

If you want the Windows bootloader to use a language other than English, replace en-us in the command above with the appropriate Windows language code.

Now type exit, remove the Windows installation media and reboot your system. It should come up in Ubuntu just as before. Run sudo update-grub to update the GRUB boot menu. If all goes well, it should now auto-detect the presence of UEFI Windows bootloader and add it to the GRUB boot menu.

Also now in the BIOS boot order menu, there should be a item named "Windows Boot Manager". If you want to remove Ubuntu and go back to Windows-only system, just switch this one as the primary boot option, and the system should skip GRUB and boot into Windows by default. Then you can remove the Linux partitions using Windows Disk Management. Do not remove the EFI System partition, as now Windows also needs it for booting.
