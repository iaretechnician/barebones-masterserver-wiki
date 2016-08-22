This section will cover how you should setup your project for easy development. 

:star: Result of this tutorial should be included in the asset you acquired. If you're lazy, you can just copy the whole solution and work with it there :)

## Create a New Solution

1. In Visual studio, go `File > New > Project`
2. Choose `Templates > Visual C# > Console Application`, and name it YourGame

This should have created a new solution, with startup project called **YourGame**. This project is going to hold our server code, and will compile into a console application, which we will use to start the server.

## Create a Common Project

We need a common project, which will be compiled into a .dll and shared between server and client.

1. Right click on your solution, then `Add > New Project`
2. This time, choose `Templates > Visual C# > Class Library`. Make sure it's **Class Library**. Name it **YourGame.Common**
3. We need to change Target Framework to **.NET 3.5**, because Unity doesn't support anything higher. Right click on your project, go to `Properties > Application` and change the TargetFramework to 3.5. 
4. You can delete the `Class1.cs` file, which was created automatically

## Adding Source

We should start by adding Barebones MasterServer code into our solution. You can do that by referencing DLL files, but I think you'd be happier if you added the projects, so you could navigate through source code if necessary.

Right click on your solution, `Add existing project`, and navigate to `MasterServerSource` folder

Now you'll need to add these projects:
* `Barebones.MasterServer` - main master server project
* `Barebones.MasterServer.Common` - files, shared between master server and client 
* `Barebones.MasterStarter`_(optional) - includes code to help you get started (parses cmd args, initializes objects and etc.)
* `Barebones.Networking` - networking API code
* `Barebones.Networking.Server` - helpful server scripts

If you have imported all of the projects, here's what you should have:

![](http://i.imgur.com/2InoccI.png)

## Adding Project Dependencies

1. Expand **YourGame** project, right click on **References**, then go `Add Reference > Projects >` and select all of the projects
2. Expand **YourGame.Common**, and add project references to:
    * Barebones.Networking
    * Barebones.MasterServer.Common

## Adding Third Party Dependencies

1. Expand **YourGame** project, right click on **References**, then go `Add Reference > Browse` and click Browse  button.
2. Navigate to the `MasterServerSource > libs` directory, and select:

    * log4net.dll - logging library
    * LiteDB _(optional)_ - embedded database. Include only if you want to use it

And hit ok

## Logger setup

Now you should open `App.config` file and add log4net configuration. Configuring log4net might be a challenging task, this is why I'm adding my App.config, which you can copy (or copy portions of it):

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <configSections>
    <section name="log4net" type="log4net.Config.Log4NetConfigurationSectionHandler, log4net" />
  </configSections>
  
  <log4net>
    <appender name="ColoredConsoleAppender" type="log4net.Appender.ColoredConsoleAppender">
      <mapping>
        <level value="WARN" />
        <foreColor value="Yellow, HighIntensity" />
      </mapping>
      <mapping>
        <level value="ERROR" />
        <foreColor value="Red, HighIntensity" />
      </mapping>
      <mapping>
        <level value="FATAL" />
        <foreColor value="White" />
        <backColor value="Red" />
      </mapping>
      <mapping>
        <level value="INFO" />
        <foreColor value="Cyan" />
      </mapping>
      <mapping>
        <level value="DEBUG" />
        <foreColor value="Green" />
      </mapping>
      <layout type="log4net.Layout.PatternLayout">
        <conversionPattern value="%date [%thread] %-5level %logger - %message%newline" />
      </layout>

      <!-- Disable super sockets logging -->
      <filter type="log4net.Filter.LoggerMatchFilter">
        <loggerToMatch value="CustomWebsocketServer" />
        <acceptOnMatch value="false" />
      </filter>

    </appender>

    <appender name="FileAppender" type="log4net.Appender.FileAppender">
      <file value="info.log" />
      <appendToFile value="true" />
      <lockingModel type="log4net.Appender.FileAppender+MinimalLock" />
      <layout type="log4net.Layout.PatternLayout">
        <conversionPattern value="%utcdate [%-3thread] %-5level - %message (%logger)%newline" />
      </layout>
    </appender>

    <root>
      <level value="DEBUG" />
      <appender-ref ref="ColoredConsoleAppender" />
      <appender-ref ref="FileAppender" />
    </root>

  </log4net>
  
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
</configuration>
```

:information_source: What should be of interest to you is `log4net` tree and `configSections`element


##  Starting project

Open the `YourGame.Program.cs`, and add this code:

```C#
using System.Threading;
using System.Threading.Tasks;
using Barebones.MasterStarter;

namespace YourGame
{
    class Program
    {
        static void Main(string[] args)
        {
            log4net.Config.XmlConfigurator.Configure();  
          
            Task.Run(() =>
            {
                var starter = new Starter();
                starter.Setup();
                Start(starter);
            });
            while (true)
            {
                Thread.Sleep(1000);
            }
        }


        static void Start(Starter starter)
        {
            // Starter object exposes all of the objects that you might need
            starter.Start();
        }
    }
}
```

Try to run your project (`Debug -> Start Without Debugging`), and you should see a warning:

`You didn't really start anything... Check out command line arguments`

To start the game

