# Ship Chunk Claims
Every ship has a chunk claim.
This chunk claim specifies where the shipyard of the ship is exactly.
Currenlty chunk claims are hardcoded to 256 by 256 chunks.
This means that ships cannot be bigger than 256x256 chunks.

## `chunkClaim` field in `Ship`
The function `fun getTotalVoxelRegion(yRange: LevelYRange, destination: AABBi = AABBi()): AABBi` in `ChunkClaim` can be used to get the AABB of the chunk claim (= Shipyard area)
It takes an argument of the type `LevelYRange` which can be easily created from a level:
```kotlin
LevelYRange(level.minBuildHeight, level.maxBuildHeight)
```
This creates a Y-Range of the build height in the level.

## `chunkClaimDimension` field in `Ship`
The dimension ID of the chunk claim.

The ship always has to be in the same dimension as the chunk claim, so this is also the dimension ID of the ship.

You can find more information about dimension IDs [here](dimension_id.md)
