# Raytrace AntiXray SDK

## Overview

This is a SDK to integrate raytracing anti-xray into your Spigot/Paper fork, with few lines of modifications, you can majorly improve the functionality and reduce the performance impact of Raytrace AntiXray when using it with your fork.

## Forks with SDK intrgrated
- [1.8.8 ImanitySpigot3](https://builtbybit.com/resources/imanityspigot3-regular.10770/)
- [1.21.4 Leaf](https://github.com/Winds-Studio/Leaf/)
- [1.21.x UniverseSpigot](https://discord.universespigot.com/)
- [1.12.2 HybridFix (Mod brings SDK integration into hybrid servers)](https://github.com/HaHaWTH/HybridFix/)
- [1.8.8 AzuriteSpigot](https://azuritedev.net/store/azuritespigot.4/)

## Why should you care? (as a developer of a Spigot/Paper fork)

Raytrace AntiXray is currently the #4 Trending Minecraft plugin on [BuiltByBit](https://builtbybit.com/resources/raytraceantixray-ores-entities-tiles.41896/) with over 400 sales all platforms combined.
We hope to improve the experience of using Raytrace AntiXray as much as possible, but due to the nature of the plugin, there is always a bottleneck we can't improve without direct modifications to the Spigot/Paper source code.
And that's where you come in!
If you're a developer of a Spigot/Paper fork, integrating this SDK not only reduces the performance impact of Raytrace AntiXray when using it with your fork, we'll also feature your fork in our recommendations!

## How to integrate

Repository
* Maven
```
<repository>
      <id>imanity-repo</id>
    <url>https://maven.imanity.dev/repository/imanity-libraries/</url>
</repository>
```        
* Gradle
```
maven {
    url = "https://maven.imanity.dev/repository/imanity-libraries/"
}
```

Dependency
* Maven
```
<dependency>
    <groupId>dev.imanity.antixray</groupId>
    <artifactId>antixray-sdk</artifactId>
    <version>1.0.3</version>
</dependency>
```
* Gradle
```
implementation 'dev.imanity.antixray:antixray-sdk:1.0.3'
```

## How to use

### Legacy (pre-1.17 where Mojang mapping isn't being used)

in `net.minecraft.server.World.setTypeAndData`, we need to add the following code right under Chunk.a(blockposition, iblockdata):
```java
net.minecraft.server.block.IBlockData iblockdata1 = chunk.a(blockposition, iblockdata);
// Imanity start - AntiXraySDK integration
AntiXrayAdapter adapter = AntiXraySDK.getAdapter();
if (adapter != null) {
    adapter.callBlockChange(world, blockposition.getX(), blockposition.getY(), blockposition.getZ(), CraftMagicNumbers.getMaterial(iblockdata.getBlock()));
}
// Imanity end
```

in `net.minecraft.server.PlayerInteractManager.a(BlockPosition, EnumDirection)`, we need to add the following code at the end of the method:
```java
// Imanity start - AntiXraySDK integration
AntiXrayAdapter adapter = AntiXraySDK.getAdapter();
if (adapter != null) {
    adapter.callPlayerLeftClickBlock(player.world.getWorld(), player.getBukkitEntity(), pos.getX(), pos.getY(), pos.getZ());
}
// Imanity end
```
### Modern (1.17+ where Mojang mapping is being used)

in `net.minecraft.world.level.Level.setBlock`, we need to add the following code right under Chunk.setBlockState(pos, state):

```java
net.minecraft.world.level.block.state.BlockState blockstate = chunk.setBlockState(pos, state, (flags & 64) != 0, (flags & 1024) != 0);
// Imanity start - AntiXraySDK integration
AntiXrayAdapter adapter = AntiXraySDK.getAdapter();
if (adapter != null) {
    adapter.callBlockChange(world.getWorld(), pos.getX(), pos.getY(), pos.getZ(), state.getBukkitMaterial());
}
// Imanity end
```

in `net.minecraft.server.level.ServerPlayerGameMode.handleBlockBreakAction(pos, action, direction, worldHeight, sequence)` we need to add the following code at the end of the method:
```java
// Imanity start - AntiXraySDK integration
AntiXrayAdapter adapter = AntiXraySDK.getAdapter();
if (adapter != null) {
    adapter.callPlayerRightClickBlock(player.world.getWorld(), player.getBukkitEntity(), pos.getX(), pos.getY(), pos.getZ());
}
// Imanity end
```

---

You may notice a pattern here, we are adding the methods that in Modern version ChunkPacketBlockController is already calling, and you will be correct. but these things will never be available in Legacy version, so we need to do it manually.
For modern version yes, we may consider to adopt ChunkPacketBlockController in the future, but for now, we will keep it as is.

## Contact

If you have any questions, feel free to contact us on our [Discord server](https://go.imanity.dev/discord).

If you wish to have your fork featured in our recommendations, feel free to open a pull request or contact us on Discord!
