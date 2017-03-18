# Raspberry Pi

## Hard drive

### What is it about
- Mount a hard drive at boot
- Mount a FAT/NTFS hard drive now

### Instructions
1. Create a directory to mount the hard drive with:

   ```shell
   mkdir ~/ftp/drive
   ```
   
2. Edit the file system table with:

   ```shell
   sudo nano /etc/fstab
   ```

3. And add the following:
    - For NTFS drives:
    
      ```shell
      /dev/sda1 ~/ftp/drive  ntfs-3g    user,umask=0000   0       0
      ```
      
    - For FAT drives:
      
      ```shell
      /dev/sda1 ~/ftp/drive  vfat    user,umask=0000   0       0
      ```
      
4. Mount it now with:
   - For NTFS drives:

     ```shell
     sudo apt-get -y install ntfs-3g
     sudo mount /dev/sda1 ~/ftp/drive -o umask=0000
     ```
     
   - For FAT drives:
     
     ```shell
     sudo mount -t vfat /dev/sda1 ~/ftp/drive -o umask=0000
     ```

5. After that your Pi won't boot up correctly if the hard drive is not connected...
   You would have to change *fstab* back so that it boots fine.
