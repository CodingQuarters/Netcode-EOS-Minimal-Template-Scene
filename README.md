# Netcode for GameObjects + EOS P2P - Minimal Template Scene
Get EOS P2P working with Unity Netcode for GameObjects very quickly. A minimal starter project for free peer-to-peer multiplayer.

This project simplifies the process and saves you time to get 
your multiplayer game working faster and better. 

> [!NOTE]
> This template is designed for those with minimal experience with [Netcode for GameObjects](https://docs.unity3d.com/Packages/com.unity.netcode.gameobjects@3.1/manual/index.html) and Epic Online Services such as RPCs and network Variables. This project is not perfect and you may find bugs.

## Why would you choose EOS + Netcode?
- Netcode is the official Unity package for multiplayer
- Epic Online Services (EOS) handles the matchmaking
- It uses P2P (Peer-to-peer), no per player multiplayer server hosting required 
- Very powerful tools, allowing you to have lobbies, Device ID login, and host-authoritative Netcode in one scene

## Features
- This project is simply a Unity scene. You just need to download it and then import it to get it working. However, you'll need to install EOS + netcode for GameObjects packages (I'll explain how to do that later)
- This Unity scene contains the very minimum to get this working. With only 2 files (P2PManager.cs (for the EOS logic) and UIManager.cs (for the UI logic)), it contains also all the UI and the setup needed.
- Since it uses Device Id to sign you in, you will not be able to connect to two instances on the same computer unfortunately.

## To get this working  
### Install the packages needed:
- install the package from Unity called Netcode for GameObjects ([Netcode for GameObjects](https://docs.unity3d.com/Packages/com.unity.netcode.gameobjects@3.1/manual/index.html))
- install the package from playEveryWare EOS ([Epic Online Services](https://github.com/EOS-Contrib/eos_plugin_for_unity_upm)) with this github url in your package manager : 
```
https://github.com/EOS-Contrib/eos_plugin_for_unity_upm.git
```
### For EOS:
- Create a developer account at [Epic Games Developer Portal](https://dev.epicgames.com/portal/) if you don't already have one
- Create a new product 
- In your product, create a new client policy (choose Peer 2 Peer as your client policy)
- Create a client 
    - Add your policy newly created
    - Leave the IP allowlist empty
- Create an application
    - Add a linked client
    - Save changes for both permissions and linked client
### In Unity : 
- open the EOS configuration window (might need to close and reopen your project for it to appear)
- fill in everything needed (Product Name, Product Id, Version, Client, Client Secret, Sandbox, Deployment) - follow the appropriate EOS guide 
- Import the scene (Netcode-EOS-Minimal-Template2.unitypackage) that you downloaded 
- To confirm that it is done, playtest it by : 
    - You need two separate devices; one can be the Unity editor
    - Create the lobby on one instance and send the lobby code
    - Join the lobby on the other instance with the lobby code
    - Confirm both clients connected to Netcode
    - Press PLAY and verify that only the host loads the next scene
### Important: Starting the game on all clients
When the host presses **PLAY**, the template sends an RPC to trigger the game-start logic. However, the current implementation does not automatically make every client load the next scene.
To make the game start on all clients, you need to add a handler to your own client-side script that listens for the game-start event and performs the necessary actions, such as loading the next scene, spawning player objects, and assigning roles.
For example, you can subscribe to the appropriate game-start event when your networked client object spawns, then unsubscribe when it despawns. In the case of the template, when the host presses **START** it invokes from the UIManager an event called OnGlobalActionTriggered. You could then do this from another script that your client has access to.
```Csharp
    public override void OnNetworkSpawn()
    {
        UIManager.OnGlobalActionTriggered += HandleGameStart; // HandleGameStart handles the logic to start the game, such as spawning and assigning gameobjects

    }
    public override void OnNetworkDespawn()
    {
        if (NetworkManager.Singleton != null)
        {
            UIManager.OnGlobalActionTriggered -= HandleGameStart;
        }
        base.OnNetworkDespawn();
    }
```
**Note:** The template handles the basic EOS lobby and Netcode connection flow. Synchronizing your game's scene transitions and gameplay logic may require additional implementation.

### Important: P2PSocketName not accessible error
When you first import the template into your project, there may be one error in P2PManager.cs script saying that "P2PSocketName is not accessible". You simply need to go into the EOSTransport.cs file and change the variable "private string P2PSocketName" to "public". EOSTransport.cs file is a file from the PlayEveryWare plugin. However, I don't believe this is a universal issue with all the versions of EOS.



## Import the template
1. Open your Unity project.
2. Install Netcode for GameObjects and the PlayEveryWare EOS plugin.
3. Open the Unity Package Manager's import option and import `Netcode-EOS-Minimal-Template2.unitypackage`.
4. Open the scene included in the imported package.
5. Configure EOS using your own product, client, sandbox, and deployment settings.
6. Follow the testing steps below to verify that the connection flow works.

The package contains the template scene and its associated files. It does not include the third-party packages, which must be installed separately.

Here is how it would look like:
<img width="100%" alt="Gif Sample" src="https://github.com/user-attachments/assets/30792ec3-1e2a-48ea-b812-6a4e5ff208be" />


## Limitations
- It has only been tested on PC
- It does not handle all edge cases, such as unexpected disconnections or players leaving a lobby
- Tested only with version 6.1.2 of Epic Online Services Plugin for Unity and version 2.13.2 of Netcode for GameObjects
- Tested it only on Unity version 6.3 LTS (6000.3.7f1)

## How does it work? 
It uses [EOS PlayEveryWare plugin](https://eospluginforunity.playeveryware.com).
Allows players to create or join a lobby and start a game. It sets up an easy scalable system.
Manages all the logins of the players in the background. 
Once creating a lobby or joining a lobby, it starts Netcode. 
When the host presses PLAY, it calls an RPC to all the clients and they are all supposed to load the next scene. However, to keep this project minimal, the clients won't load the next scene, only the host will (see the section "Important: starting game on all clients" above for more info). Also, you cannot put a prefab in the netcode slot to automatically spawn: you need to make it spawn afterward, once the game starts.
This template is only bridging the gap to connect and create a lobby to initializing netcode.



## Why is it a scene and not a Unity project 
I originally wanted it to be a Unity project, but in the project's files, there would be the raw files from PlayEveryWare and Netcode, which their licenses don’t allow redistributing the package files. Therefore, you have to download the packages yourself. 

## Troubleshooting/Common bugs
- When you playtest your game, if an error comes up saying that it can't initialize EOS, it most likely is because you forgot one field or more in your EOS Configuration window.

- If the Host button is all gray and you can't click on it, that means that you're not signed in yet. You need to wait a little (usually from 1-10 seconds), but more if it's your first time (as it would have to create an account) launching the game.

---

If you have more bugs, things not working or even have a new feature in mind, I'm more than happy to help. Just submit them in the Issues tab.

## The story behind it
3 months ago, I wanted to make a Unity multiplayer game, which you can check out by the way ([The Faithful Voice on itch io](https://codingquarters.itch.io/the-faithful-voice)). I chose to use EOS + Netcode. However, I realized soon after that the EOS PlayEveryWare plugin is not very easy to setup. It's a powerful plugin, but it didn't have what I needed in its samples. I needed a simple way to connect the player through device ID + to manage lobbies creating and joining. So I spent 3 months spending more time building the multiplayer system than actually making the game. Thus, I wanted to release this template for anyone who has the same problem as I do and does not want to waste a lot of time simply building the multiplayer logic of their game. 