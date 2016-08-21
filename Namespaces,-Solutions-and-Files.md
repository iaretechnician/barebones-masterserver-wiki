## Master Server Solution

Master server (backend) solution consists of 5 projects:

1. `Barebones.MasterServer` - this is the main project, which contains most of the BM functionality, including authentication, accepting client and spawner connections, managing game servers and etc.
1. `Barebones.MasterServer.Common` - this project is compiled into a DLL which is shared between master server and client. It mostly contains OpCodes and Packet definitions
1. `Barebones.MasterServer.Demo` - this is a console application project, which is there to server as an example on how you can setup your master server.
1. `Barebones.Networking` - contains the core networking API, used to allow communication between servers and clients
1. `Barebones.Networking.Server` - contains some helpful classes for writing server-side code
