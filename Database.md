When choosing how to store data within the framework, I was looking for three things

1. Fastest possible development process - this means avoiding databases with tedious table/column/field setups.
1. Easy to setup, with zero configurations
1. No extra installations

Achieving these points would mean that new developers don't have to worry about how their data is stored and structured, and they can launch demo with one click, without further setting up.

So to achieve these points, I decided to integrate an **embedded NoSQL database - LiteDB**. 

🔗 [Homepage](http://www.litedb.org/)

⚠️ LiteDB is the default solution, but if the need arises, you can switch it with something else.

## Interactions With Databases Are Abstract

All of the interactions with the database within framework are abstracted (by using interfaces). If you find yourself in a situation where you need to use a specific database solution, you should be able to write you own implementations.

## Performance

Framework itself is not database-use intensive, so performance was not the main point for choosing a database solution. 

Being an embedded database, LiteDBs' performance is not as good compared to specialized software (Mongo, MySQL, etc.). If it's running on a machine with SSD, it should still be able to handle most of the mid-sized games.

If you found out that database is your bottleneck, you can easily change usage of LiteDB to something else by implementing database-related interfaces, because interactions with db are abstracted.

ℹ️ Due to "synchronous" nature of LiteDB, interfaces were designed mainly for "blocking" interactions. I'll update the framework to allow asynchronous calls in the near future (probably by the end of the beta).

## Using Another Database Solution

All the modules that use database have an interface for interactions. For example, `AuthModule` interacts with database through interface `IAuthDatabase`.

`AuthModule` generates it's concrete database interactions by calling a method `SetupDatabase`, at the moment of writing, it's defined like this:

``` C#
protected virtual IAuthDatabase SetupDatabase()
{
            IAuthDatabase database = null;
#if (!UNITY_WEBGL && !UNITY_IOS) || UNITY_EDITOR
            database =  new AuthDatabaseLdb(new LiteDatabase("./auth.db"));
#endif
            return database;
}
```

As you can see, default implementation of this method creates an instance of `AuthDatabaseLdb`, which implements interface `IAuthDatabase`. 

You can change the database solution by creating a new class, which would implement `IAuthDatabase`, and then overriding the `SetupDatabase` method to return an instance of it.

Other modules that interact with database have a similar virtual method, which you can override to switch concrete implementation to one of your own.