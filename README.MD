# Project Zomboid dedicated server container
The code my docker image and server are based upon is here: https://github.com/Danixu/project-zomboid-server-docker
Please go give them some love!

This Dockerfile downloads the Project Zombie dedicated server using SteamCMD. My image supports all original functions plus adds on some additional QOL features and fixes/refreshes some previously unimplemented features.
I added the following features to this image:
  - I added a DATA_DIR variable that specifies where on the machine Docker will store your servers data.
  - I added an alternative launch which auto-loads custom SandboxVars.lua, spawnpoints.lua, spawnregions.lua, and .ini files from a user filled folder titled custom-settings. To use this, simply make a folder at your specified HOST_DIR (or within the same directory as your docker-compose.yml if unset), name said folder custom-settings, and place your files inside. (overrides PRESET options)
  - I added CUSTOM_INI, CUSTOM_SANDBOXVARS, CUSTOM_SPAWNREGIONS, CUSTOM_INI variables, that way if multiple custom files of a type exist within custom-settings, you can specify the name of the file you want to use. Otherwise the first file of each type in lexical order will be loaded.
  - I finished implementing the LANG variable so that it updates the locale during build time if given a valid UTF-8 language code. This will make your terminal permanently print with that locale as it traditionally would if it was set on a traditional linux.
  - I finished implementing and updated the PORT variables to reflect the standards Build 42 uses. This works by having the server host on the port it defaults to, then your compose file maps Project Zomboids default ports to the ports you specified on your router and firewall.
  - I added a variable for RCON_PORT with identical implementation as above.
  - I added fallback variables so any variable can remain unset.

> **WARNING:** This docker image essentially requires a docker-compose.yml to run and .env file to customize. It's possible to set it up without it, but I'd highly recommend against it.
The ready to use image is available here:

https://hub.docker.com/r/aerialassault/pzserverdocker

> **WARNING:** Once you have pulled the image, please save the docker-compose.yml and .evn.template in this repository to a directory with either empty/non-existent "./data", "./workshop-mods", and "./custom-settings" folders. Alternatively change the HOST_DIR to a relative or static file path that meets this criteria. Once you have done this, configure your environmental variables and save ".env.template" as ".env". After that, simply run docker-compose up.

>**NOTE:** If you want to run multiple instances of this server, simply change the HOST_DIR to a new location. The initialization script searches for the custom-settings directory within the set HOST_DIR path. This means to access the same custom-settings files, you must manually create the custom-settings directory and populate it with those files. You could also just change the servername variable, however I find that this can get messy depending on the mods used as they may change parts of the /data/ folder that may be used by a server without that mod.

