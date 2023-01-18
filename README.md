# GodotSteam Server for Godot 3.x
An open-source and fully functional Steamworks server SDK / API module and plug-in for the Godot Game Engine (version 3.x). For the Windows, Linux, and Mac platforms. 

Additional flavors include:
- [Godot 2.x](https://github.com/Gramps/GodotSteam/tree/godot2)
- [Godot 3.x](https://github.com/Gramps/GodotSteam/tree/master)
- [Godot 4.x](https://github.com/Gramps/GodotSteam/tree/godot4)
- [GDNative](https://github.com/Gramps/GodotSteam/tree/gdnative)
- [GDExtension](https://github.com/Gramps/GodotSteam/tree/gdextension)

Documentation
----------
[Documentation is available here](https://godotsteam.com/). You can also check out the Search Help section inside Godot Engine after compiling it with GodotSteam Server.

Feel free to chat with us about GodotSteam on the [CoaguCo Discord server](https://discord.gg/SJRSq6K).

Current Build
----------
You can [download pre-compiled versions _(currently v2.0.1)_ of this repo here](https://github.com/Gramps/GodotSteam/releases).

**Version 2.0.1 Changes**
- Changed: layout to make Git cloning easier
- Fixed getSessionConnectionInfo using old networking struct
- Removed: unused networking stricts

[You can read more change-logs here](https://godotsteam.com/changelog/server/).

Known Issues
----------
- **Using MinGW causes crashes.** I strong recommend you **do not use MinGW** to compile at this time.

"Quick" How-To
----------
#### 1a. Downloading
By far the easiest way to use GodotSteam is to download our pre-compiled editors and templates; especially good for folks who don't want to set up the tools for compiling and just want to get going.
- Download the [pre-compiled editor from the Release section](https://github.com/Gramps/GodotSteam/releases) and unpack it.
  - Users on Linux may have issues with the libsteam_api.so, if so then [read our Linux Caveats doc](https://godotsteam.com/tutorials/linux_caveats/).

At this point you can skip all the following steps and check our our tutorials to learn more about integrating Steamworks or just explore the SDK!

#### 1b. Compile Yourself
For those of you who are comfortable compiling or want to give it a shot, here are some steps to follow.
- Set your system up for [compiling based on Godot's recommendations / required tools.](https://docs.godotengine.org/en/stable/development/compiling/index.html)
- Download and unpack the [Godot source 3.x](https://github.com/godotengine/godot).
- Acquire the GodotSteam Server source either by downloading it here or cloning the repo:
  - Download this repository then unpack it into a folder called **godotsteam_server** inside your Godot Engine source code **/modules** folder.
    - You will have to create the **godotsteam_server** folder and it must be named exactly this.
  - Alternatively, clone this repository into your Godot Engine source code **/modules** folder
    - Use ````git clone -b server https://github.com/Gramps/GodotSteam.git godotsteam_server````
- Download and unpack the [Steamworks SDK 1.55](https://partner.steamgames.com).
  - This requires a Steam developer account.
  - Please see "Known Issues" above about versions.

#### 2. Setting Up the SDK
Move the following from the unzipped Steamworks SDK to the **/modules/godotsteam_server/sdk/** folder:
````
    sdk/public/
    sdk/redistributable_bin/
````

#### 3. Double-Checking Folder / File Structure
The repo's directory contents should now look like this:
````
    godotsteam_server/sdk/public/*
    godotsteam_server/sdk/redistributable_bin/*
    godotsteam_server/SCsub
    godotsteam_server/config.py
    godotsteam_server/godotsteam.cpp
    godotsteam_server/godotsteam.h
    godotsteam_server/register_types.cpp
    godotsteam_server/register_types.h
````

You can also just put the godotsteam_server directory where ever you like and just apply the ````custom_modules=.../path/to/dir/godotsteam_server```` flag in SCONS when compiling.  Make sure the ````custom_modules=```` flag points to where the godotsteam_server folder is located.

#### 4. Compiling Time
Recompile for your platform:
  - For headless build with editor functionality: ````scons platform=server production=yes tools=yes target=release_debug````
  - For debug server: ````scons platform=server production=yes tools=no target=release_debug````
  - For optimized server: ````scons platform=server production=yes tools=no target=release````

Some things to be aware of:
- If using Ubuntu 16.10 or higher and having issues with PIE security in GCC, use ````LINKFLAGS='-no-pie'```` to get an executable instead of a shared library.
- When creating templates for OSX, please refer to this post for assistance as the documentation is a bit lacking ( http://steamcommunity.com/app/404790/discussions/0/364042703865087202/ ).

#### 5. Altogether Now
When recompiling the engine is finished, do the following before running it the first time:
  - Copy the shared library (steam_api), for your OS, from sdk/redistributable_bin/ folders to the Godot binary location (by default in the godot source /bin/ file but you can move them to a new folder).
    - These files are called **steam_api.dll, steam_api64.dll, libsteam_api.so, or libsteam_api.dylib**; no other files are needed.
  - Create a **steam_appid.txt** file with your game's app ID or 480 in this folder.  Necessary if the editor or game is run _outside_ of Steam.

The finished hierarchy should look like this (if you downloaded the pre-compiles, the editor files go in place of these tools files, which are the same thing):
  - Linux 32/64-bit
  ```
  libsteam_api.so
  steam_appid.txt
  ./godot.linux.tools.32 or ./godot.linux.tools.64
  ```
  - MacOS
  ```
  libsteam_api.dylib
  steam_appid.txt
  ./godot.osx.tools.32 or ./godot.osx.tools.64
  ```
  - Windows 32-bit
  ```
  steam_api.dll
  steam_appid.txt
  ./godot.windows.tools.32.exe
  ```
  - Windows 64-bit
  ```
  steam_api64.dll
  steam_appid.txt
  ./godot.windows.tools.64.exe
  ```

Lack of the **Steam API .dll/.so/.dylib**, for your respective OS, or the **steam_appid.txt** will cause the editor or game fail and crash when testing or running the game _outside_ of the Steam client.
- **NOTE:** Some people report putting the Steam API file inside their project folder fixes some issues.
- **NOTE:** For MacOS, the libsteam_api.dylib and steam_appid.txt must be in the Content/MacOS/ folder in your application zip or the game will crash.
- **NOTE:** For Linux, you may have to load the overlay library for Steam overlay to work:
  ```
  export LD_PRELOAD=~/.local/share/Steam/ubuntu12_32/gameoverlayrenderer.so;~/.local/share/Steam/ubuntu12_64/gameoverlayrenderer.so
  
  or 
  
  export LD_PRELOAD=~/.local/share/Steam/ubuntu12_32/gameoverlayrenderer.so;
  export LD_PRELOAD=~/.local/share/Steam/ubuntu12_64/gameoverlayrenderer.so;
  ```
  This can be done in an .sh file that runs these before running your executable.  This issue may not arise for all users and can also just be done by the user in a terminal separately.  You can [read more about it in our Linux Caveats doc](https://godotsteam.com/tutorials/linux_caveats/).

#### 6. Good To Go

From here you should be able to call various functions of Steamworks. You should be able to look up the functions in Godot itself under the search section. In addition, you should be able to [read the Steamworks API documentation](https://partner.steamgames.com/doc/home) to see what all is available and [cross-reference with GodotSteam's documentation](https://godotsteam.com/).

#### 7. Shipping Your Game
When uploading your game to Steam, you _**must**_ upload your game's executable and **Steam API .dll/.so/.dylb** (steam_api.dll, steam_api64.dll, libsteam_api.dylib, and/or libsteam_api.so).  *Do not* include the steam_appid.txt or any .lib files as they are unnecessary; however, they won't hurt anything.

Donate
-------------
Pull-requests are the best way to help the project out but you can also donate through [Github Sponsors](https://github.com/sponsors/Gramps) or [Paypal](https://www.paypal.me/sithlordkyle)!

License
-------------
MIT license
