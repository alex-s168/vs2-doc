# Dimension ID
Dimension IDs are a VS2 specific feature.

Dimension IDs are in the format `minecraft:dimension:[mod]:[dimension name]`.
To convert this to a `ResourceKey<Level>` (which is how Minecraft stores level IDs), you can do this:
```kotlin
/* DimensionId in VS2 is an type alias for String */

fun DimensionId.toDimensionKey() {
  val it = this.split(":")
  val rl = ResourceLocation(it[it.size - 2], it[it.size - 1])
  return ResourceKey.create(Registry.DIMENSION_REGISTRY, rl)
}
```
This can now be used to for example get the `ServerLevel` of a ship when given a `MinecraftServer` object using the `getLevel` method in `MinecraftServer`.
