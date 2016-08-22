This section will cover how you should setup your project for easy development

## Create a New Solution

1. In Visual studio, go `File > New > Project`
2. Choose `Templates > Visual C# > Console Application`, and name it YourGame

This should have created a new solution, with startup project called **YourGame**. This project is going to hold our server code, and will compile into a console application, which we will use to start the server.

## Create a Common Project

We need a common project, which will be compiled into a .dll and shared between server and client.

1. Right click on your solution, then `Add > New Project`
2. This time, choose `Templates > Visual C# > Class Library`. Make sure it's **Class Library**. Name it **YourGame.Common**
3. We need to change Target Framework to **.NET 3.5**, because Unity doesn't support anything higher. Right click on your project `Properties > Application` and change the TargetFramework to 3.5. 