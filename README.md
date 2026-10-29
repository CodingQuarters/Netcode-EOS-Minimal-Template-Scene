# Netcode for GameObjects + EOS P2P - Minimal Template Scene
Get EOS P2P working with Unity Netcode for GameObjects very quickly. A minimal starter project for free peer-to-peer multiplayer.

This project erases the struggles and saves you time to get 
your multiplayer game working faster and better. 

## Why would you choose EOS + Netcode 
- Netcode is the official unity package for multiplayer
- Epic Online Services (EOS) handles the matchmaking
- It uses P2P (Peer-to-peer), no per player multiplayer server hosting required 
- Very powerful tools, allowing you to have endless possibilities

## Features
- This project is simply a unity scene. You just need to download it and then import it to get it working. However, you'll need to install EOS + netcode for GameObjects packages (I'll explain how to do that later)

## To get this working  
### Install the packages needed:
- install the package from Unity called Netcode for GameObjects ([official link]([Netcode for GameObjects](https://docs.unity3d.com/Packages/com.unity.netcode.gameobjects@3.1/manual/index.html)))
- install the package from playEveryWare EOS ([Epic Onlines Services](https://github.com/EOS-Contrib/eos_plugin_for_unity_upm)) with this github url in your package manager : 
```
https://github.com/EOS-Contrib/eos_plugin_for_unity_upm.git
```
### For EOS:
- Create a developer account at [Epic Games Portal](https://dev.epicgames.com/portal/) if you don't already have one
- Create a new product 
- In your product, create a new client policy (choose Peer 2 Peer as your client policy)
- Create a client 
    - Add your policy newly created
    - Do not add any IP players allowed
- Create an application
    - Add a linked client
    - Save changes for both permissions and linked client
### In Unity : 
- open the EOS configuration window (might need to close and reopen your project for it to appear)
- fill in everything needed (Product Name, Product Id, Version, Client, Client Secret, Sandbox, Deployment)
- Import the scene (Netcode-EOS-Minimal-Template2.unitypackage) that you downloaded 
- To confirm that it is done, playtest it by : 
    - Running the build from two computers (you can also run it in your unity editor)
    - Create the lobby on one instance and send the lobby code
    - Join the lobby on the other instance with the lobby code
    - Confirm both clients connected to Netcode
    - Press PLAY and verify that only the host loads the next scene
        - However, in this template the way it starts the game is through an RPC sent to the clients. To make the clients also start the game, you need to add in one of your client script, something like that: 
```C#
    public override void OnNetworkSpawn()
    {
        SceneManager.OnGlobalActionTriggered += HandleGameStart; // HandleGameStart handles the logic to start the game, such as spawning and assigning gameobjects

    }
    public override void OnNetworkDespawn()
    {
        if (NetworkManager.Singleton != null)
        {
            SceneManager.OnGlobalActionTriggered -= HandleGameStart;
        }
        base.OnNetworkDespawn();
    }
```

> [! NOTE]
> This template is designed for those with minimal experience with [Netcode for GameObjects](https://docs.unity3d.com/Packages/com.unity.netcode.gameobjects@3.1/manual/index.html) and Epic Onlines Services such as RPCs and network Variables. This project is not perfect and you may find bugs.


## What's included / features
- a .unitypackage containing a unity scene. You need to create a new scene to import that on top of that scene 
- login and creating account automatically done in background through device Id login
- creating lobbies, setting up what is the minimum amount of people to start the game and what is the maximum
- joining a lobby
- basic UI

## Limitations
- it has only been tested on PC
- it does not take into accounts edge cases such as closing the game and leaving a lobby
- Has only been tested on the version 6.1.2 of Epic Online Services Plugin for Unity and version 2.13.2 of Netcode for GameObjects
- tested it only on Unity version 6.3 LTS (6000.3.7f1)

## How does it work? 
It uses [EOS PlayEveryWare plugin](https://eospluginforunity.playeveryware.com).
Allows players to connect to create a lobby, connect to a lobby and to start a game. It sets up an easy scalable system.
Manages all the logins of the players in the background. 
Once creating a lobby or joining a lobby, it starts Netcode. 
When the host presses PLAY, it calls an RPC to all the clients and they all load the next scene. Also, you cannot put a prefab in the netcode slot to automatically spawn: you need to make it spawn afterhand, once the game starts.
This template is only bridging the gap to connect and create a lobby to initializing netcode.

## The story behind it
3 months ago, I wanted to make a Unity multiplayer game, which you can check out by the way ([The Faithful Voice on itch io - Amazing Game](https://codingquarters.itch.io/the-faithful-voice)). Looking into the options I had, I chose EOS + Netcode. However, I realized soon after that the EOS PlayEveryWare plugin is not that easy to setup. It's a powerful plugin, but it didn't have what I needed in its samples. I needed a simple connect through device ID + a way to create simple lobbies and joining lobbies. So I spent 3 months tackling this and spending more time building the multiplayer system than actually making the core of the game. Thus, I wanted to release this template for anyone who has the same problem as I do and does not want to waste a lot of time simply building the multiplayer logic. 

## Why is it a scene and not a unity project 
I originally wanted it to be a Unity project, but in the project's files, there would be the raw files from PlayEveryWare and Netcode, which does not comply with their policy. Therefore, you have to download the packages yourself. 

## Troubleshooting/Common bugs
When you first import it into your project, there may be one error in P2PManager.cs script saying that "P2PSocketName is not accessible". You simply need to go into the EOSTransport.cs file and change the variable private string P2PSocketName to public.

When you playtest your game, if an error comes up saying that it can't initialize EOS, it most likely is because you forgot one field or more in your EOS Configuration window.

---

If you have more bugs, things not working or even have a new feature in mind, I'm more than happy to help. Just submit them in the Issues tab.
