Information provided here is not guaranteed to be up-to-date.

# Ship Attachments
Ship attachments are used to store data in a ship.

VS2 currently uses jackson to serialize and deserialize ship attachments.

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
you can however use a constructor with arguments for saveAttachment,
but you still need a default constructor without arguments.

To remove an attachment you can do:
```kotlin
serverShip.saveAttachment<MyAttachmentClass>(null)
```

## Attachment class fields
Every class you use as attachment needs to follow these rules:

- All fields that you don't want to be saved in the world need to be marked 
with `@com.fasterxml.jackson.annotation.JsonIgnore`
- All types of the fields that are not marked with
`@com.fasterxml.jackson.annotation.JsonIgnore` need to be classes with a default 
constructor without arguments. This means that you can't use interfaces as types of these fields

## World "corruption"
If you change anything related to fields that get saved in a ship attachment class,
you will get an error when loading ships with the old data. This is because the ship attachment
class is not compatible with the old data anymore. To fix this, you should create a new ship attachment class
and make the old one copy everything over to the new one, and then delete the old one from a ship.

## Ticked ship attachments
Implementing the `org.valkyrienskies.core.impl.api.Ticked` interface in your ship attachment class 
will run a method inside of it every server tick for the ship it is attached to.
This interface does not give you access to the ship the object is attached to.

The `Ticked` interface:
```kotlin
package org.valkyrienskies.core.impl.api

/**
 * Defines a class that wants to get ticked (in the server thread)
 */
@Deprecated("sus")
interface Ticked {

    fun tick()
}
```

## Accessing the ship from a ship attachment
Implementing the `ServerShipUser` interface in the package
`org.valkyrienskies.core.impl.api` gives you acces to the ship
the attachment is attached to.

The `ServerShipUser` interface:
```kotlin
package org.valkyrienskies.core.impl.api

import org.valkyrienskies.core.api.ships.ServerShip

/**
 * ServerShipProvider: A interface that just specifies the existence of a ship field
 */
@Deprecated("sus")
interface ServerShipProvider {
    val ship: ServerShip?
}

/**
 * Modifiable version of ServerShipProvider, will be automatically set when used as an attachment
 */
@Deprecated("sus")
interface ServerShipUser : ServerShipProvider {
    override var ship: ServerShip?
}

```

Note that you have to add the `com.fasterxml.jackson.annotation.JsonIgnore`
annotation to the ship field because otherwise it will try to save / load
the ship recursively, and then crash.

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
    ) { /* ... */ }
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

class ShipFuelStorage: ServerShipUser {
    @JsonIgnore
    override var ship: ServerShip? = null
    
    var fuel: Int = 0
    
    companion object {
        fun getOrCreate(ship: ServerShip): ShipFuelStorage =
            ship.getAttachment<ShipFuelStorage>()
                ?: ShipFuelStorage().also { 
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