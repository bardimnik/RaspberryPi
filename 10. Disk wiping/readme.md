# Raspberry Pi

## Disk wiping

### What is it about
- Wipe securely a hard drive
- Access the Pi with SSH and fork the disk wiping in the background with *screen*

### Instructions
1. Install **screen** to fork a terminal in the background with:

   ```shell
    sudo apt-get install screen
   ```
   
2. Find the *right* hard drive by entering:

   ```shell
   sudo fdisk -l
   ```
   
   It should be something like ```/dev/sda``` (**not** ```/dev/sda1```)
   
3. Start a screen named *wiper* with:

   ```shell
   screen -S wiper -h 100000
   ```
   
   You will automatically be logged in the background terminal *wiper*.
   ```-h 100000``` specifies the number of lines in the scrollback buffer, which can then be accessed with **CTRL-A + [**.
   
4. Enter the following to securely wipe the drive **sda**:

   ```shell
   sudo shred -n 2 -z -v /dev/sda
   ```
   
   This wipes the disk n=**2** times, resets all the bits to zero at the end and is verbose.
   
5. You can now detach from the *wiper* screen with **CTRL-A + D** and even close the original terminal

6. Check the name of your running screens with:

   ```shell
   screen -ls
   ```
   
7. Reconnect to the *wiper* screen with:

   ```shell
   screen -r wiper
   ```

8. Once the wiping is finished, you can kill the *wiper* screen with **CTRL-A + K** or with *exit*