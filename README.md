# Debian64Pi
64-bit Debian Stretch for the Raspberry Pi 3

These images are various flavours of Debian Stretch that run on the Pi. These are built upon the Debian ARM64 base and are not based on Raspbian. These images will work on the Pi 3B, 3B+, and revision 1.2 of the Pi 2.

## Status
*Keyboard, mouse, Wi-Fi, and Ethernet are working.

*MATE desktop loaded successfully and can load applications such as Chromium and LibreOffice.

*Multiarch in dpkg works -- can add armhf as a secondary architecture and load armhf programs.

*Snaps can be installed after installing snapd.

## Desktop Environment
Currently, an image with the MATE desktop is available from the Releases section of this page. I plan on releasing images pre-loaded with the following desktop environments in the future:

*LXDE

*LXQT

*KDE Plasma

*XFCE 

## Troubleshooting

### My Pi hangs on the rainbow screen.

This is the issue you face when you are using an unsupported Pi model. Your Pi model MUST have the BCM2837 SoC for this image to work. 
