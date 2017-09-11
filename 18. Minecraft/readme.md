# Raspberry Pi

## Minecraft

### What is it about
- Setup a lightweight Minecraft server for up to 4 people

### Instructions
1. Install the **screen** packages with:
   
   ```shell
   sudo apt-get update
   sudo apt-get install screen
   ```
   
2. Download the latest version of **Java 8**
   - Download it from [**Oracle**](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) with a browser
   - Choose the ARM 32 bit Hard Float ABI architecture
   - Rename the downloaded archive to *jdk8.tar.gz* and move it to `~/`
   
3. Install Java 8:
   - Move *jdk8.tar.gz* and extract it with:
   
      ```shell
	  sudo mkdir /usr/java
	  sudo mv ~/jdk8.tar.gz /usr/java/
	  sudo tar xf jdk8.tar.gz
	  sudo rm jdk8.tar.gz
	  ```
	  
   - Create symbolic links for java with:
  
      ```shell
      sudo update-alternatives --install /usr/bin/java java /usr/java/jdk1.8.0_111/bin/java 1000	  
	  sudo update-alternatives --install /usr/bin/javac javac /usr/java/jdk1.8.0_111/bin/javac 1000
	  ```
	  
   - Verify the Java version is correct with:
   
      ```shell
	  java -version
	  ```
	  
4. Create a directory for minecraft, preferrably on a faster hard drive:

   ```shell
   sudo mkdir ~/ftp/drive/minecraft
   sudo chmod 777 ~/ftp/drive/minecraft
   cd ~/ftp/drive/minecraft
   ```
   
5. Build **Spigotmc**
   - By building from source (safer):
      1. Download from [spigotmc.org](https://hub.spigotmc.org) with:
	     
		 ```shell
		 wget https://hub.spigotmc.org/jenkins/job/BuildTools/lastSuccessfulBuild/artifact/target/BuildTools.jar
		 ```
		 
      2. Set your git with: 
	  
	     ```shell
		 git config --global --unset core.autocrlf
		 ```
		 
      3. Build it with Java 8 (**takes about 1 hour**) in the background with:
	  
	     ```shell
		 sudo screen -S mcbuilder -d -m java -jar BuildTools.jar --rev latest
		 ```
		 
		 Check it with:
		 
		 ```shell
		 screen -r mcbuilder
		 ```
		 
		 Detach from it with **CTRL**+**A** - **D**
		 
      4. Once done, you can delete all the files except **spigot-1.12.1.jar** with:
	   
	     ```shell
		 sudo mv spigot-1.12.1.jar ../spigot-1.12.1.jar
		 sudo rm -fr *
		 sudo mv ../spigot-1.12.1.jar ./spigot-1.12.1.jar
		 ```
		 
   - Download it from [linuxnorth.org](https://www.linuxnorth.org/minecraft/download/) with:
   
      ```shell
	  wget https://www.linuxnorth.org/minecraft/download/spigot-1.12.1.jar
	  ```
   
6. Start the server with:

   ```shell
   java -jar -Xms384M -Xmx800M spigot-1.12.1.jar nogui
   ```
   
7. Edit *eula.txt* by changing **eula=false** to **eula=true** with:

   ```shell
   sudo nano eula.txt
   ```
   
8. Enter the command below:
   
   ```shell
   sudo rm server.properties
   sudo nano server.properties
   ```
   
   and paste the following content in the editor:
   
   ```
#Minecraft server properties
#Mon Sep 11 00:39:19 EDT 2017
generator-settings=
op-permission-level=4
allow-nether=true
level-name=world
allow-flight=false
prevent-proxy-connections=false
server-port=25565
max-world-size=29999984
level-type=DEFAULT
level-seed=
force-gamemode=true
server-ip=
network-compression-threshold=256
max-build-height=256
spawn-npcs=true
white-list=false
spawn-animals=true
hardcore=false
snooper-enabled=true
resource-pack-sha1=
online-mode=true
resource-pack=
pvp=true
difficulty=2
enable-command-block=false
gamemode=0
player-idle-timeout=0
max-players=6
spawn-monsters=true
generate-structures=true
view-distance=5
motd=Spigot Minecraft RPI
   ```

9. For better performance, install the **NoSpawnChunks** plugin
   1. Download manually the **NoSpawnChunks** plugin from [https://dev.bukkit.org/projects/nospawnchunks/files](https://dev.bukkit.org/projects/nospawnchunks/files)
   2. Move the **jar** file to *~/ftp/drive/minecraft/plugins/
   
10. Start the server again but in the background with:

   ```shell
   screen -S minecraft -d -m java -jar -Xms384M -Xmx800M spigot-1.12.1.jar nogui
   ```
   
11. Stop the server with:

   ```shell
   screen -r minecraft
   stop
   ```
   
   And kill the screen with **CTRL**+**A**+**K**

