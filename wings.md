# Wings

## How to create a wing block
Create a class that extends Minecraft's `Block` class and implements `org.valkyrienskies.mod.common.block.WingBlock`.

The WingBlock interface:
```kotlin
package org.valkyrienskies.mod.common.block

import net.minecraft.core.BlockPos
import net.minecraft.world.level.Level
import net.minecraft.world.level.block.state.BlockState
import org.valkyrienskies.core.api.ships.Wing

interface WingBlock {
    fun getWing(level: Level?, pos: BlockPos?, blockState: BlockState): Wing?
}
```

The `getWing` method gets called every tick. This means that you can easily make dynamic wings.

The Wing class:
```kotlin
package org.valkyrienskies.core.api.ships

import org.joml.Vector3dc

data class Wing(
    val wingNormal: Vector3dc,
    val wingLiftPower: Double,
    val wingDragPower: Double,

    /**
    * The maximum force the wing can withstand. Null is infinite.
    */
    val wingBreakingForce: Double?,

    /**
    * Angle of airfoil bias in radians, typically zero for symmetric airfoils, and non-zero for cambered airfoils
    * Note that positive values will make lift in the direction of [wingNormal] at 0 angle of attack
    */
    val wingCamberAttackAngleBias: Double
)
```

## Flaps vs Wings
Flaps are identical to Wings but have a `wingCamberAttackAngleBias` of zero.

## Example Values
| | lift | drag | camber attach angle bias | breaking force |
| - | - | - | - | - |
| VS2 debug wing | 150 | 30 | 10° | inf |
| VS2 debug flap | 150 | 30 | 0° | inf |
| Clockwork wing | 150 | 150 | 10° | 10 |
| Clockwork flap | 150 | 150 | 0° | 10 |

## Non Block Aligned Wings
Sometimes you want scaled or rotated invisible wings on your ship.
For that you can use `org.valkyrienskies.core.api.ships.WingManager`.

`WingManager` is a attachment class, which means that you can get it with `ship.getAttachment<WingManager>()`.

A `WingManager` manages wing groups which consist of voxel grid aligned `Wing`s.
To get it to be any rotation, position and scale, use `WingManager#setWingGroupTransform`.

Example:
```kotlin
fun createWing(ship: Ship, wingStats: Wing, pos: Vector3dc, rot: Quaterniondc, scale: Vector3dc): WingGroupId {
    val wm = ship.getAttachment<WingManager>()

    val group = wm.createWingGroup()
    wm.setWing(group, 0, 0, 0, wingStats)

    val transform = Matrix4d().setTranslation(pos)
    transform.mul(Matrix4d().rotation(rot))
    transform.mul(Matrix4d().scaling(scale))
    wm.setWingGroupTransform(group, transform)

    return group
}
```

Don't forget to delete your wing group with `WingManager#deleteWingGroup`!
