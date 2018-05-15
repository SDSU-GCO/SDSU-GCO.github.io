# Building a small game in Unity's ECS
This guide covers building a small video game in Unity's ECS. This guide applies to both 2D and 3D games. The current version of this guide targets Unity's 2018.1.2 release build. At this time, ECS in Unity is still incredibly new. While I aim to provide the best guidance possible, I do not have enough information nor experience to provide the absolute best practices. For any questions or curiosities, the following links contain the best resources to understand this topic: [Samples and Documentation](https://github.com/Unity-Technologies/EntityComponentSystemSamples)  [Forums](https://forum.unity.com/forums/entity-component-system-and-c-job-system.147/)

For this guide, I will be using a Wii Play Tanks clone as an example. However, any game that involves constant updating will work really well. I am writing this guide targeting new developers familiar with basic game development concepts but new to programming and ECS fundamentals. However, I'll include some tips for some more advanced concepts and implementations along the way.

This guide is broken up into phases. These are meant to be hard stopping points, as especially members at GCO have a tendency of jumping too far ahead without finishing all the steps and everything dissolves into hypothetical hot air. Follow this guide correctly and you will end up with a working game! 

## Phase 1. Preparation

###  Step 1.  Define your Premise
What is the basic concept of your game? What genre is your game? What art style is your game? What makes your game unique? How long is it going to take you? Keep things simple but make sure it is interesting. Plan out some core gameplay that you want to implement in the short term, but then have an idea of how you might expand the game down the road.

#### Example
In my case, I want to recreate much of the fun experience of Wii Play Tanks. Long term, I want this to be a large multiplayer online battle game with a bunch of different factions consisting of different kinds of toys. But for the starting point I just want a couple of tanks able to drive around, shoot, and lay mines.

###  Step 2. Install your Tools
Now that you got enough of an idea in your head where you are ready to start this adventure, download an appropriate version of Unity. Start a new project when you are ready.

*Once you open a project, you may need to need to go to Edit->Project Settings->Player and in the "Other Settings" category you need to change the Scripting Runtime to use Stable .NET 4.x rath than Legacy .NET 3.5. You'll then want to open the Package Manager Window from the toolbar and add the Entities Preview Package. You may have to re-import all assets in your project to get it to work. You will also probably have to update Visual Studio and possibly install the .NET 4.0-4.6 kits in Visual Studio. But if everything works correctly, you should have 0 red errors in the console window. In Visual Studio, you can test if everything is working by adding*
```csharp
using Unity.Mathematics;

float2 duh;
```
*to a default Unity C# script just under all the other 'using' commands. If everything works correctly, 'float2' will turn green. If it doesn't turn green, that means Unity and Visual Studio aren't talking to each other correctly. Your code will still work. You just won't get the realtime error checking features.*

### Step 3.  Define your Data for Entities in your Game
In traditional Unity, we worked with **Game Objects**. In ECS, we work with **Entities**. Entities are like Game Objects, except they are lightweight, fast, and don't have all the fancy polish Game Objects have. But in defining these, the same rules apply. Environment objects are entities. Characters are entities. Projectiles are enemies. Spawn points, items, and interactables can all be entities (depending on your logic). 

Pick the entities that you need for your alpha version of your game. For each entity, write down the different small pieces of data you need. Also classify your data as one of three types: primitive data, shared data, and Game Object Components.

**Primitive Data** - These include numbers, booleans, and enumerations. Numbers can be integer-based or floating point. Most of your data should be of this type if possible.

**Shared Data** - These are more complex pieces of data that are shared among several entities. These can include database arrays, meshes, materials, tags, ect. *Shared means SHARED! If every entity in your game has unique "shared" data, your game is going to slow way down!*

**Game Object Components** - Only use these if you absolutely NEED them. Every entity that includes these becomes a Game Object, which means they can't be created or destroyed easily on the fly, and you lose a lot of control over them. Entities that are also Game Objects are called **hybrid ECS** entities, compared to **pure ECS** entities that do not have associated Game Objects.

##### Special Cases
**Transforms** - If you realize an entity has become exclusively a Game Object with built-in Unity components only, you might as well use a Transform component. *The only case I can imagine this applying is for an environment piece that has physics and you are using Unity's built-in physics.* Otherwise, you can represent basic transformations using simpler data types in ECS. If you have complicated hierarchies, you might consider using Game Objects. But it is possible to implement a scene graph in ECS. 

**Colliders and Rigidbodies** - If you need advanced physics interactions with bouncing objects, momentum transfer, and explosion forces, you are stuck using Unity's built-in physics solution which is still rooted in GameObject land. However, if your core gameplay is only dependent on collision detection and preventing things from going through other things, you can do these things pretty easily in ECS and probably a lot faster than Unity's built-in solution.

**Audio** - I'm still trying to figure this one out. Worst comes to worst I'll create an audio pool of game objects that play sounds for me at different points in the scene. **Do not use AudioSource.PlayClipAtPoint()! The implementation is horrible! It creates and destroys a Game Object at every call!**

**Rendering** - Unity has a built-in MeshInstancedRenderer and MeshInstancedRendererSystem for handling basic 3D. It does not have many features currently, but it gets the job done without much work. There's an equivalent made by a forum member for 2D which works but isn't optimized. Food Processor was designed with ECS-like concepts in mind. With some work, it can be extended to provide some high-quality graphics in a pure or hybrid ECS environment. I also have a design sketch for an equivalent 2D implementation. If you need an advanced implementation for graphics, let me know and we'll come up with a solution!

#### Example
Since I want my game to eventually support realtime level modifications over a network, I am building my level using pure ECS. So each entity in my environment will have a Position (2D) and an EntityType which is an enumeration I will be using to determine appropriate collision resolution. EntityType is shared, because it is a tag that helps me group objects together and won't change during runtime.

Other optional components include a TransformMatrix, PositionY, and MeshInstanceRenderer for making objects visible and correctly placed in 3D space, and ECSCollider which is a box collider struct for areas that require 2D collision.

A bullet will have a Position, a Velocity, a Direction, a Rebounds count, a Lifetime (for large maps where bullets can go offscreen), an ECSCollider, a PositionY, a PlayerID, an EntityType and a MeshInstanceRenderer.

### Step 4.  Create your Artwork
This artwork does not have to be that good. It could just be placeholder. That's especially true if you are just using simple geometry for colliders. Get the bare minimum amount of artwork you need to create your alpha version and move on.

#### Example
For my tanks game, I only need very simple low poly models. I dit this fairly easily in Blender. In the future I will need some more advanced rendering tech, but for testing gameplay the built-in resources will be sufficient.

![Tank Prototype in Blender](https://github.com/SDSU-GCO/SDSU-GCO.github.io/blob/master/Images/Tank_Model_In_Blender.PNG)

### Step 5.  Convert your Data from Step 3 into Code
You want to create one or more Unity C# scripts for this. The script name does not matter here, so go crazy. Modify your script so that it looks like something like the below snippet. Change the namespace name to something short and sweet, like "GCO", your intitials, or your project's initials. This should be the same for every script you write in your project. *Advanced C#-ers, do your style!*

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.Entities;
using Unity.Mathematics;

namespace GCO
{
  
}
```
#### Example
Here is a script that shows all the syntax and has a bunch of examples! Some come directly from my game. Others are hypothetical use cases. 

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.Entities;
using Unity.Mathematics;

namespace GCO
{
    // Anything that starts with a "//" is a comment and is not treated as code for the remainder of the line. 

    // The general syntax for each piece of data is as follows:

    public struct NameOfDataType : UnityInterface
    {
        // This is where you declare the actual pieces of primitive data. 
        // Normally you only have one piece in these. 
        // But sometimes you need two or more to fully represent the type of data you want.
        public float thisIsAVariableName; // float is a decimal type like 3.3f
        public float anotherAwesomeVariable = 1.0f; // We can provide default values this way. Generally not necessary.

        public int thisVariableHoldsAWholeNumber; // Includes -500, -1, 0, 1, 2, 23, 10000, ect.

        public bool boolCausesBlittableErrors; // Do not use.

        public bool1 useThisInstead = true; // This holds a "true" or "false" value.

        public float2 thisHoldsTwoFloatsInOne;
        public float3 thisHoldsThree = new float3(1.0f, 2.0f, 3.0f); // This is how we provide defaults for complex types.

        public int2 thisHoldsTwoInts;

        // The individual floats in float3 are named x, y, and z in that order. 
        // The individual floats in float4 are named x, y, z, and w in that order.
        // The same rules apply for int3 and int4 as well as bool3 and bool4.

        public float4x4 thisIsAMatrix; // You only care about this if you are building a scene graph.

        // This lets you remember another entity. 
        // However, this isn't as fast as the other types to access.
        // It's still faster than GameObjects, but you don't want to be chasing these down several million times per frame.
        public Entity parentEntity; 

        // There's also fixed size arrays, but I will let you look up the syntax for that one since it is not recommended
        // unless you have a valid use case.
    }

    /******************************************************
    * Primitive Type Examples:
    ******************************************************/
    public struct Position : IComponentData
    {
        public float2 position;
    }

    public struct Velocity : IComponentData
    {
        public float velocity;
    }

    public struct BulletsRemaining : IComponentData
    {
        public int bulletsRemaining;
    }

    // This odd thing in brackets lets this show up in the Unity Editor if I were to use this outside the ECS system.
    [System.Serializable]
    public struct InputData : IComponentData
    {
        public float2 moveInput;
        public float2 cursorPosition;
        public bool1 cursorPositionIsScreenSpacePosition;
        public bool1 fireButtonDown;
        public bool1 fireButtonHeld;
        public bool1 fireButtonReleased;
        public bool1 mineButtonDown;
        public bool1 mineButtonHeld;
        public bool1 mineButtonReleased;
    }

    /******************************************************
    * Shared Types Examples:
    ******************************************************/

    // You have to be very careful with shared data.
    // Every time the shared data changes all the entities using it have all of their data copied to a different location in memory.
    // A way to get around this is to store references to other data containers like ScriptableObjects. 
    // However, those containers should not be created or destroyed on the fly unless they are NativeArrays or other Native types.
    // Another bonus is that these can be quickly filtered based on a certain value very efficiently. So tags and enumerations work well with these.

    // Enumeration example. C# enums don't seem to work here yet.
    public struct EntityTypeValues
    {
        public const int BULLET = 0;
        public const int TANK = 1;
        public const int ENVIRONMENT = 2;
    }

    public struct EntityType : ISharedComponentData
    {
        public int entityType = EntityTypeValues.ENVIRONMENT;
    }

    // Shared data example
    public struct FPMeshInstanceRenderer : ISharedComponentData
    {
        public Mesh sharedMesh;
        public List<Material> geometryMaterials;
        public List<Material> surfaceMaterials;
        public NativeList<FPNoodleData> noodlesTemplate;
        public bool isStatic; // bool is ok in shared components that have reference types in them.
    }

    // Another shared data example that can be created and destroyed on the fly but shouldn't change while in use.
    public struct FPPixelPerfectColliderImage : ISharedComponentData
    {
        public NativeArray<int> pixelsEncodedAsBitmasks;
        public int2 ColliderDimensions;
    }

    // A tag type.
    public struct Dead : ISharedComponentData { }
}
```

## Phase 2


**STOP**

Make sure you have enough of your entities defined and your components typed out. The components don't have to be perfect, but you should have a good list started.

Make sure you have a good amount of artwork to test with.

**Make sure you have a plan for your environment!**
If you are using Unity's built-in physics, you probably can get away with the classical Unity workflow for level design. If you are using Procedural Generation techniques, the structs you defined for entities can be used like normal structs. Then it is just a matter of copying them over into ECS once your algorithm is done.

#### Example
In my case, I am using pure ECS for my level as I might add runtime level modifications features later on. To design my level, I am designing with traditional Unity Game Objects and then flattening the data into ECS using a C# script. I'll post the code once it is finished and working.

### Step 6.  Design your Level
By "level" I am referring to static objects, which also includes things such as spawn positions and the like.

*Tangent: In Running Water back in the day, we encoded not just the collision information but also the spawn information in a texture synced with the level artwork. As the background scrolled, we would scan just a little ways beyond the right of the screen for magic collision pixels and then use that to spawn our animated water and poison droplets.*

Depending on the type of game you are making, how you design your level can be quite unique. For my game, I'm just doing things the old fashioned way and then running a converter script. But for a 2D tile based game, building a tilemap out of a pixel art image in which each pixel represents a tile will provide an interesting and fast workflow. You could also use one of the many tilemap editors out there as long as you are familiar with the editor's output format. 

Todo: Add example with images of level design for Tanks.

### Step 7.  Write Code to Easily Design Entities with Art Assets and Initial Data
Todo: Go into way more detail with examples.

I use Scriptable Objects for this.

### Step 8.  Design your Entities
Todo: Go into way more detail on designs.

This step should include adding artwork via means provided by Step 7.

### Step 9.  Create your Loader Logic
Todo: Go into way more detail on how to load your level and initialize your entities.

### Step 10.  Play Test your Level and Ensure Graphics Load

## Phase 3

### Step 11.  Write your First System

### Step 12.  Handle Input

### Step 13.  Write your Gameplay Systems

### Step 14.  Add Sound

### Step 15.  Build your Game
Make sure your input management has a means to quit the game.

## Phase 4

### Step 16.  Add UI

### Step 17.  Add a Pause Menu

### Step 18.  Add a Title Screen

### Step 19.  Create Scene Navigation

### Step 20.  Build your *Improved* Game

## Phase 5

### Step 21.  Beef Up your Graphics

### Step 22.  Jobify your Game Logic

### Step 23.  Add Customizations and Features

### Step 24.  Iterate and Build and Iterate Some More

### Step 25.  Profit!