> **Note:** If you want to play **Build 42** multiplayer, you must set the `STEAMAPPBRANCH` [environment variable](#environment-variables) to `unstable`.

>**WARNING:** Running the image on Windows using WSL2 in Docker Desktop will make the server startup times significantly slower. This is fine unless you are running alot of mods in which case server startup times may vary from 15 - 40 minutes. The best way to solve this problem is to run your container on a Linux based system or in a Linux VM.



This image uses some env variables as arguments in the server command, allowing to configure it. The image supports the following environment variables:





  **Check https://lh.2xlibre.net/locales/ for up to date language codes. For client translations, checkout https://github.com/TheIndieStone/ProjectZomboidTranslations.**

Changes language of system config before anything is installed.<br>
**LANG:** *en_EN.UTF-8, Only support UTF-8 encodings; 

**WARNING:**Some options must appear in the exact format shown in options for guaranteed compatibility.

***IF AN OPTION IS ITALICIZED, IT MEANS THAT VALUE IS THE HARDCODED DEFAULT WITHIN THE DOCKER-COMPOSE.YML***

## ENVIRONMENTAL VARIABLES

Which branch from https://steamdb.info/app/380870/depots/ to download for your server.<br>
**STEAM_APP_BRANCH:** unstable, outdatedunstable, *public;*

Determines whether the game will boot from steam. If a player isn't using steam to connect, enable this here and in your client.<br>
**NO_STEAM:**  True, *False;*

Option to control where mods are loaded from and the order. Any of the 3 keywords may be left out and may appear in any order.<br>
**MOD_FOLDERS:**  *workshop*,steam,mods (the default is loading from all locations in that order.)

You can choose a different servername by using this option when starting the server.<br>
**SERVER_NAME:**  Any typical valid servername, *servertest*

If more than one file ending with _spawnpoints.lua exists within "./custom-settings" folder, specify file name of which one to load.<br>
**CUSTOM_SPAWNPOINTS:**  Any typical valid file name ending in _spawnpoints.lua, *N/A*; loads in lexical order.

If more than one file ending with _spawnregions.lua exists within "./custom-settings" folder, specify file name of which one to load.<br>
**CUSTOM_SPAWNREGIONS:**  Any typical valid file name ending in _spawnregions.lua, *N/A*; loads in lexical order.

If more than one file ending with _SandboxVars.lua exists within "./custom-settings" folder, specify file name of which one to load.<br>
**CUSTOM_SANDBOXVARS:**  Any typical valid file name ending in _SandboxVars.lua, *N/A*; loads in lexical order.

If more than one file ending with .ini exists within "./custom-settings" folder, specify file name of which one to load.<br>
**CUSTOM_INI:**  Any typical valid file name ending in .ini, *N/A*; loads in lexical order.

Specify which port is forwarded within router and firewall to host server to for UDP.<br>
**DEFAULT_PORT:**  Any UDP port that is unused and that you have forwarded., *16261*

Enable steam anti-cheat.<br>
**STEAM_VAC:**  *True, False*

Changes the username for the admin profile within Project Zomboid.<br>
**ADMIN_USERNAME:**  Any typical valid username, *admin*

Sets/Changes the admin password for the admin profile within Project Zomboid (PLEASE CHANGE THIS). Deletable after first run of new save.<br>
**ADMIN_PASSWORD:**  Any typical valid password, *PleaseChangeThis*

Changes where the server data is stored on local system. This directory MUST be empty and have standard permissions for the active user.<br>
**HOST_DIR:**  Any empty userspace directory, *./server* ***(makes a new folder named "server" in the directory where the docker-compose.yml file is)***

Enables the use of custom init files, disables all preset related settings. Any unspecified files will be generated at runtime with the servers default files.<br>
**ALT_LAUNCH:**  True, *False*

Sets which mods to load and save from Steam Workshop at runtime.<br>
**WORKSHOP_IDS:**  semi-colon separated list of Workshop IDs in load order. ie. "3423660713;3567084868;3406392630", *N/A (dont include quotations from example)*

Specify which port is forwarded within router and firewall to for steam to use for Steam's P2P relay.<br>
**UDP_PORT:**  Any UDP port that is unused and that you have forwarded., *16262*

Overridden by ALT_LAUNCH, sets default preset to use within save.<br>
**SERVER_PRESET:**  *apocalypse, beginner, builder, firstweek, sixmonthslater, survival, survivor.*

Overridden by ALT_LAUNCH, replaces the server preset by the one set by the SERVERPRESET variable. <br>
**SERVER_PRESET_REPLACE:**  True, *False (defaults to false to avoid replacing once server starts)*

Turns off MOD_IDS and WORKSHOP_IDS options to allow you to externally manage your own mods, either through bind mounts or other means.<br>
**SELF_MANAGED_MODS:**  True, *False*

Similar to SERVER_PRESET_REPLACE, replaces the server custom settings with the ones specified by various "CUSTOM_" files above OR files in custom-settings by lexical order.<br>
**REPLACE_CUSTOM:**  True, *False*

Specify which port is forwarded within router and firewall to use RCON relay.<br>
**RCON_PORT:**  Any UDP port that is unused and that you have forwarded., *27015*

Sets/Changes the servers RCON password. (used to access server via consoles that aren't within the game or docker server.)<br>
**RCON_PASSWORD:**  Any typical valid password, *N/A (left unset due to the necessity of the password to be secure)*

Makes the server accessible via the in-game server browser. A good practice is ensure good security before enabling.<br>
**PUBLIC:**  True, *False*

Sets the password required to connect to your server. Optional.<br>
**PASSWORD:**  Any typical valid password, *N/A*

Sets the path for the game data cache directory. An example would be home/zomboid/data/. It's crucial recommended to put this in persistant storage (somewhere in home/steam/Zomboid, home/steam/pz-dedicated/steamapps/workshop, /home/steam/custom-settings) to avoid data loss, if changed<br>
**CACHE_DIR:** Any valid userspace directory on the linux machine, *N/A*

Specifies what local (non workshop) mods to be loaded. Must be placed manually in servers ~/Zomboid/mods/ folder to be detected and loaded.<br>
**MOD_IDS:**  Semicolon+backslashed list (eg. "3423660713;\3567084868;\3406392630"), *N/A*

Sets minimum amount of ram to initialize server with. Falls back to MEMORY if unset.<br>
**MIN_MEMORY:**  RAMSIZE_IN_NUMBERSm, e.g. 2048m, *N/A*

Sets maximum amount of ram that can be accessed if needed. Falls back to MEMORY if unset.<br>
**MAX_MEMORY:**  RAMSIZE_IN_NUMBERSm, e.g. 4096m, *N/A*

Sets total amount of ram used consistently by the server.<br>
**MEMORY:**  RAMSIZE_IN_NUMBERSm, e.g. 4096m, *2048m*

Allows you to use multiple network cards. **Example:** 127.0.0.1<br>
**IP:**  Any valid IP, *N/A*

Forces the server to update to the latest version.<br>
**FORCE_UPDATE:**  True, *False*

Forces the steam client to update to the latest version.<br>
**FORCE_STEAM_CLIENT_UPDATE:**  True, *False*

Sets or changes the display name in the in-game server browser.<br>
**DISPLAY_NAME:**  Any typical valid display name, *PZServer*

Launches the server in debug mode.<br>
**DEBUG:**  True, *False*

Allows your server to be launched in CO_OP mode. Disabled the default admin.<br>
**CO_OP:**  True, *False*

Performs a soft reset on your server. This does many things as detailed below. Use at your own disgression.<br>
**SOFT_RESET:**  True, *False*

## Soft Reset

Soft reset will perform the following tasks:

* Removes items from the ground.
* Removes items from containers and replaces them with new loot. This includes player-made containers.
* Removes corpses and zombified players.
* Resets zombies.
* Removes blood splatter.
* Resets building alarms.
* Resets the game clock. I’m not positive if this resets to day 1, but the original blog post implies that it does. So this would bring water and electricity back. Assuming your server settings have them * available at day 1.
* Player-made buildings won’t be deleted.
* Player inventories won’t reset.

Removing the following files in the Server data folder (ToDo: I have to search the equivalent files in modern version):

* all zombie_X_Y.bin files
* map_t.bin
* map_meta.bin
* reanimated.bin

Currently is not working correctly and will cause the server to crash when it is active. Also the above points are not performed so nothign will change in the server.

## Required ports

The required ports are docummented in the [Official wiki](https://pzwiki.net/wiki/Dedicated_Server#Forwarding_Required_Ports)

For now the ports are the following, but it can change.

### Steam clients ports

This ports are used by the Steam clients to connect with the server, if no manual mapping, these must be opened in firewall and configured in your router.

* 16262 UDP
* 16261 UDP (configurable)

### Non Steam clients ports

This ports are used by the No Steam clients to connect with the server, so it must be opened in firewall and configured in NAT.

* 16262 UDP (configurable)
* 16261 UDP (configurable)

### RCON Port

This port is used to edit your server from an RCON terminal.

*27015 (configurable)

### Notes

* The 16261 port can be changed using the PORT environment variable. This will affect at the TCP ports range, changing it to the next port. For example, if PORT is set to 12234, the TCP range will be 12234 - 12244.
* The TCP ports range is used by the clients, so every client slot will need a port. If your server plans to host 50 clients, then you will need to open 50 ports in firewall/nat... (16262 - 16312).
* The Wiki says that Non Steam ports is supposed to be for non steam clients, but I am using the steam client and those ports are used too.

## How to use the image

To setup the server you can use the docker-compose program:

* Copy the .env.template to .env and edit it with the settings you want. The only mandatory variable is ADMINPASSWORD and just the first time the server is executed (once is started at least once, it can be removed). The other variables are optional.
* Run the `docker-compose up -d` command to start the server.

## Scripts folder

In the scripts folders there are three scripts:

* entry.sh: The script used in the image to start the server
* search_folder.sh: Script to search through mod folders for any maps
* get_updates.sh: will perform a search of the latest version on the forum and the website to download and build the latest stable and unstable version. The local image will be created and it will try to upload the image to the Dockerhub website, but it will fail by the lack of permissions.
