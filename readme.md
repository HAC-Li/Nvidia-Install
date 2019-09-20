# kde/NVIDIA 系统安装



#### Disable nouveau

```
echo -e "blacklist nouveau\noptions nouveau modeset=0\nalias nouveau off" > /etc/modprobe.d/blacklist-nouveau.conf
update-initramfs -u && reboot
```

#### Install nvidia driver 

`apt-get install  nvidia-driver nvidia-xconfig`

#### we have to find bus id of our nvidia card

```
nvidia-xconfig --query-gpu-info | grep 'BusID : ' | cut -d ' ' -f6
```

#### generate **/etc/X11/xorg.conf** file 

```
Section "ServerLayout"
    Identifier "layout"
    Screen 0 "nvidia"
    Inactive "intel"
EndSection

Section "Device"
    Identifier "nvidia"
    Driver "nvidia"
    BusID "PCI:10:0:0"
EndSection

Section "Screen"
    Identifier "nvidia"
    Device "nvidia"
    Option "AllowEmptyInitialConfiguration"
EndSection

Section "Device"
    Identifier "intel"
    Driver "modesetting"
EndSection

Section "Screen"
    Identifier "intel"
    Device "intel"
EndSection
```

#### SDDM

```
# nano /usr/share/sddm/scripts/Xsetup

xrandr --setprovideroutputsource modesetting NVIDIA-0
xrandr --auto
```



#### Instal CUDA 

```
apt-get install ocl-icd-libopencl1 nvidia-cuda-toolkit
```





### Verify if PRIME is disabled

```
xrandr --verbose|grep PRIME
```

it should output something like this:
PRIME Synchronization: 0 
PRIME Synchronization: 1

#### Edit **/etc/default/grub** 

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet nvidia-drm.modeset=1"
```

#### Update grub

`update-grub`

#### Verify if PRIME is enabled

`xrandr --verbose|grep PRIME`

Now it should output:

PRIME Synchronization: 1 
PRIME Synchronization: 1
