# Polycosmos
Polycosmos is a mod for the game Hades, which gives it integration with Archipelago Multiworld. Right now Polycosmos is in version: 
0.2.2 and up to this version it posses the feature detailed below:

Modes:
- Reverse Heat: Start the game with high pacts of punishments. The goal is to beat Hades one time.
  
Items:
- Pact of Punishment down: an item that turns down the level of pact of punishment. Note the game may load this effect
on the run room, biome or run.

- Keys, Darkness, Gemstones, Diamonds, Titan Blood, Nectar and Ambrosia as filler items.

Locations:
- Rooms: Beating any room counts as a location for the AP.

Settings:
-Number of Pact of Punishments: tweak how high each pact of punishment starts, and so how many of this items are in the pool.
-Value of filler items: tweak how much currency each filler item gives you. Can use to turn off filler items

# Requirements
- Have Hades installed (duh!) and [ModImporter](https://github.com/SGG-Modding/ModImporter/releases/tag/1.5.2), put modimporter.exe in your Hades/Content folder.
- Have [ModUtils](https://github.com/SGG-Modding/ModUtil) and [StyxScribe](https://github.com/SGG-Modding/StyxScribe) installed. Make sure this works in your computer (by executing SubsumeHades.py in Hades content folder). (To install ModUtils put the contents on Hades/Content/Mods/ModsUtils and open modimporter.exe. For StyxScribe follow the instructions in the repo). 
- Now you can use the modimporter.exe to install the Polycosmos mod folder in this repository (put the Polycosmos folder in Content/Mods folder and open modimporter.exe).
- Get your [source code Archipelago folder](https://github.com/ArchipelagoMW/Archipelago) and on it copy HadesClient.py on the base folder (where all the Clients.py are) and copy the hades folder in the worlds folder.
- After doing this you should be able to generate and play multiword with Hades! To start you can use the template yaml in the hades world folder, which also includes some explanation of the settings.

NOTE: up to the time of writing this mod does not warrantee any type of compatibility with other Hades mods. You have been warned!

# How to use Polycosmos

- To use Polycosmos execute the HadesClient.py/HadesClient.exe in your Archipelago folder. If everything is working correctly this should open a window to search for your Hades base folder (the standard steam path being C:\Program Files\Steam\steamapps\common\Hades ). Select that folder and this should open Hades, the Archipelago client plus a command terminal. This terminal is what communicates Hades and the Client, SO DO NOT CLOSE IT!
- Before starting a file, connect to your Archipelago server using the client as you would do with any other AP game. Play the game and have fun ;).

# Credits

Everyone at the Hades modding discord. They have been a massive help. Especially Magic_Gonads and PonyWarrior for answering my pestering questions.

The AP discord and all the people in the Hades subthread which have pitch in with ideas and help keep me motivated. That includes, but is not limited to, DoesBoKnow for proposing this for the multiworld and providing a ton of resources and testing and Flore for proposing the “reversed heat” idea (which was simple enough to start implementing almost right away, which made this much more bearable).

# Bugs 

A known issue is that some changes in heat level only take effect when starting the next room, biome or run starts. That is how Hades work and not much we can do about that.

You see some weird debug messages while playing with keyboard and mouse. To avoid this go to your main Hades folder, and in there open StyxScribe.py. Go to line 26 and in there change "DebugDraw=true" to "DebugDraw=false". In a future version this will be disabled permanently.

Any other bug is not expected and reporting helps a ton :).

# Incoming features

This is a list of features that are planned for this mod.

- Making a "score based" location system (and not a "depth based" one, like it is now).

- Making weapons an item, and give the ability to start with any given weapon.

- Make the ability of choosing a particular boon a check. Put this Boon traits into the item pool and allow a menu that gives them to the player (Similar to CodexMenu mod)

- Choosing a starting weapon and make other unlockable

- Choosing how many runs victories are required to beat the mod.

- Make better compatibility with the pact of punishment window and this mod (so you can choose your heat level if you have enough pact levels).

# How this mod works

There might be the case in which you want to collaborate to improve this multiworld or want to adapt this to bring other game to Archipelago.
If that is the case; great! You can always put in contact with me at my discord. In any case, here is a broad overview of how this mod is set up.

First, there are 3 ingredients; Polycosmos mod, StyxScribe and the ArchipelagoClient. In a broad way; the Polycosmos
mod is what can directly influence the game (give Zag items, record when locations have been reached, etc.). The Archipelago Client
is what Communicate with the AP Server, and can communicate messages to other clients (for example "HadesPlayer reached a location" or "HadesPlayer have received an item").
The StyxScribe is what can communicate the Polycosmos mod with the ArchipleagoCLient.

- Polycosmos mod works like a standard Hades mod. It is written in .lua with some stripped down capabilities (in particular no access to
"require" or related commands). Up to the time of 0.2.2 it is compromised of the following modules:

PolycosmosEvents: reacts to certain important events in the game (location reached, game loaded) by notifying other modules.
PolycosmosHeatManager: manages the current Heat level according to the settings and items it recieves
PolycosmosItemManager: manages the reception of filler items
PolycosmosMessages: It is the module that prints messages to the player.
PolycosmosUtils: Contains functions that are useful to manage or parse data, but are agnostic to the mod structure, so do not fit in any other library.

Note that while some modules could be mashed together, this different functionalities have been split to be able to growth this in the most modular way possible.

- StyxScribe is a Hades mod that allow communication between .lua and .py modules. It uses a Hook system with strings;
ie, Allows to execute certain functions in the modules each time a message with a certain prefix have been sent in the console.
Is that by using this hooks that can communicate between Polycosmos and the Archipelago Client.

As a side note, you might be considering why we even use StyxScribe and not import a .dll to use for the APClient (which already
exists for game running in .lua). The reason is simple: Hades run on a lua compiler that does not allow manul import of external files.
It why we use StyxScribe; to bypass this limitation as painlessly as possible. If other ways are found to also
overcome this, another implementation of this mod might be possible which directly implements the client or other functionality inside Hades
itself.

- Archipelago Client is a .py app that can communicate with an Archipelago server to send and recieve items. This server also
deals with game randomization (ie, what item correspond to which other player item).

The current implementation of the Client differentiate mainly in that it import StyxScribe, creates an instance of its main class,
and then add all the hook it needs to communicate with StyxScribe. Finally it uses another thread to initiliazes the game+StyxScribe in parallel to it. This is how the Client automatically opens the game and set up communication to it. It also set ups the settings from the .yalm in a StyxScribe.Shared.Root state, which then the game can read
to gather information about the settings the player chose. Beside this, the client should not present other main difference with other AP clients.
