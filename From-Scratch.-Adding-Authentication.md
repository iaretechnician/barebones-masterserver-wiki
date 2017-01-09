
# Setup

## Server Side

### 1. Add Auth module

* Create an empty game object as a child of **Master Server**
* Name it **AuthModule**
* Attach "AuthModule" component

> **Does it have to be a child of "Master Server"?** Nope, not really. It's just for convenience. Master server looks for modules in the whole scene

### 2. Add Database Factory 

* Create a new empty object as a child of "Master Server"
* Name it **Database**
* Attach **LiteDbFactory** component

#### What's the database factory?

Instead of using databases directly, all of the modules use (depend on) simple interfaces. It means that modules don't really care about which database is used, as long as it implements the required interface. This allows us to switch database implementations when necessary.

Database factory is a central script, responsible for creating / giving concrete database implementations. In this example, we're using LiteDB.
