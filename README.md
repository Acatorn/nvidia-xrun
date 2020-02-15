# nvidia-xrun
Init independent fork of awesome https://github.com/Witko/nvidia-xrun. Uses bbswitch to turn GPU on and off.

These utility scripts aim to make the life easier for nvidia cards users. It started with a revelation that bumblebee in current state offers very poor performance. This solution offers a bit more complicated procedure but offers a full GPU utilization(in terms of linux drivers)
## Usage:

    switch to free tty
    login
    run nvidia-xrun [app]
    enjoy

Currently sudo is required as the script needs to wake up GPU, modprobe the nvidia driver and perform cleanup afterwards. For this we use bbswitch.
## Structure

* **nvidia-xrun** - uses following dir structure:
* **/usr/bin/nvidia-xrun** - the executable script
* **/etc/X11/nvidia-xorg.conf** - the main X confing file
* **/etc/X11/xinit/nvidia-xinitrc** - xinitrc config file. Contains the setting of provider output source
* **/etc/X11/xinit/nvidia-xinitrc.d** - custom xinitrc scripts directory
* **/etc/X11/nvidia-xorg.conf.d** - custom X config directory
* **/etc/default/nvidia-xrun** - nvidia-xrun config files (contains values for variables used in /usr/bin/nvidia-xrun)
* **/etc/modules-load.d/bbswitch.conf** - loads bbswitch module on boot
* **/etc/modprobe.d/bbswitch.conf** - disables the nvidia module on boot
* **/etc/modprobe.d/nvidia-xrun-blacklist.conf** - prevents any nvidia* and nouveau modules to load on boot 
* **[OPTIONAL] ~/.config/X11/nvidia-xinitrc** user-level custom xinit script file. You can put here your favourite window manager for example

## Dependencies
mesa
bbswitch
xinit
xorg-server
nvidia (proprietary drivers)
xrandr

## Installation
### Manual
1. Make sure you have above dependencies installed on your system
2. Clone this repository
3. Place files from:
* bin to /usr/bin on your system
* X11 to /etc/X11 on your system 
* default to /etc/default on your system
* modules-load.d to /etc/modules-load.d on your system
* modprobe.d to /etc/modprobe.d on your system
4. Set the right bus id (see **Setting the right bus id** section below)
5. Reboot

## Setting the right bus id

Usually the 1:0:0 bus is correct. If this is not your case(you can find out through lspci or bbswitch output mesages) you can create a conf script for example nano /etc/X11/nvidia-xorg.conf.d/30-nvidia.conf to set the proper bus id:

    Section "Device"
        Identifier "nvidia"
        Driver "nvidia"
        BusID "PCI:2:0:0"
    EndSection

You can use this command to get the bus id:

lspci | grep -i nvidia | awk '{print $1}'

## Automatically run window manager

For convenience you can create nano ~/.config/X11/nvidia-xinitrc and put there your favourite window manager:

    if [ $# -gt 0 ]; then
        $*
    else
        openbox-session
    fi

With this you do not need to specify the app and you can simply run:

nvidia-xrun

## Troubleshooting
### Steam issues
Yes unfortunately running Steam directly with nvidia-xrun does not work well - I recommend to use some window manager like openbox.

### HiDPI issue
When using openbox on a HiDPI (i.e. 4k) display, everything could be so small that is difficult to read.
To fix, you can change the DPI settings in `~/.Xresources (~/.Xdefaults)` file by adding/changing `Xft.dpi` setting. For example :

```
Xft.dpi: 192
```

### Vulkan does not work
Check https://wiki.archlinux.org/index.php/Vulkan
* remove package vulkan-intel
* set VK_ICD_FILENAMES=/usr/share/vulkan/icd.d/nvidia_icd.json

