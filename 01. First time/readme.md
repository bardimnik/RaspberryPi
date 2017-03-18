# Raspberry Pi

## First time

### What is it about
- Setup the time
- Setup passwords
- Setup basic SSH access
- Upgrade and update your Pi 

### Instructions
1. Username and password are **pi** and **raspberry** by default
2. Change the date and password for user *pi* with:

   ```bash
   sudo raspi-config
   ```
   
    - *Change User Password* and enter your new password twice
    - *Localisation Options*, *Change timezone*, then choose your timezone 
3. Set up the password for root with:

   ```bash
   sudo passwd root
   ```
   
4. Launch ssh for remote access with

   ```bash
   sudo service ssh start
   ```
   
5. Enter and note your LAN IP address (usually 192.168.1.XXX):

   ```bash
   ifconfig
   ```
   
6. On your computer, generate a RSA key pair if you did not already.
    - You need **ssh-keygen**.
    - On Windows only:
        - Simply download and install [**Git**](https://www.git-scm.com/downloads) 
        - Add *C:\Program Files\Git\usr\bin* to your path or `C: && cd C:\Program Files\Git\usr\bin`
    - Generate the RSA key pair and enter a password to protect it
    
      ```bash
      ssh-keygen
      ```
      
7. On your computer, enter the following and replace the XXX with your 
   Raspberry Pi's LAN IP address:
   
    ```bash
    cat ~/.ssh/id_rsa.pub | ssh pi@192.168.1.XXX "mkdir -p ~/.ssh && cat >>  ~/.ssh/authorized_keys"
    ```
    
    - Answer **yes** for the host's authenticity establishment
    - Input the username pi's password you have setup previously.
    - Your SSH public key is now registered in your Raspberry Pi.
8. Remotely log in to your Raspberry Pi with

   ```bash
   ssh pi@192.168.1.XXX
   ```
   
   And enter your SSH key's password when prompted.
9. Finally, upgrade the OS and update its repositories with:
   ```bash
   sudo apt-get update
   sudo apt-get upgrade
   ```