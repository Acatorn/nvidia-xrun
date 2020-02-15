# nvidia-xrun
Init independent fork of https://github.com/Witko/nvidia-xrun. Uses bbswitch to turn GPU on and off.

These utility scripts aim to make the life easier for nvidia cards users. It started with a revelation that bumblebee in current state offers very poor performance. This solution offers a bit more complicated procedure but offers a full GPU utilization(in terms of linux drivers)
Usage:

    switch to free tty
    login
    run nvidia-xrun [app]
    enjoy

Currently sudo is required as the script needs to wake up GPU, modprobe the nvidia driver and perform cleanup afterwards. For this we use bbswitch.
Structure

    nvidia-xrun - uses following dir structure:
    /usr/bin/nvidia-xrun - the executable script
    /etc/X11/nvidia-xorg.conf - the main X confing file
    /etc/X11/xinit/nvidia-xinitrc - xinitrc config file. Contains the setting of provider output source
    /etc/X11/xinit/nvidia-xinitrc.d - custom xinitrc scripts directory
    /etc/X11/nvidia-xorg.conf.d - custom X config directory
    /etc/default/nvidia-xrun - nvidia-xrun config files (see config folder in this repo, contains values for variables used in /usr/bin/nvidia-xrun)
    /etc/modprobe.d/bbswitch.conf
    /etc/modprobe.d/nvidia-xrun-blacklist.conf
    [OPTIONAL] ~/.nvidia-xinitrc - user-level custom xinit script file. You can put here your favourite window manager for example

Setting the right bus id

Usually the 1:0:0 bus is correct. If this is not your case(you can find out through lspci or bbswitch output mesages) you can create a conf script in /etc/X11/nvidia-xorg.conf.d to set the proper bus id

Section "Device"
    Identifier "nvidia"
    Driver "nvidia"
    BusID "PCI:2:0:0"
EndSection

Also this way you can adjust some nvidia settings if you encounter issues:

Section "Screen"
    Identifier "nvidia"
    Device "nvidia"
    #  Option "AllowEmptyInitialConfiguration" "Yes"
    #  Option "UseDisplayDevice" "none"
EndSection
