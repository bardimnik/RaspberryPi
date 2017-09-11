# Raspberry Pi

## Minecraft

### What is it about
- Setup a lightweight Minecraft server for up to 4 people

### Instructions
1. Install the following packages with:
   
   ```shell
   sudo apt-get update
   sudo apt-get install netatalk screen avahi-daemon
   ```
   
2. Download the latest version of Java JDK 8
   - Download it from [oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) with a browser
   - Choose the ARM 32 bit Hard Float ABI architecture
   - Rename the downloaded archive to jdk8.tar.gz and move it to ~/
   
3. Install Java 8:
   - Move the archive to /usr/java and extract it with:
   
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
   
5. Download **Spigotmc** from linuxnorth.org
   
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
   sudo rm server.properties && sudo nano server.properties
   ```
   
   and paste the following content in the editor:
   
   ```ini
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

9. Start the server again with:

   ```shell
   java -jar -Xms384M -Xmx800M spigot-1.12.1.jar nogui
   ```
   
10. For better performance:
   - Download the **NoSpawnChunks** plugin from [https://dev.bukkit.org/projects/nospawnchunks/files](https://dev.bukkit.org/projects/nospawnchunks/files)
     and move the **jar** file to *~/ftp/drive/minecraft/plugins
   

