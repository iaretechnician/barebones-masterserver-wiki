First of all, please read [Profiles Module doc](https://github.com/alvyxaz/barebones-masterserver/wiki/Profiles-Module) because it does contain all the info you need. If you're still confused, come back...

Ok. I'm going to explain this using my own code from my very own project. You can compare to the doc linked above as another way to understand what you will need to change for your own project. You'll also see how I use coroutines to wait for things that aren't always ready when I go to use them. But first, a few explanations.

Any user can have profile data, and you access it via their username. Username is accessible when a user connects to your game server, and we'll see the simplest example below, using the UNET demo code provided in MSF. 

The profile gets defined on the master server AND in each of the places you need to use it. Think of the profile on the master as holding a model of all the data that you might want to store on a per-user basis, and then you define copies of them on the game server to set the values, and on the client to read them. Any data you want to store MUST be in the master server definition, but the client and game server definitions can be sub-sets, as we'll see. 

A profile is indexed by username, but the username doesn't have to exist in the auth database. This means the game server can create and access arbitrary profiles as long as the names don't collide with the name of a real user. Since users can only access profiles that have the same name as their user, they would not be able to see this data directly. This could be useful for storing data about the game-server itself, though it's probably technically better to create a new module for that kind of data because it would likely have different fields. 

# The Code

To start with, just create a new file with a class in it that will contain your profile. My game is called "Caris World" and I often abbreviate it as CW, so I'm going to create a CW Player Profile. _This script is attached to a game object in my master server scene._ It defines the profile factory that will be used by the client and game server. It's the simplest part. Just add the fields you want. 

    using Barebones.MasterServer;
    using Barebones.Networking;

    namespace HBYW.BMSF.CarisWorld
    {
        public class CWPlayerProfiles : ServerModuleBehaviour
        {
            public const short UserTokenKey = 1001;
            public const string UserTokenDefault = "00";

            public const short UsernameKey = 1002;
            public const string UsernameDefault = "username_not_set";

            public const short UserBreedKey = 1003;
            public const string UserBreedDefault = "userbreed_not_set";

            void Awake()
            {
                // Request for profiles module
                AddOptionalDependency<ProfilesModule>();
            }

            public override void Initialize(IServer server)
            {
                base.Initialize(server);

                var profilesModule = server.GetModule<ProfilesModule>();

                if (profilesModule == null)
                    return;

                // Set the new factory
                profilesModule.ProfileFactory = CreateProfileInServer;

                Logs.Warn("Created CW profile factory");
            }

            /// <summary>
            /// This method generates a "scheme" for profile on the server side
            /// </summary>
            /// <param name="username"></param>
            /// <param name="peer"></param>
            /// <returns></returns>
            public static ObservableServerProfile CreateProfileInServer(string username, IPeer peer)
            {
                return new ObservableServerProfile(username, peer)
                {
                    new ObservableString(UserTokenKey, UserTokenDefault),
                    new ObservableString(UsernameKey, UsernameDefault),
                    new ObservableString(UserBreedKey, UserBreedDefault)
                };
            }

            public static ObservableServerProfile CreateProfileInServer(string username)
            {
                return CreateProfileInServer(username, null);
            }


        }
    }


As I said above, we're going to use the (UNET HLAPI-based) Demo Room code from MSF. _On the game server_, we're going to access the profile right from inside our NetworkManager. In Awake, we need to listen for GameRoom events that we'll use to set up the profile. We also define some Obserables right in that class for easy reference. Note they are the same as the ones in the profile we created on the master server. (AngAccountManager and the AngAccount that it returns is my game code, included to demonstrate how MSF integrates.)

Remember, code inside the lambda expressions are really callbacks being executed AFTER the profile is returned: `Msf.Server.Profiles.FillProfileValues(defaultProfile, (successful, error) =>
            {...}`


    namespace HBYW.BMSF.CarisWorld
    {
        public class ModifiedNetworkManager : NetworkManager
        {
            // Set this in the inspector
            public UnetGameRoom GameRoom;

            ObservableString usertokenProperty;
            ObservableString userbreedProperty;
            ObservableString usernameProperty;

            void Awake()
            {
                if (GameRoom == null)
                {
                    Debug.LogError("Game Room property is not set on NetworkManager");
                    return;
                }

                // Subscribe to events
                GameRoom.PlayerJoined += OnPlayerJoined;
                GameRoom.PlayerLeft += OnPlayerLeft;

            }


            private void OnPlayerJoined(UnetMsfPlayer player)
            {
                AngError.NormalMessage("OnPlayerJoined: player.Username = " + player.Username);

                // Create an "empty" (default) player profile
                var defaultProfile = CWPlayerProfiles.CreateProfileInServer(player.Username);


                usertokenProperty = defaultProfile.GetProperty<ObservableString>(CWPlayerProfiles.UserTokenKey);
                usernameProperty = defaultProfile.GetProperty<ObservableString>(CWPlayerProfiles.UsernameKey);
                userbreedProperty = defaultProfile.GetProperty<ObservableString>(CWPlayerProfiles.UserBreedKey);

                // Fill the profile with values from master server
                Msf.Server.Profiles.FillProfileValues(defaultProfile, (successful, error) =>
                {
                    if (!successful)
                    {
                        Logs.Error("Failed to retrieve profile values: " + error);
                        return;
                    }
                    else
                    {
                        Logs.Info("Retrieved profile for " + player.Username);
                        GameControl.Instance.StartCoroutine(WaitForPlayer(player.Username, player.Connection.connectionId));
                    }
                });

            }


            IEnumerator WaitForPlayer(string player, int connid)
            {
                    //despite the adorable log messages, we are waiting for our client object to spawn on server and set connid
                    AngError.NormalMessage("WaitForPlayer(): Waiting for prince/princess to rescue me..."); //this is too cute not to leave in
                    yield return new WaitWhile(() => !GameControl.Instance.ConnIds.ContainsKey(connid));
                    AngError.NormalMessage("WaitForPlayer(): Finally I have been rescued!");
                    InitPlayer(player, connid);
                    AngNetMessages.SendReadyToStart(connid);
            }

            void InitPlayer(string player, int connid)
            {
                string username = (usernameProperty.Value == CWPlayerProfiles.UsernameDefault) ? player : usernameProperty.Value;
                usernameProperty.Set(username);

                string usertoken = (usertokenProperty.Value == CWPlayerProfiles.UserTokenDefault) ? AngUniqueID.GenerateUID(player) : usertokenProperty.Value;
                usertokenProperty.Set(usertoken);

                string userbreed = "BOPMAN";
                userbreed = (player == "caris" || player == "Caris") ? "CARISFAIRY" : userbreed;
                userbreed = (userbreedProperty.Value == CWPlayerProfiles.UserBreedDefault) ? userbreed : userbreedProperty.Value;
                userbreedProperty.Set(userbreed);

                AngAccount acc = AngAccountManager.Instance.AddAccount(usertoken, username);
                acc.ConnectionId = connid;
                acc.angnetcontrol = GameControl.Instance.ConnIds[connid];
                acc.CurrBreed = userbreed;
            }

Finally, _on the client_, from a NetworkBehaviour on the initial player object, we read the profile values. Note that when creating the ObservalbeProfile, you don't get ALL the profile values, you will get a warning, but that can be turned off in MSF. Look for `public bool IgnoreProfileMissmatchError = false;` Remember, just like the game server code above, the code inside the lambda expression `Msf.Client.Profiles.GetProfileValues(profile, (isSuccessful, profileError) = { ...} ` doesn 't run until after the result comes back!

    using UnityEngine.Networking;
    using Barebones.MasterServer;
    using HBYW.UniqueGameObject;
    using HBYW.BMSF.CarisWorld;

    namespace HBYW.Multiplayer
    {
        public class AngNetControl : NetworkBehaviour
        {
            bool initialized = false;

            void Start()
            {

                if (isServer)
                {
                    GameControl.Instance.ConnIds.Add(connectionToClient.connectionId, this);
                    initialized = true;
                }
                else if (isLocalPlayer)
                {
                    if (GameControl.Instance.PlayerGameObj == null)
                    {
                        if (GameControl.Instance.angNetControl == null)
                        {
                            GameControl.Instance.angNetControl = this;
                            GameControl.Instance.PlayerGameObj = gameObject;

                            AngError.NormalMessage("Trying to get Usertoken");

                            var profile = new ObservableProfile()
                            {
                                new ObservableString(CWPlayerProfiles.UserTokenKey, CWPlayerProfiles.UserTokenDefault),
                                new ObservableString(CWPlayerProfiles.UsernameKey, CWPlayerProfiles.UsernameDefault),
                                new ObservableString(CWPlayerProfiles.UserBreedKey, CWPlayerProfiles.UserBreedDefault)

                            };

                            var usertokenProperty = profile.GetProperty<ObservableString>(CWPlayerProfiles.UserTokenKey);
                            var usernameProperty = profile.GetProperty<ObservableString>(CWPlayerProfiles.UsernameKey);
                            var userbreedProperty = profile.GetProperty<ObservableString>(CWPlayerProfiles.UserBreedKey);

                            Msf.Client.Profiles.GetProfileValues(profile, (isSuccessful, profileError) =>
                            {
                                if (!isSuccessful)
                                {
                                    Logs.Error(profileError);
                                    return;
                                }
                                else
                                {
                                    GameControl.Instance.UserToken = usertokenProperty.Value;
                                    GameControl.Instance.InitClient(GameControl.Instance.UserToken, usernameProperty.Value);
                                    initialized = true;
                                }
                            });
                        }
                    }
                }
            }
        ...


