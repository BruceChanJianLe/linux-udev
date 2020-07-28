# Linux Ubuntu udev

This is a guide to setting up udev to recognize a Logitech Wireless Gamepad F710. This can be generalized to be used for devices.

## Example 1: Logitech Wireless Gamepad F710
Below shows a picture of Logitech Wireless Gamepad F710.
![img](logitech-gamepad-f710.jpg)

**Step 1:**  
Open a terminal and check if `99-usb-serial.rules` exist under `/etc/udev/rules.d/`
```bash
ls /etc/udev/rules.d/
```

**Step 2:**  
Check for the device we are looking for

**_Method 1_**
```bash
dmseg
```

**_Method 2_**
```bash
# To know which /dev/input/XX, you should do a `ls /dev/input/`
udevadm info -a -n /dev/input/js0
```

You should look for the below attributes.  
-KERNEL  
-SUBSYSTEMS  
-ATTRS{idVendor}  
-ATTRS{idProduct}  
-ATTRS{product}  

**Step 3:**  
Edit `99-usb-serial.rules` and add the below line
```
# F710 logitech wireless joystick - D mode
KERNEL=="js*", SUBSYSTEMS=="usb", ATTRS{idVendor}=="046d", ATTRS{idProduct}=="c219", ATTRS{product}=="Logitech Cordless RumblePad 2", SYMLINK+="input/joy_wireless"
```

**Step 4:**  
Finally, reboot or reload the rules
```bash
# Reload rules
sudo udevadm control --reload-rules
# Trigger rules
sudo udevadm trigger
```

**Step 5:**  
Check if the rules are really updated by listing out `/dev/input/`
```bash
ll /dev/input/
```
You should see something like this
```
lrwxrwxrwx   1 root root       3 Jun  5 13:44 joy_wireless -> js0
```

## Example 2: No Touch Screen

Say you have cracked you laptop or monitor touch screen ability and now it is faulty and you would like to disable it after logging in. You may follow the steps below to disable the touch screen feature. There are also several commands where you can run on terminal to disable the touch screen but it is not permanent as rebooting will take it alway, therefore, udev comes in to save the day.

For other methods besides udev please refer to the reference link below...(link2 and link3)

**Step 1**  
```bash
ls /usr/share/X11/xorg.conf.d
```

**Step 2**  
Edit 40-libinput.conf and add `Option "Ignore" "on"` before EndSection of touchscreen
```bash
Section "InputClass"
        Identifier "libinput touchscreen catchall"
        MatchIsTouchscreen "on"
        MatchDevicePath "/dev/input/event*"
        Driver "libinput"
        Option "Ignore" "on"
EndSection
```

**Step 3**  
Save, exit and restart your computer.

**Reference**  
[link1](https://unix.stackexchange.com/questions/127443/how-do-i-disable-the-touch-screen-on-my-laptop/129603#129603) [link2](https://www.youtube.com/watch?v=zlAB_nX27Bo) [link3](https://www.youtube.com/watch?v=7qZBiOsXI_s)
