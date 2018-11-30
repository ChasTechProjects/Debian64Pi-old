# Debian64Pi
64-bit Debian Stretch for the Raspberry Pi 3

This is a 64-bit Debian Stretch image that works on the Raspberry Pi 3. It is a minimal image built upon the Debian ARM64 base but with a kernel and firmware that works on the Raspberry Pi 3.

## Status
*Keyboard, mouse, and Ethernet are working.

*XFCE desktop loaded successfully and can load applications such as Chromium and LibreOffice.

*Multiarch in apt works -- can add armhf as an alternative architecture and load armhf programs.

*Snaps can be installed after installing snapd.

*Pi 3's built in WiFi currently not working, despite kernel modules including drivers for it were installed.

## Desktop Environment
Image does not include a desktop environment, but one can be installed with one of the following commands:

<code>sudo apt install xfce4</code>

<code>sudo apt install lxde</code>

<code>sudo apt install kde-plasma-desktop</code>

<code>sudo apt install lxqt</code>
 
<code>sudo apt install mate-desktop</code>

## Pre-release image

You can download a pre-release image from the releases section of this repository. The SHA256 hash on the release description is the hash of the archive, NOT the image file itself.
