# Chapter 2

Bolt deals with replication in a completely different way compared to most (all?) other networking solutions for Unity. Instead of manually writing code for serializing data, for example using **OnSerializeNetworkView** in the built in Unity networking solution, Bolt lets you define transforms, animations and custom properties for it to automatically replicate over the network - you don't need to write any code what so ever. In fact in this entire tutorial we do not write any type of low level networking code, Bolt handles all of it for you.

## Adding a camera

The first thing we are going to do is just to setup a camera so that we can see what's going on, but instead of just dropping a camera into the scene we will go through Bolt and hook into some of it's callbacks. The finished tutorial comes with an already functioning camera which has all the features we need, and since this tutorial is not about building a third person camera, we are just going to use it verbatim.

You will find the camera prefab in *bolt_tutorial/prefabs/singletons/resources/PlayerCamera* and it's associated script is available in *bolt_tutorial/scripts/player/PlayerCamera.cs*. The camera script inherits from a utility base class defined inside bolt called BoltSingletonPrefab\<T\>, it is used to automatically load a prefab from a *Resources*.

Time to create our first script, in our own tutorial folder create the a script called *TutorialPlayerCallbacks.cs* in the folder *tutorial/Scripts/Callbacks*. 

* Have the class inherit from `BoltCallbacks`
* Apply the `[BoltGlobalBehaviour]` to the class
* Override the `SceneLoadLocalDone` method that is inherited from `BoltCallbacks`
* Inside the `SceneLoadLocalDone` override call `PlayerCamera.Instantiate()` (yes, without any arguments)
* It is very important that you **do not** attach this behavior to any game object in any of your scenes

```csharp
using UnityEngine;

[BoltGlobalBehaviour]
public class TutorialPlayerCallbacks : BoltCallbacks {
  public override void SceneLoadLocalDone(string map) {
	// this just instantiates our player camera, 
	// the Instantiate() method is supplied by the BoltSingletonPrefab<T> class
    PlayerCamera.Instantiate();
  }
}
```

![](images/img12.png)

Before we start our game it's probably a good idea to explain exactly what is going on here. What does the `[BoltGlobalBehaviour]` actually do? When Bolt starts, it will find *all* classes which have the `[BoltGlobalBehaviour]` and in some way or another inherit from `MonoBehaviour` (Since `BoltCallbacks` itself inherits from `MonoBehaviour` our own class `TutorialPlayerCallbacks` is also considered as inheriting from `MonoBehaviour`). 

Bolt will then go through the classes it found matching these two conditions and create instances of them automatically for you, so that they exist when Bolt is running and are destroyed when Bolt is shut down. Any instances which are created will be added to the 'Bolt' game object which is automatically created by Bolt on start, and you can clearly see it in your scene hierarchy.

There are a couple of ways to configure how `[BoltGlobalBehaviour]` will act, the first and most simple one is that you can decide if the behaviour in question should run on either the server or client, or both. Specifying nothing like we did for our `TutorialPlayerCallbacks` class means it will run on both the server and client.

```csharp
// only on the server
[BoltGlobalBehaviour(BoltNetworkModes.Server)]

// only on the client
[BoltGlobalBehaviour(BoltNetworkModes.Client)]
```

You can also tell Bolt that a behaviour should only be available during specific scenes, for example our scene is called *Level2* and if we only wanted our behaviour to run during this scene, we could configure the `[BoltGlobalBehaviour]` like this.

```csharp
// only when the current scene is 'Level2'
[BoltGlobalBehaviour("Level2")]

// only when the current scene is 'Level1', 'Level2' or 'Level3' 
[BoltGlobalBehaviour("Level1", "Level2", "Level3")]
```

You can also combine these.

```csharp
// only when we are the server AND the current scene is 'Level2'
[BoltGlobalBehaviour(BoltNetworkModes.Server, "Level2")]

// only when we are the client AND the current scene is 'Level2'
[BoltGlobalBehaviour(BoltNetworkModes.Client, "Level2")]
```

This is an integral part of Bolt as it allows you to easily define behaviour that is global to the entire application or an entire scene, without having to manually fiddle around with passing a game object marked with `DontDestroyOnLoad` around through all of your scenes. Like we mentioned before it is **paramount** that you do not under any circumstance manually attach these scripts to an object in your scene, Bolt will handle this for you automatically.

## Starting with our camera

Open the *Window/Bolt Scenes* window and click *Play As Server* on the *Level2* scene. You should now see the server starting in the *Game* window and our camera instantiate. 

![](images/img13.png)

If you look in the scene hierarchy you will see a game object called *Bolt*, this is bolts internal object and we went the route of making it competely visible (no HideFlags) so that you know what's going on at all times. If you check the inspector for this object you will see all of the internal behaviours that Bolt automatically instantiates, and also your `TutorialPlayerCallbacks` behaviour at the bottom. You will also see the PlayerCamera which was instantiated in the `SceneLoadLocalDone` callback.

![](images/img14.png)

## Spawning your character

Start by creating a new empty game object and call it *Player*, make sure that it is positioned at (0, 0, 0) with rotation (0, 0, 0) and scale (1, 1, 1). The model we are going to use you can find im *bolt_tutorial/arg/models/sgtBolt* and the prefab is called *sgtBolt4Merged-ModelOnly*, drag an instance of this prefab into the hierarchy. Make sure the sgtBolt prefab has the same position, rotation and scale values as the *Player* game object. Now drag the *sgtBolt4Merged-ModelOnly* object as a child to your *Player* object.

![](images/img15.png)

Create a new folder called *Prefabs* in your tutorial folder and drag your *Player* object into this folder to create a prefab out of it. You can now delete the Player object in the scene hierarchy.

![](images/img16.png)

