By default, MSF uses [Lite DB V2](https://github.com/mbdavid/LiteDB) for data storage. It was chosen because it's **Embedded**, **requires no setting up**, and works with **.NET 2.0 subset** target. 

However, it's not perfect for all games, because:

* It's very hard to edit the data, as it's in binary form
* V2 is outdated. V3 should be used (but it required full **.NET 2.0**)
* Other databases have better performance

So because of these issues, you might want to change your database implementation. 

## Other implementations

All of the MSF accesses to databases are through interfaces, which means that you can switch implementations to use anything you need. If you want, you can even write your own.

You can find alternative database implementations in `Barebones/MsfDatabaseImplementations/`. They will be zipped, so if you want to use them, you'll need to extract them first. By default, there should be two alternatives:

* MySQL
* MongoDB

## How to Change The Database Implementation

1. Extract the necessary files
1. `File > Build Settings > Player Settings > Other Settings > Api COmpatibility Level: ` **.NET 2.0** (not subset)
1. Create a new game object as a child of `MasterServer/Databases/` (hierarchy)
1. Attach to it a factory component (for example, if it's MongoDB: `MongoDbFactory`, MySQL: `MySqlDbFactory`)

![](http://i.imgur.com/iyFcIQu.png)

## Installing Alternative Databases

* MongoDB doesn't require any setting up, except for installing a database
* MySQL - you'll need to "inject" provided SQL code into your database to setup the tables

There are plenty tutorials on installing these databases that are a lot better than what I could write. 

If you still can't do it, send me a message and I'll try to help you out 😉 