# Folia 26.2 port notes

This is the first Folia 26.2 port of the supplied `playit-minecraft-plugin-0.2.0` source.

## Changes

- Targeted Folia API `26.2.build.+`.
- Targeted Java 25, matching current Folia/Paper 26.2 requirements.
- Added `folia-supported: true` to `plugin.yml`.
- Updated `api-version` to `26.2`.
- Replaced the legacy Bukkit scheduler call with Folia's `GlobalRegionScheduler`.
- Captured the local Minecraft listen address during plugin enable instead of reading `Bukkit.getIp()` / `Bukkit.getPort()` from the Playit worker thread.
- Reworked the Netty accept-pipeline reflection for modern Mojang-mapped `ServerConnectionListener` and the unversioned `org.bukkit.craftbukkit.CraftServer`.
- Removed dependence on old `v1_XX_RX` CraftBukkit package names.
- Relaxed discovery of the server listener's `ChannelFuture` list so it does not depend on generic-signature metadata.
- Added a fork version `0.2.0-folia.1`.
- Enabled TCP_NODELAY and keepalive on both sides of the TCP relay, with explicit connect timeouts and a bounded Netty worker pool.
- Added configurable UDP tunnels for raw UDP services such as Simple Voice Chat and Minecraft Bedrock/Geyser, including Playit UDP-channel authentication and packet forwarding.
- Added `/playit tunnel list` for displaying active additional tunnel addresses.

## Build

Requires Java 25 and an internet connection for Gradle/Maven dependencies:

```bash
./gradlew clean build
```

Expected output:

```text
build/libs/playit-minecraft-plugin-0.2.0-folia.1.jar
```

## Important

The build was not executed in this environment because the execution environment does not have JDK 25 or network access to download Gradle/dependencies. The source port was prepared and checked for remaining legacy scheduler/versioned-CraftBukkit references, but runtime compatibility with a live Folia 26.2 server still needs to be tested.
