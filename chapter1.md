# Chapter 1 - Initial Setup

This tutorial will take you through building a third person shooter game using the Bolt Networking Engine. The game
we are building will feature several complex features which allows us to go through most of what Bolt can do.

 * Authoritative movement, shooting and game logic
 * Replication of animation, transforms and game object state
 * ... **ADD MORE**

## Art

All of the art we are using in this tutorial has either been produced with, provided by or run through the excellent [PolyWorld: Woodland Toolkit](https://www.assetstore.unity3d.com/en/#!/content/18572).

![https://www.assetstore.unity3d.com/en/#!/content/18572](images/pw_logo.png)


## Getting Bolt

If you purchased through the Unity Asset Store, the best way to get the latest version of Bolt is by going to [http://www.boltengine.com/AssetStore.aspx](http://www.boltengine.com/AssetStore.aspx) and punching in your asset store invoice number, this lets you always access the latest version of Bolt without having to wait for the Asset Store approval process to catch up.

If you purchased Bolt through our site, you should have been supplied a download link on the return page after your purchase, if you have for some reason lost this link email [support@boltengine.com](support@boltengine.com) to receive a new one. We also send out a link for every release of Bolt to your email. 

## Basic settings and development features

Extract the downloaded *Bolt\_VERSION.zip* package to a folder somewhere on your disk and find the .unitypackage file called *Bolt\_VERSION_CompleteWithTutorial.unitypackage*. Create a new unity project and import the package, this will leave you with a directory structure as in the image below.

![](images/img0.png)

This package contains an already unpacked and installed version of Bolt, complete with a working version of the entire tutorial in the *bolt\_tutorial* folder. Before we get started with the tutorial, open up the *Bolt Settings* window which you can find at *Window/Bolt Settings* in the top menu.

![](images/img1.png)

Most of the settings here we can ignore for now, but look at the bottom *Console* settings and make sure that *Toggle Key* is set to something easily accessible for you, and also that *Visible By Default* is checked. This tells Bolt to always create an in-game console for you when it starts so that we have an easy way to get information from Bolt.   

Create a new folder in the root of your project, I will be calling my folder *tutorial* throughout this tutorial. Create the following folders inside of your new folder.

* Scripts
* Scenes
* Events

Your folder structure should now look like this.
 
![](images/img3.png)

Inside the *bolt\_tutorial/scenes* folder there is a preexisting level we are going to use, you are free to make your own before we continue. If you chose to use our level, copy the scene at *bolt\_tutorial/scenes/Level1.unity* into your own *tutorial/Scenes* folder and rename it *Level2.unity*. Our level looks like this.

![](images/img2.png)

We also need a *MainMenu* scene so that we have an easy way of starting our game while developing it; Bolt comes with a simple script which helps us do this. Create a new scene; on the Main Camera in the empty scene attach the script found in *bolt/scripts/BoltInit.cs*, save this scene to *tutorial/Scenes/MainMenu.unity*

![](images/img4.png)

Above is the *BoltInit* script attached to the main camera in the *ManuMenu* scene. Below is how your *tutorial* folder should be looking like.

![](images/img5.png)

Before we launch our scene to make sure everything is working we need to setup a few things inside Unity, first we  have to add both our *MainMenu* and *Level2* scene to the Build Settings. Make sure that *MainMenu* comes before *Level2*.

![](images/img6.png)

Click the *Player Settings...* button in the *Build Settings* window. Under *Settings for PC, Mac & Linux Standalone* verify that you have the following settings correct.

1. Default Is Full Screen - **DISABLED**  
2. Default Screen Width - **640**
3. Default Screen Height - **360**
4. Run In Background - **ENABLED**
5. Display Resolution Dialog - **DISABLED**

![](images/img7.png) 

You can obviously pick whatever width/height you want, but I find that if you want to run a couple of clients at the same time 640:360 is a good resolution. If you build a standalone version of your project and start two copies of it, you will be greeted by the following screen.

![](images/img8.png)

Start one of the instances as *Server* and the other as *Client*, when you click server you get to pick a map to load, and since we only have one *Level2* pick that. On the *Client* you get to input an IP and Port, but the default should be fine so just click *Connect*. On each of the instances you will see a screen that looks like this.

![](images/img9.png)

Since we don't have any camera inside our *Level2* scene, all we get is the blue background from the previous scene. Let's look at another way to start our game, which is especially handy when we need to do quick testing.

Open up the *Bolt Scenes* window by going to *Windows/Bolt Scenes*, it will look something like this.

![](images/img10.png)

This window looks a bit different depending on if you have Unity Pro or not, the window in the screenshot above is the version in Unity Pro, and if you have Unity Free you will not see the *Debug Start Settings* options or the *Debug Start* button. 

*Debug Start* allows you to tell Bolt to start a server + N clients, and then have them all connect to each other and load the correct scene, without manually doing anything. To learn more about it watch this video.

[![](http://img.youtube.com/vi/SIBu4SGxcRU/0.jpg)](http://www.youtube.com/watch?v=SIBu4SGxcRU)

The button we are going to look at right now is the *Play As Server* button, this button is available for both Unity Pro and Unity Free users and simply starts the scene you click on as a Bolt server, and since it's easy to setup your game with Bolt so that you can switch between dedicated or listen servers, this gives a quick and easy way of testing features when developing, below you can see the server running inside of the editor after pressing *Play As Server*.  

![](images/img11.png)

That was all for this chapter, in the next one we will start developing code for our game.

[Next Chapter >>](chapter2.md)
