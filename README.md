# G14-Setup
## [Keys](https://www.reddit.com/r/ZephyrusG14/comments/k69zjm/linux_new_daily_driver/gek0v8t)
Add below lines to `/etc/udev/hwdb.d/90-g14-mod.hwdb` to enable respective key functions
```
evdev:input:b0003v0B05p1866*
  KEYBOARD_KEY_c00b6=kbdillumdown # Fn+F2 (music prev)
  KEYBOARD_KEY_c00b5=kbdillumup   # Fn+F4 (music skip)
  KEYBOARD_KEY_ff3100c5=pagedown  # Fn+Down
  KEYBOARD_KEY_ff3100c4=pageup    # Fn+Up
  KEYBOARD_KEY_ff3100b2=home      # Fn+Left
  KEYBOARD_KEY_ff3100b3=end       # Fn+Right
```
Put above changes into effect
```
sudo systemd-hwdb update
sudo udevadm trigger
```
Edit `/usr/share/pulseaudio/alsa-mixer/paths/analog-output.conf.common`
Add following just above `[Element PCM]` section to enable mute button and better volume control
```
[Element Master]
switch = mute
volume = ignore
```
## Time
`timedatectl set-local-rtc 1 --adjust-system-clock`
## [Update to kernel 5.10.0](https://pop-planet.info/forums/threads/kernels-installing-and-removing.758/)
```
sudo add-apt-repository -y ppa:cappelikan/ppa
sudo apt update
sudo apt install -y mainline
```
Go to "All Software" by pressing `super + A`, you will see the "Ubuntu Mainline Kernel Installer".
Install 5.10.0 from the list
Restart
## Asus dkms packages - Controlling battery charge limit, cpu turbo, gpu profile and fan curves
```
echo "deb https://download.opensuse.org/repositories/home:/luke_nukem:/asus/xUbuntu_20.10/ /" | sudo tee /etc/apt/sources.list.d/asus.list
wget -q -O - https://download.opensuse.org/repositories/home:/luke_nukem:/asus/xUbuntu_20.10/Release.key | sudo apt-key add -
sudo apt-get update
sudo apt-get install dkms-hid-asus-rog dkms-asus-rog-nb-wmi asus-nb-ctrl
sudo apt install acpi-call-dkms
sudo modprobe acpi_call
systemctl daemon-reload && systemctl restart asusd
systemctl --user enable asus-notify.service
systemctl --user start asus-notify.service
```
Add following lines to `/etc/modules-load.d/acpi_call.conf`
```
acpi_call
```
Add following lines to `/etc/modules-load.d/asus.conf`
```
hid_asus
```
Restart
Disable system76-power gnome extension and service if you want to manage graphics using the dkms packages. (Recommended)
```
systemctl disable system76-power.service
```
### asusctl
Use `asusctl` command in the terminal to configure battery charge limit, cpu turbo, gpu profile and fan curves. Use the command `asusctl -h` to get help on the command. To get help on a specific option use `asusctl <option> -h`. Making changes here writes the values to the config file `/etc/asusd/asusd.conf`.
You can also use the config file I have uploaded in this repo.

Bind the `Fn+F5` key to `asusctl profile -n` in the custom keyboard shortcuts section in Pop OS settings.
#### Fan Curve
In order to set a fan curve, you must have exactly 8 pairs, and also must not specify a temperature lower than 30c, and not higher than 109c. Each pair has the temperature and fan speed separated by a colon, and each pair is separated by commas. Example `"30c:0%,40c:0%,50c:20%,60c:20%,70c:40%,80c:60%,90c:80%,100c:80%"`
### Issues
Run `sudo asusd` in terminal. This tells you about the status of the config you have set using `asusctl`.
If you get errors, try installing the dkms modules from [asus-rog-nb-wmi](https://gitlab.com/asus-linux/asus-rog-nb-wmi) and [hid-asus-rog](https://gitlab.com/asus-linux/hid-asus-rog) and restart the service using `systemctl daemon-reload && systemctl restart asusd`
### Last resort
If you still get issues on running `sudo asusd`, search in the [Discord server](https://discord.gg/ngbdKabAnP) or google
## Gnome Tweak Tool
```
sudo apt install gnome-tweaks
```
Place your gnome extensions in `~/.local/share/gnome-shell/extensions` under same folder name as the `uid` in `metadata.json` of the extension
### Recommended Extensions
- [Power Indicator](https://extensions.gnome.org/extension/1501/power-indicator/)
- [Dash to Dock](https://extensions.gnome.org/extension/307/dash-to-dock/)

You can see the gnome shell version in the Tweaks app
