# Networking (using vscore)

1. Create a packet class implementing `SimplePacket`. This class will be serialized by Jackson.
```kotlin
data class MyPacket(val someVal: Int): SimplePacket
```
  
3. Register your packet on mod init using:
```kotlin
// uncomment if VS with new VS Core
// with(vsCore.simplePacketNetworking) {
MyPacket::class.register()
// }
```

4. Register a client or server handler (on mod init):
```kotlin
// uncomment if VS with new VS Core
// with(vsCore.simplePacketNetworking) {
MyPacket::class.registerClientHandler { // or alternatively registerServerHandler 
  println(it.someVal)
}
// }
```

5. Send the packet:
```kotlin
// uncomment if VS with new VS Core
// with(vsCore.simplePacketNetworking) {
MyPacket(10).sendToAllClients() // alternatively: "sendToClient(IPlayer)", "sendToServer()"
// }
```

6. Profit
