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

Usually the 1:0:0 bus is correct. If this is not your case(you can find out through lspci or bbswitch output mesages) you can create a conf script for example nano /etc/X11/nvidia-xorg.conf.d/30-nvidia.conf to set the proper bus id:

Section "Device"
    Identifier "nvidia"
    Driver "nvidia"
    BusID "PCI:2:0:0"
EndSection

You can use this command to get the bus id:

lspci | grep -i nvidia | awk '{print $1}'

Note that this prints your bus id in hexadecimal, but the Xorg configuration script requires that you provide it in decimal, so you'll need to covert it. You can do this with bash:

# In this example, my bus id is "3c"
bash -c "echo $(( 16#3c ))"

Also this way you can adjust some nvidia settings if you encounter issues:

Section "Screen"
    Identifier "nvidia"
    Device "nvidia"
    #  Option "AllowEmptyInitialConfiguration" "Yes"
    #  Option "UseDisplayDevice" "none"
EndSection

In order to make power management features work properly, you need to make sure that bus ids in /etc/default/nvidia-xrun are correctly set for both the NVIDIA graphic card and the PCI express controller that hosts it. You should be able to find both the ids in the output of lshw: the PCIe controller is usually displayed right before the graphic card.
Automatically run window manager

For convenience you can create nano ~/.config/X11/nvidia-xinitrc and put there your favourite window manager:

if [ $# -gt 0 ]; then
    $*
else
    openbox-session
#   startkde
fi

With this you do not need to specify the app and you can simply run:

nvidia-xrun

