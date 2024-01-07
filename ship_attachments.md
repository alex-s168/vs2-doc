Information provided here is not guaranteed to be up-to-date.

# Ship Attachments
Ship attachments are used to store data in a ship.

VS2 currently uses Jackson to serialize and deserialize ship attachments.

The attached class will be stored in the ship in the world.
This means that when the ship is loaded, your class will be deserialized.

Ship attachments need to be classes with a default constructor without arguments.

## Saving, loading and removing
in the package `org.valkyrienskies.core.api.ships`
there are two extension functions for saving and loading attachments:
```kotlin
import org.valkyrienskies.core.api.ships.ServerShip
import org.jetbrains.annotations.ApiStatus.Experimental

@Experimental
public inline fun <reified T> ServerShip.getAttachment(): T? { /* ... */ }

@Experimental
public inline fun <reified T> ServerShip.saveAttachment(value: T?) { /* ... */ }
```
So to save an attachment you can do:
```kotlin
serverShip.saveAttachment(MyAttachmentClass())
```
and to load it:
```kotlin
val attachment = serverShip.getAttachment<MyAttachmentClass>()
```
Note that the attachment class needs to have a default constructor without arguments, 
you can however use a constructor with arguments for `saveAttachment`,
but you still need a default constructor without arguments.

To remove an attachment you can do:
```kotlin
serverShip.saveAttachment<MyAttachmentClass>(null)
```

## Attachment class fields
Every class you use as an attachment needs to follow these rules:

- It should have the `com.fasterxml.jackson.annotation.JsonAutoDetect` annotation with the correct settings
- All fields that you don't want to be saved in the attachment need to be marked 
with `@com.fasterxml.jackson.annotation.JsonIgnore`. This includes ships because otherwise they would recursively save themself.
- All types of the fields that are not marked with
`@com.fasterxml.jackson.annotation.JsonIgnore` need to be classes with a default 
constructor without arguments. This means that you can't use interfaces as types of these fields

## World "corruption"
If you change anything related to fields that get saved in a ship attachment class,
you will get an error when loading ships with the old data. This is because the ship attachment
class is not compatible with the old data anymore. To fix this, you should create a new ship attachment class
and make the old one copy everything over to the new one, and then delete the old one from a ship.

For ship force inducers, you can use the `applyForces` method as event hook.

If your attachment is not a ship force inducer, you can use the shipLoadEvent to do that:

Register an event lister in your mod's on-load (init) method:
```kotlin
VSEvents.shipLoadEvent.on { event ->
  
}
```
In there, you can check if the ship has your old attachment and then remove it and save the new one.
(For more information about the events in VS2, go to [VS Events](vs_events.md))

## Force inducers
Implementing the `ShipForcesInducer` interface in the package
`org.valkyrienskies.core.api.ships` allows you to override a method that
gets called every physics tick for the ship it is attached to.
It also gives you a PhysShip object that you can use to apply forces to the ship.
```kotlin
package org.valkyrienskies.core.api.ships

import org.valkyrienskies.core.api.ships.PhysShip
import org.valkyrienskies.core.api.ships.properties.ShipId

@Deprecated("sus")
interface ShipForcesInducer {
    fun applyForces(
        physShip: PhysShip
    )

    fun applyForcesAndLookupPhysShips(
        physShip: PhysShip,
        lookupPhysShip: (ShipId) -> PhysShip?
    ) {
        // Default implementation to not break existing implementations
    }
}
```

## Thread safety
VS2 physics run in a different thread than the minecraft server thread.
This means that you need to be careful when accessing fields in your ship
attachment class.

## Examples
```kotlin
import com.fasterxml.jackson.annotation.JsonIgnore
import org.valkyrienskies.core.api.ships.ServerShip
import org.valkyrienskies.core.impl.api.ServerShipUser

class ShipFuelStorage(
    @JsonIgnore
    var ship: ServerShip? = null
) {
    var fuel: Int = 0
    
    companion object {
        fun getOrCreate(ship: ServerShip): ShipFuelStorage =
            ship.getAttachment<ShipFuelStorage>()
                ?: ShipFuelStorage(ship).also {
                    ship.saveAttachment(it) 
                }
    }
}
```

```kotlin
import com.fasterxml.jackson.annotation.JsonIgnore
import org.valkyrienskies.core.api.ships.PhysShip
import org.valkyrienskies.core.api.ships.ShipForcesInducer

class ShipThrusterControler: ShipForcesInducer {
    
    override fun applyForces(physShip: PhysShip) {
        // ...
    }

    companion object {
        fun getOrCreate(ship: ServerShip): ShipThrusterControler =
            ship.getAttachment<ShipThrusterControler>()
                ?: ShipThrusterControler().also {
                    ship.saveAttachment(it)
                }
    }
    
}
```
