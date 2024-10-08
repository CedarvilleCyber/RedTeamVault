This is likely the most effective and essential webapp-pentesting tool. For a close competitor see OWASP Zap. 

# Setup Procedure
Burp Suite itself can be installed without trouble using the Windows .msi or a Kali installation candidate in `apt`. 

The difficulty comes in enabling core features. 

> [!note]
> You can bypass a large amount of this trouble by using the built-in Chromium browser that now ships with Burp Suite. In a quick-and-dirty environment, this is my recommendation.

The steps are as follows:
- Burp Suite Installation
- FoxyProxy Installation
- Certificates

## Burp Suite Installation
- Kali
	- The `apt` repository contains a version of Burp Suite Community. 
```shell
apt install burpsuite
```
- Windows has an installation MSI available on the Burp Suite website. 

https://portswigger.net/burp/communitydownload
## FoxyProxy Installation
FoxyProxy is available from the Firefox Add-Ons store, and potentially the Chrome extensions store as well for free. I recommend using Firefox for the ease of the following section.

## Certificates
In order to proxy HTTPS connections, you must install the Burp Suite CA into your browser. Firefox is a simpler install, but Chrome makes it harder, as you have to add the certificates to Windows, not to Chrome. This information is potentially out of date.  

TODO: Add more information above

# Proxy
The Burp Proxy stands between you and your target. Using this you can capture web requests before they are relayed to your target. From here you an send them to other tools, or manipulate them directly. The "Intercept is on" button tells you that traffic is going to be **stopped** at your proxy. This means that no traffic in scope will pass from your browser to the site you are accessing without allowing the traffic from the proxy page. Use this information to help you figure out why Gmail isn't loading at a weird time...

# Repeater
Burp Repeater is a tool built into Burp suite to record a request and send it many times. You can quickly prototype exploit requests this way, by manipulating a request, sending it, reading the results, modifying the *same request* and then sending it again. Quite useful for injection attacks, etc where you have to tweak syntaxes to get it just right. Right-click request -> Send to Repeater, Action -> Send to Repeater, or Ctrl-R .

# Intruder
Burp Intruder is a bruteforcing tool. You can bruteforce different parameters passed to the web application in Sniper, Cluster Bomb, Pitchfork, and Battering Ram modes. Right-click -> Send to Intruder, Action -> Send to Intruder, or Ctrl-I.
- Sniper
- Cluster Bomb
- Pitchfork
- Battering Ram
TODO: Add details about the different attacks and use cases. 
