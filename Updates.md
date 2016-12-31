## Warning

:warning: It's very important to make a backup before upgrading. Always.

## V1.03

### Breaking Changes

API Compatibility level will need to be changed to .NET 2.0
(`File > Build Settings > Player Settings > Other Settings`)

![](http://i.imgur.com/61WRlSD.png)

:warning: This version contains a relatively large number of breaking changes, mainly in:

* 🔥 **Default port for client connections changed from 90 to 5000** - this is to accomodate permission issues on MacOS
* Command line arguments
  * -bmStartDedicated -> -bmStartManual
  * -bmStartUserCreated -> -bmStartSpawned ("user created" made sense originally, but not anymore)
  * -bmInstanceId -> -bmSpawnId
* A bunch of method names were changed to make more sense, most of them were used internally, and shouldn't give you much trouble. If you have trouble upgrading - feel free to contact me :)

### Other changes:

* New feature - Spawner Inspector 
* New feature - MongoDB support
* New feature - improved logging (just a first step towards proper logging)
* `MessageHelper.Create` now accepts uNet messages (extensions of BaseMessage) - this means you can reek some of the benefits of automatic serialization, provided by uNet
* `IIncommingMessage.DeserializeMessage<BaseMessage type>()` - deserializes uNet messages
* Bug Fix - `EndianBinaryWriter` string serialization fix*
* Bug Fic - Lobby map names are visible in the games list
* Added more control for giving out accesses to game servers:
  * Added a new method to the interface `IGameServer` - `ValidateAccessRequest` - this will be called before giving an access to a user who requests it. Useful to do some custom checking for bans or other rules you might need
  * Added a new method to the interface `IGameServer` - `FillAccessProperties` - useful, in case you want to attach extra data to the access before sending it to the player
* Spawner Queue sizes can now be changed via `SpawnersModule` - spawner queue is there to make sure people don't wait too long on a relatively busy server
* RSA key size - can be changed via static `BmSecurity.RsaKeySize` field
* Guest login can now be disabled via field AuthModule.EnableGuestLogin (inspector)
* Added two methods to `IObservableProperty` - `SerializeToString` and `DeserializeFromString` - these can be used when writing custom database implementations, so that you can save data in human-readable formats (previously it was just binary)
* A bunch of other things I forgot to note



