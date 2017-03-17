### 
:warning: If you're using V1.xx version of the framework -  [**V1.03 documentation**](https://github.com/alvyxaz/barebones-master-v1/wiki)

## Table Of Contents (V2)

ℹ️ [**Main Changes In V2**](https://github.com/alvyxaz/barebones-masterserver/wiki/Main-Changes-in-V2)

⚠️ If you've downloaded framework from the asset store, you'll find V2.0 as a separate .unitypackage within `Barebones` folder. Read the `V2 README !IMPORTANT.txt` for more information.

Under the hood, framework is quite simple, but variety of different concepts might be a bit difficult to grasp at first - these guides are here to help you find your away through it.

_**If you found a section that's lacking**, or if you want something covered in the documentation - post a feature request here, in GitHub (Issues tab)_, or send me a personal message. I know what it feels to own a product with great documentation, so I'll be more than happy to assist. 🌞 

1. [**Introduction to the framework**](https://github.com/alvyxaz/barebones-masterserver/wiki/Introduction-To-The-Framework) - a general introduction to the main concepts of the framework
1. [**Folder Structure**](https://github.com/alvyxaz/barebones-masterserver/wiki/Folder-Structure)
1. API
   1. [**Authentication module examples**](https://github.com/alvyxaz/barebones-masterserver/wiki/API-examples.-Authentication)
   1. [**Rooms module examples**](https://github.com/alvyxaz/barebones-masterserver/wiki/API-examples.-Rooms)
   1. [**Spawners module examples**](https://github.com/alvyxaz/barebones-masterserver/wiki/API-examples.-Spawner)
   1. [**Chat module examples**](https://github.com/alvyxaz/barebones-masterserver/wiki/API-examples.-Chat)
1. Getting Started
   1. Core concepts / Basic examples
   1. Rooms Demo
   1. Worlds Demo
1. Development
   1. Creating modules
   1. Peer Extensions / States
   1. Sending / Receiving messages
   1. uNET + Rooms Module
   1. Switching database implementations
   1. Command Line Arguments
1. Production
   1. Make sure your IP addresses are correct
1. Modules
   1. Auth Module
   1. Chat Module
   1. Rooms Module
   1. Profiles Module
   1. Spawners Module

(I'm writing content at the moment ^_^)

## Editing Source code

It's highly advised that you don't make any changes to source code. In most cases, you should be able to make changes by implementing interfaces, extending classes or subscribing to events. If you edit the source, it's possible that your project will break when you download an update, because your changes will be overwritten.

_With that said, it's still possible that you might stumble upon an issue where there's no work around. In that case, make the changes and contact me, I'll try to provide a better access to what you need in the next update._