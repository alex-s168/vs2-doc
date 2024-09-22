# Events
VS2 provides some useful events in `org.valkyrienskies.core.impl.hooks.VSEvents` and `org.valkyrienskies.mod.common.hooks.VSGameEvents`

## All events
`org.valkyrienskies.core.impl.hooks.VSEvents`:
| Name | Description | Arguments |
| - | - | - |
| `shipLoadEvent` | Will be called once a ship gets (chunk) loaded on the server | `ship: org.valkyrienskies.core.impl.game.ships.ShipObjectServer` |
| `shipLoadEventClient` | Will be called once a ship gets (chunk) loaded on the client | `ship: org.valkyrienskies.core.impl.game.ships.ShipObjectClient` |
| `tickEndEvent` | Will be called at the end of the server ticking a world | `world: org.valkyrienskies.core.impl.game.ships.ShipObjectServerWorld` |

`org.valkyrienskies.mod.common.hooks.VSGameEvents`:
| Name | Description | Arguments |
| - | - | - |
| `registriesCompleted` | Will be called once the mass & similar registries are filled with data | |
| `tagsAreLoaded` | Will be called after Minecraft tags are loaded | |
| `renderShip` | Will be called before a ship is rendered | `org.valkyrienskies.mod.common.hooks.VSGameEvents.ShipRenderEvent` |
| `postRenderShip` | Will be called after a ship is rendered | `org.valkyrienskies.mod.common.hooks.VSGameEvents.ShipRenderEvent` |
| `shipsStartRendering` | Will be called before all ships are rendered | `org.valkyrienskies.mod.common.hooks.VSGameEvents.ShipStartRenderEvent` |

## How to listen to events
There is a `on` method in all of those events.

## Example
Print the slug (name) of every ship in a world every tick:
```kotlin
VSEvents.tickEndEvent.on { e ->
  e.world.allShips.forEach { ship ->
    println(ship.slug)
  }
}
```
