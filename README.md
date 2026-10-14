# Playit.gg Minecraft Plugin

This is the official Minecraft Java Plugin for https://playit.gg. Download the latest version [here](https://github.com/playit-cloud/playit-minecraft-plugin/releases/latest/download/playit-minecraft-plugin.jar).

Not sure how to use the playit plugin? Watch our [YouTube video](https://youtu.be/QQYRdgBL-4o).

## Compatibility Matrix
Contributions welcome!

❌ = not planned

🚧 = planned

✔️ = implemented

### Server Types
| Server Type  | Playit works | Real IP works | Plugin Download                                                                                                                         |
|--------------| ------------ |---------------|-----------------------------------------------------------------------------------------------------------------------------------|
| [Spigot 1.19](https://getbukkit.org/download/spigot)  | ✔️ | ✔️ | [0.1.4](https://github.com/playit-cloud/playit-minecraft-plugin/releases/download/v0.1.4/playit-minecraft-plugin.jar) |
| [Spigot 1.16.5](https://getbukkit.org/download/spigot) | ✔️ | ✔️ | [0.1.4-mc1.16](https://github.com/playit-cloud/playit-minecraft-plugin/releases/download/v0.1.4/playit-minecraft-plugin-1.16.jar) |
| [Paper 1.19](https://papermc.io/)   | ✔️ | ✔️ | [0.1.4](https://github.com/playit-cloud/playit-minecraft-plugin/releases/download/v0.1.4/playit-minecraft-plugin.jar) |
| [Paper 1.8 - 1.18](https://papermc.io/legacy) | ✔️ | ❌ | [0.1.4](https://github.com/playit-cloud/playit-minecraft-plugin/releases/download/v0.1.4/playit-minecraft-plugin.jar) & [0.1.4-mc1.16](https://github.com/playit-cloud/playit-minecraft-plugin/releases/download/v0.1.4/playit-minecraft-plugin-1.16.jar)|
| [Magma 1.18](https://magmafoundation.org/) | ✔️ | ❌ | [0.1.4](https://github.com/playit-cloud/playit-minecraft-plugin/releases/download/v0.1.4/playit-minecraft-plugin.jar)
| [BungeeCord](https://www.spigotmc.org/wiki/bungeecord/) | 🚧 |   |


### Plugins

| Plugin | Playit works |
| ------ | ------------ |
| [Geysermc](https://geysermc.org/) | 🚧 |
| [Dynmap](https://www.spigotmc.org/resources/dynmap%C2%AE.274/) | 🚧 |
| [Nova Modding Framwork](https://www.spigotmc.org/resources/nova-modding-framework-1-19-1-1-19-2.93648/) | ❌ |



## Developer

### Building the plugin

Requires Java 17+ and [Gradle](https://gradle.org/).

```bash
./gradlew build
```

The Minecraft plugin jar is written to `build/libs/playit-minecraft-plugin-$VERSION.jar` (e.g. `playit-minecraft-plugin-0.2.0.jar`). This fat jar includes all dependencies and is ready to drop into your server's `plugins` folder. Only this JAR is produced; use it for deployment.

### Other links
* https://www.spigotmc.org/resources/playit-gg.105566/


## Folia 26.2 fork

This fork targets Folia 26.2 and uses the Folia global scheduler for server-wide startup work.
It marks the plugin as `folia-supported: true` and removes the old versioned CraftBukkit reflection paths.

Build with Java 25 and Gradle 9.1+:

```bash
./gradlew clean build
```

The resulting plugin is `build/libs/playit-minecraft-plugin-0.2.0-folia.1.jar`.

### Lower-latency TCP relay

The relay enables `TCP_NODELAY` for both the Playit-to-plugin and plugin-to-Minecraft sockets. This avoids delaying small game packets while waiting for Nagle's algorithm to batch them. The relay uses up to four Netty I/O threads to avoid starting an unnecessarily large worker pool on high-core hosts. The final ping still depends on the Playit route and the player's network.

### Additional UDP tunnels

Add entries under `additional-tunnels` in `plugins/playit-gg/config.yml`. The plugin creates the matching Playit tunnel and forwards its UDP traffic to the configured local address.

```yaml
additional-tunnels:
  - name: Simple Voice Chat
    type: udp
    local-ip: 127.0.0.1
    local-port: 24454
    description: Simple Voice Chat
    region: global
  - name: Geyser
    type: minecraft-bedrock
    local-ip: 127.0.0.1
    local-port: 19132
    region: global
```

Use the UDP address shown by `/playit tunnel list` in the Simple Voice Chat configuration, and make sure Geyser is listening on the configured UDP port. `type: udp` creates a one-port raw UDP tunnel; `type: minecraft-bedrock` creates a Playit Bedrock tunnel. Supported region values are the Playit region names, such as `global`, `europe`, and `poland`. A tunnel only appears in the list after Playit reports it active.

> The Netty pipeline injector still relies on reflection because Playit needs to add a decoder to
> Minecraft's inbound connection pipeline. The injector is written against modern Mojang-mapped
> `ServerConnectionListener`/`CraftServer` names and deliberately avoids old `v1_XX_RX` package names.
