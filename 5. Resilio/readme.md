# Raspberry Pi

## Resilio

### What is it about
- Configure Resilio
- Make it start at boot
- Start it now

### Instructions
1. Create a shared resilio directory with:

   ```shell
   mkdir ~/ftp/drive/resilio
   ```

2. Create a hidden resilio configuration directory with:

   ```shell
   mkdir ~/.resilio
   cd ~/.resilio
   ```

3. Download Resilio for ARM with:

   ```shell
   wget https://download-cdn.resilio.com/stable/linux-arm/resilio-sync_arm.tar.gz
   ```

4. Extract it with:

   ```shell
   tar -xf resilio-sync_arm.tar.gz rslsync
   ```

5. Remove the archive with:

   ```shell
   rm ~/.resilio/resilio-sync_arm.tar.gz
   ```

6. Create the configuration file with:
   
   ```shell
   nano ~/.resilio/resilio.conf
   ```
   
   and enter the following (AND CHANGE THE **PASSWORD**)

   ```json
   {
       "device_name": "Raspberry Pi",
       "listening_port" : 18952, //0 - randomize port
       "pid_file" : "/var/run/btsync.pid",
       "use_upnp" : true, //UPnP for port mapping
       "download_limit" : 0,
       "upload_limit" : 0,
       // "proxy_type" : "socks4", //or "socks5", "http_connect"
       // "proxy_addr" : "192.168.1.2",
       // "proxy_port" : 1080,
       // "proxy_auth" : false, //Use authentication for proxy. Note: only username/password for socks5 (RFC 1929) is supported, and it is not really secure
       // "proxy_username" : "user",
       // "proxy_password" : "password",
       "directory_root" : "~/ftp/drive/resilio/",
       "directory_root_policy" : "all", //or "belowroot"
       "webui" :
       {
           "listen" : "0.0.0.0:18953", //remove field to disable WebUI
           "login" : "admin",
           "password" : "password", //CHANGE THAT
           "allow_empty_password" : false
           //,"force_https" : true // disable http
           //,"ssl_certificate" : "/path/to/cert.pem"
           //,"ssl_private_key" : "/path/to/private.key"
           //,"dir_whitelist" : [ "/home/user/MySharedFolders/personal", "work" ]
       }
       //see more at "http://help.getsync.com/customer/portal/articles/1902048-sync-advanced-preferences--more-options"
   }
   ```

7. Create the boot script with:

   ```shell
   sudo nano /etc/init.d/rslsync
   ```
   
   and enter the following:
   
   ```shell
   #!/bin/sh
   
   ### BEGIN INIT INFO
   # Provides:          rslsync
   # Required-Start:
   # Required-Stop:
   # Should-Start:
   # Should-Stop:
   # Default-Start:     2 3 4 5
   # Default-Stop:      0 1 6
   # Short-Description: Resilio service, for boot
   # Description: This script will start Resilio (rslsync) at boot
   ### END INIT INFO
   
   # Carry out specific functions when asked to by the system
   case "$1" in
   start)
       ~/.resilio/rslsync --config ~/.resilio/resilio.conf
       ;;
   stop)
       killall resilio
       ;;
   *)
       echo "Usage: /etc/init.d/rslsync {start|stop}"
       exit 1
       ;;
   esac
   
   exit 0
   ```
   
8. Make that script executable with:

   ```shell
   sudo chmod +x /etc/init.d/rslsync
   ```

9. Add it to the boot process with:

   ```shell
   sudo update-rc.d rslsync defaults
   ```

10. Launch it now with:

    ```shell
    sudo ./~/.resilio/rslsync --config ~/.resilio/resilio.conf
    ```
    
11. Terminate Resilio with:

    ```shell
    sudo killall rslsync
    ```