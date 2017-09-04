# Raspberry Pi

## Plex

### What is it about
- Install Plex Media Server on Raspberry Pi with a hard drive
- **WARNING**: The Raspberry Pi might be not powerful enough to run Plex smoothly, so you might want to skip this part.

### Instructions
1. Install the HTTPS transport package with:

   ```shell
   sudo apt-get install -y apt-transport-https
   ```
   
2. Add a crypt o key for the *dev2day* website to the keyring with:

   ```shell
   wget -O - https://dev2day.de/pms/dev2day-pms.gpg.key | sudo apt-key add -
   ```
   
3. Add *dev2day* repository to the package source list with:

   ```shell
   sudo echo "deb https://dev2day.de/pms/ jessie main" | sudo tee /etc/apt/sources.list.d/pms.list
   ```
   
4. Update the package list with:

   ```shell
   sudo apt-get update
   ```

5. Install the **Plex** media server with:

   ```shell
   sudo apt-get install -y -t jessie plexmediaserver
   ```

6. Change the username of the server with:

   ```shell
   sudo nano /etc/default/plexmediaserver
   ```
   
   And change the user from **plex** to **pi**.
   
7. Restart the **Plex** Media server with:
  
   ```shell
   sudo service plexmediaserver restart
   ```
   
8. Access the Plex media server at *192.168.XXX.XXX:32400/web/*

9. Sign in/Sign up and then click on **Add library** and add your media content.
