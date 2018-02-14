# Raspberry Pi

## Hard drive

### What is it about

- Mount a FAT/NTFS hard drive
- Unmount it
- Make it spin down when unused

### Instructions

1. Create a directory to mount the hard drive with:
    ```shell
    mkdir ~/ftp/drive
    ```
1. Find out the drive (i.e. sda1) you want to mount with:
    ```shell
    sudo blkid
    ```
1. Mount it with:
   - For NTFS drives:
     ```shell
     sudo apt-get install ntfs-3g -y
     sudo mount /dev/sda1 ~/ftp/drive -o umask=0000
     ```
   - For FAT drives:
     ```shell
     sudo mount -t vfat /dev/sda1 ~/ftp/drive -o umask=0000
     ```
1. Unmount it if you want to with:
    ```shell
    sudo umount ~/ftp/drive/
    ```
1. Make the *sda1* drive i.e. spin down when unused with:
    ```shell
    sudo apt-get install hdparm -y
    sudo nano /etc/hdparm.conf    
    ```
    Add the following in the file:
    ```
    /dev/sda1 {
    write_cache = on
    spindown_time = 120
    }
    ```
    Then restart the service:
    ```shell
    sudo service hdparm restart
    ```
    More information at [htpcguides.com](https://www.htpcguides.com/spin-down-and-manage-hard-drive-power-on-raspberry-pi/)