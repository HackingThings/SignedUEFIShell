# SignedUEFIShell
During our research of the BootHole vulnerability last year, we tried to find as many signed bootloaders as we could. We searched all across the internet and we found these bootloaders were part of rescue CDs, firmware update tools, drive encryption utilities and more. One of these was a bootable usb image that was part of the Seagate utility suite called “SeaChest”.

From http://support.seagate.com/seachest/SeaChest_Combo_UserGuides.html)
“SeaChest is a comprehensive, easy-to-use command line diagnostic tool that helps you quickly determine the health and status of your Seagate storage product. It includes several tests that will examine the physical media on your Seagate, Samsung or Maxtor disk drive.“ 

This particular bootloader has been added to the revoked bootloader list by Microsoft as a response to last year's BootHole vulnerability, meaning that any computer with the latest DBX updates in their UEFI Firmware will not be able to run this tool.
Caveat: In most platforms, restoring factory default settings for Secure Boot will revert back to a previous version of DBX.

Within the bootable image included within this set of tool there are UEFI Shell binaries, these binaries are signed by Seagate and are loaded by this now revoked bootloader, which essentially means that because Secure Boot is still on while a UEFI Shell is running, only SeaGate signed binaries can run.

However, since a UEFI shell is a command line interface that presents the user with a shell to manually type and run commands and scripts like simple commands that include the basic change directory, list directory, copy, move and delete files. And automatic script execution using a similar mechanism to batch files in Windows (Instead of .bat it uses .nsh, more in the specification).
Some of these built in commands allow reading and writing from memory, which can be useful in several ways.

In an excellent talk by Alex Ionesu at Syscan 2012, he describes how the ACPI Specification has a definition for a Windows Platform Binary Table (WPBT) which Microsoft describes: “The primary purpose of WPBT is to allow critical software to persist even when the operating system has changed or been reinstalled in a “clean” configuration.”

And so, as an experiment we will use the built in memory reading/writing utility in the UEFI Shell to overwrite an existing table with our own WPBT and load a binary to memory allowing for Windows to automatically download and execute it for us.
(For simplicity's sake, we will avoid adding a new table to the existing ones, we will just overwrite the DBG2 table which happens to be the exact size we need for a basic WPBT entry.)

Before we begin, a big caveat here is that the binary the WPBT points to has to be signed with a valid code signing certificate, so for this proof-of-concept we’ll just place a file in memory and see if it gets saved to disk by Windows, since Windows will not run it but it will save it ¯\_(ツ)_/¯

The python script we have published alongside this post will help you do what we just described by building an .nsh script file for you.
This script uses the UEFI Shell “mm” command for replacing the content of an ACPI table it is pointed at. 

Hint: you can use the memmap command in the UEFI shell to get the ACPI location in memory along with other mapped locations you might place data that will persist when windows boot (post ExitBootServices).



References:
https://web.archive.org/web/20180101001804/https://infocon.hackingand.coffee/SyScan/SyScan%202012%20Singapore/SyScan%202012%20Singapore%20presentations/Day2-6Alex%20Ionescu/AlexSyScan12.pdf
https://web.archive.org/web/20210309140158/https://download.microsoft.com/download/8/A/2/8A2FB72D-9B96-4E2D-A559-4A27CF905A80/windows-platform-binary-table.docx
https://web.archive.org/web/20210310034802/http://www.uefi.org/sites/default/files/resources/UEFI_Shell_2_2.pdf
https://web.archive.org/web/20200807013341/https://www.seagate.com/support/kb/using-seachest-bootable-to-blockerase-ssd/
https://web.archive.org/web/20201202151645/http://support.seagate.com/seachest/SeaChestUtilities.zip
https://web.archive.org/web/20210221001814/https://github.com/Jamesits/dropWPBT
https://web.archive.org/web/20210319021620/http://support.seagate.com/seachest/SeaChest_Combo_UserGuides.html


