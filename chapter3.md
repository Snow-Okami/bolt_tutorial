[<< Prev Chapter](chapter2.md)

# Chapter 3

In this chapter we will handle taking control of entities and moving around in the world, this will familiarize you with how the *control* concept in Bolt works and how it handles authoritative movement.  

## Hiding server and client differences

Before we go onto taking control of our entities we are going to deal with and explain something which comes up a lot when dealing with both Bolt and multiplayer in general.

**The problem:** *If we want let the server be just another player in the game, how do we deal with the fact that the server doesn't exist as a connection on itself?*

Each client that connects to the server is represented by a `BoltConnection` object and on each client the server is represented as a single `BoltConnection` object. However when we want to do something to the "server player" on the server itself we have no easy way to refer to it, since there is no object which represents the server on itself.

The answer to this is that we need to create a simple abstraction, which lets us deal with a *Player* object instead of a specific connection, in this player object we will hide if we have a connection or not, so that the rest of our code does not have to think about it.

Create two new C# files and call them *TutorialPlayerObject.cs* and *TutorialPlayerObjectRegistry.cs*. Let's start in the `TutorialPlayerObject` class.

```csharp
public class TutorialPlayerObject {
  public BoltEntity character;
  public BoltConnection connection;
}
``` 

The is a standard C# class, it does **not** inherit from unitys `MonoBehaviour` class. This is very important. It also contains two fields called `character` and `connection`. The `character` field will contain the instantiated object which represents the players character in the world. The `connection` field will contain the connection tho this player **if one exists**, this will be `null` on the server for the servers player object.

We are going to add two properties also, which lets us check if this is a client or a server player object without having to deal with the `connection` field directly.

```csharp
public class TutorialPlayerObject {
  public BoltEntity character;
  public BoltConnection connection;

  public bool isServer {
    get { return connection == null; }
  }

  public bool isClient {
    get { return connection != null; }
  }
}
``` 

`isServer` and `isClient` simply check if the connection is or isn't null, which tells us if the player represents the server or a client. Before we add more functionality to our `TutorialPlayerObject` we are going to open up the `TutorialPlayerObjectRegistry` class. We are using this class for managing instance of our `TutorialPlayerObject` class.

The only thing in this entire class which isn't just standard C# code is that we are accessing the `userToken` property on the `BoltConnection` class. This property is simply a place where you can stick any type of other object/data that you want to pair with the connection. In our case we are going to pair the `TutorialPlayerObject` we create with the connection it belongs to (if it belongs to one). 

The remainder of this class contains very little which is specific to Bolt, if you want read through the code and comments below but we're not going to go into more detail on it.


```csharp
using System.Collections.Generic;
using System.Linq;

public static class TutorialPlayerObjectRegistry {
  // keeps a list of all the players
  static List<TutorialPlayerObject> players = new List<TutorialPlayerObject>();

  // create a player for a connection
  // note: connection can be null
  static TutorialPlayerObject CreatePlayer(BoltConnection connection) {
    TutorialPlayerObject p;

    // create a new player object, assign the connection property
    // of the object to the connection was passed in
    p = new TutorialPlayerObject();
    p.connection = connection;

    // if we have a connection, assign this player 
    // as the user token for the connection so that we
    // always have an easy way to get the player object 
    // for a connection
    if (p.connection != null) {
      p.connection.userToken = p;
    }

    // add to list of all players
    players.Add(p);

    return p;
  }

  // this simply returns the 'players' list cast to 
  // an IEnumerable<T> so that we hide the ability 
  // to modify the playe rlist from the outside.
  public static IEnumerable<TutorialPlayerObject> allPlayers {
    get { return players; }
  }

  // finds the server player by checking the 
  // .isServer property for every player object.
  public static TutorialPlayerObject serverPlayer {
    get { return players.First(x => x.isServer); }
  }

  // utility function which creates a server player
  public static TutorialPlayerObject CreateServerPlayer() {
    return CreatePlayer(null);
  }

  // utility that creates a client player object.
  public static TutorialPlayerObject CreateClientPlayer(BoltConnection connection) {
    return CreatePlayer(connection);
  }

  // utility function which lets us pass in a 
  // BoltConnection object (even a null) and have 
  // it return the proper player object for it.
  public static TutorialPlayerObject GetTutorialPlayer(BoltConnection connection) {
    if (connection == null) {
      return serverPlayer;
    }

    return (TutorialPlayerObject)connection.userToken;
  }
}
```

Open up the *TutorialServerCallbacks.cs* file an update the class we have in there, remove the two calls to `BoltNetwork.Instantiate`. Implement the unity `Awake` and call `TutorialPlayerObjectRegistry.CreateServerPlayer` inside it, this creates the server player for us whenever this callback object becomes active.

Also in `TutorialServerCallbacks` override the method called `ClientConnected` inherited from `BoltCallbacks`. Inside of it call `TutorialPlayerObjectRegistry.CreateClientPlayer` and pass in the connection argument.

```chsarp
using UnityEngine;

[BoltGlobalBehaviour(BoltNetworkModes.Server)]
public class TutorialServerCallbacks : BoltCallbacks {
  void Awake() {
    TutorialPlayerObjectRegistry.CreateServerPlayer();
  }

  public override void ClientConnected(BoltConnection arg) {
    TutorialPlayerObjectRegistry.CreateClientPlayer(arg);
  }

  public override void SceneLoadLocalDone(string map) {
  }

  public override void SceneLoadRemoteDone(BoltConnection connection, string map) {
  }
}

```



[Next Chapter >>](chapter4.md)