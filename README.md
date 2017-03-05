# Raspberry Pi

## 1. First run: *Time*, *passwords* and *simple SSH remote access*
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

## 2. Advanced *SSH* and *SFTP*
1. Make ssh start at boot automatically with:

   ```bash
   sudo touch /boot/ssh
   ```
   
2. For more devices to access your Pi via SSH
    - Enter:
    
    ```bash
    sudo nano ~/.ssh/authorized_keys
    ```
    
    - Save the public keys similarly to this:
    
      ```
      ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAA.... quentin.mcgaw@gmail.com
      ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAA.... Otherapplication
      ...
      ```
      
2. Change permissions for security purposes:

   ```bash
   sudo chmod 700 ~/.ssh/
   ```
   
3. Change permissions for security purposes:

   ```bash
   sudo chmod 600 ~/.ssh/authorized_keys
   ```
   
4. Change the SSH configuration with:

   ```bash
   sudo nano /etc/ssh/sshd_config
   ```
   
   and replace everything with:
   
   ```
   Port 37032
   # Use these options to restrict which interfaces/protocols sshd will bind to
   #ListenAddress ::
   #ListenAddress 0.0.0.0
   Protocol 2
   HostKey /etc/ssh/ssh_host_rsa_key
   HostKey /etc/ssh/ssh_host_dsa_key
   HostKey /etc/ssh/ssh_host_ecdsa_key
   HostKey /etc/ssh/ssh_host_ed25519_key
   UsePrivilegeSeparation yes
   KeyRegenerationInterval 3600
   ServerKeyBits 1024
   SyslogFacility AUTH
   LogLevel INFO
   LoginGraceTime 120
   PermitRootLogin without-password
   StrictModes yes
   RSAAuthentication yes
   PubkeyAuthentication yes
   #AuthorizedKeysFile     %h/.ssh/authorized_keys
   IgnoreRhosts yes
   RhostsRSAAuthentication no
   HostbasedAuthentication no
   PermitEmptyPasswords no
   ChallengeResponseAuthentication no
   PasswordAuthentication no
   ```
5. Load the new configuration and restart the service ssh

   ```bash
   sudo service ssh restart   
   ```
   
6. You will then be able to access your Raspberry Pi with:

   ```bash
   ssh pi@192.168.1.XXX -p 37032
   ```
   
   and only with the SSH key pairs registered.

## *Install* and *update* some packages
1. Update your list of programs etc with:

   ```bash
   sudo apt-get update
   ```

2. Upgrade your system with:

   ```bash
   sudo apt-get upgrade
   ```

3. Install some packages

    ```bash
    sudo apt-get -y install htop git vsftpd zip openvpn python-pip deluged deluge-console python-mako deluge-web
    ```


## FTP for LAN
1. Create an ftp directory with:
   
   ```bash
   mkdir ~/ftp
   ```
   
2. Change permissions with:

   ```bash
   sudo chmod 700 ~/ftp
   ```
   
3. Enter this to change ownership (not necessary maybe) and change USERNAME to yours (or **pi**):

   ```bash
   sudo chown -R USERNAME:pi ~/ftp
   ```
   
4. Edit the FTP configuration file with:

   ```shell
   sudo nano /etc/vsftpd.conf
   ```
   
   and replace everything with:
   
   ```shell
   listen=NO
   listen_ipv6=YES
   anonymous_enable=NO
   local_enable=YES
   write_enable=YES
   local_umask=0002
   dirmessage_enable=YES
   use_localtime=YES
   xferlog_enable=YES
   connect_from_port_20=YES
   idle_session_timeout=600
   chroot_local_user=YES
   secure_chroot_dir=/var/run/vsftpd/empty
   pam_service_name=vsftpd
   rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
   rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
   ssl_enable=NO
   user_sub_token=$USER
   local_root=/home/$USER/ftp
   ```
   
5. Restart the FTP server with the new configuration with:

   ```shell
   sudo service vsftpd restart
   ```

6. You can now connect to it at [ftp://192.168.1.181/](ftp://192.168.1.181/), 
   with your username and password. This is accessible at the standard FTP port 21.
   **DO NOT USE THIS OUT OF YOUR LAN NETWORK IT IS UNSECURED**.

## Hard drive mounting
1. Hard drive must be FAT32 otherwise we need special thing for NTFS
2. Edit the file system table with:

   ```shell
   sudo nano /etc/fstab
   ```
   
   and add:

   ```shell
   /dev/sda1 ~/ftp/drive  vfat    user,umask=0000   0       0
   ```

3. Mount it now with:

   ```shell
   sudo mount -t vfat /dev/sda1 ~/ftp/drive -o umask=0000
   ```

4. After that your Pi won't boot up correctly if the hard drive is not connected...
   You would have to change *fstab* back so that it boots fine.

## Resilio
1. Create a shared resilio directory with:

   ```shell
   mkdir ~/ftp/drive/resilio
   ```

2. Create a hidden resilio configuration directory with:

   ```shell
   mkdir ~/.resilio && cd ~/.resilio
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
   rm resilio-sync_arm.tar.gz
   ```

6. Create the configuration file with:
   
   ```shell
   nano resilio.conf
   ```
   
   and enter the following (AND CHANGE THE PASSWORD)

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
    sudo ./rslsync --config resilio.conf
    ```
    
11. Terminate Resilio with:

    ```shell
    sudo killall rslsync
    ```
    

## VPN in Romania
1. `cd /etc/openvpn`
2. `sudo wget https://www.privateinternetaccess.com/openvpn/openvpn.zip` to download files
3. `sudo unzip openvpn.zip && sudo rm openvpn.zip` to extract
4. `sudo nano file.txt` and enter your username and password on the first and second line respectively
5. `sudo nano Romania.ovpn` and replace the line:
   ```
   auth-user-pass
   ```
   by:
   ```
   auth-user-pass file.txt
   ```
6. `sudo mv Romania.ovpn Romania.conf` for boot up purposes
7. `sudo rm *.ovpn` to remove all the other OpenVPN configurations
8. `sudo nano /etc/default/openvpn` and append `AUTOSTART="Romania"`
9. `sudo reboot` to reboot the Pi and reconnect to it afterwards.
10. `curl ipinfo.io/$(wget -q -O - ipecho.net/plain)` to check your normal public IP
11. `sudo service openvpn@Romania start` to start the openvpn service now (or use `sudo systemctl start openvpn@Romania.service`)
12. `sudo service openvpn@Romania status` to check the service works
13. `curl ipinfo.io/$(wget -q -O - ipecho.net/plain)` to check the VPN public IP

## Deluge
1. `cp ~/.config/deluge/auth ~/.config/deluge/auth.old` just in case
2. `sudo nano web.conf` and append your username and password in the format `username:password:10`
3. `sudo nano core.conf` and enter the following:

   ```json
   {
     "file": 1,
     "format": 1
   }{
     "info_sent": 0.0,
     "lsd": true,
     "send_info": false,
     "move_completed_path": "",
     "enc_in_policy": 1,
     "queue_new_to_top": false,
     "ignore_limits_on_local_network": true,
     "rate_limit_ip_overhead": true,
     "daemon_port": 58846,
     "natpmp": true,
     "max_active_limit": 100,
     "utpex": true,
     "max_active_downloading": 100,
     "max_active_seeding": 100,
     "allow_remote": true,
     "max_half_open_connections": -1,
     "download_location": "~/ftp/drive/downloads",
     "compact_allocation": false,
     "max_upload_speed": -1.0,
     "cache_expiry": 60,
     "prioritize_first_last_pieces": false,
     "auto_managed": true,
     "enc_level": 2,
     "max_connections_per_second": 20,
     "dont_count_slow_torrents": false,
     "random_outgoing_ports": true,
     "max_upload_slots_per_torrent": -1,
     "new_release_check": false,
     "enc_out_policy": 1,
     "outgoing_ports": [
       0,
       0
     ],
     "seed_time_limit": 180,
     "cache_size": 512,
     "share_ratio_limit": 30.0,
     "max_download_speed": -1.0,
     "geoip_db_location": "/usr/share/GeoIP/GeoIP.dat",
     "torrentfiles_location": "",
     "stop_seed_at_ratio": false,
     "peer_tos": "0x00",
     "listen_interface": "",
     "upnp": true,
     "max_download_speed_per_torrent": -1,
     "max_upload_slots_global": -1,
     "enabled_plugins": [],
     "random_port": true,
     "autoadd_enable": true,
     "max_connections_global": -1,
     "enc_prefer_rc4": true,
     "listen_ports": [
       6881,
       6891
     ],
     "dht": true,
     "stop_seed_ratio": 2.0,
     "seed_time_ratio_limit": 7.0,
     "max_upload_speed_per_torrent": -1,
     "copy_torrent_file": false,
     "del_copy_torrent_file": false,
     "move_completed": false,
     "proxies": {
       "peer": {
         "username": "",
         "password": "",
         "type": 0,
         "hostname": "",
         "port": 8080
       },
       "web_seed": {
         "username": "",
         "password": "",
         "type": 0,
         "hostname": "",
         "port": 8080
       },
       "tracker": {
         "username": "",
         "password": "",
         "type": 0,
         "hostname": "",
         "port": 8080
       },
       "dht": {
         "username": "",
         "password": "",
         "type": 0,
         "hostname": "",
         "port": 8080
       }
     },
     "add_paused": true,
     "max_connections_per_torrent": -1,
     "remove_seed_at_ratio": false,
     "autoadd_location": "~/ftp/drive/torrents",
     "plugins_location": "~/.config/deluge/plugins"
   }
   ```
4. `killall deluged` just to be sure it is not running
5. `pip install --upgrade google-api-python-client` may be necessary
6. `sudo nano /etc/rc.local` and append the following code before `exit 0`:

   ```shell
   # torrent client start:
   sudo -u USERNAME /usr/bin/python /usr/bin/deluged
   ```
7. `deluged` to launch the daemon now. You can access it at user@192.168.1.x:58846
8. Modify IP Tables so that deluge can only work through the openvpn tunnel
    1. `sudo nano /etc/init.d/iptables` and enter the following:

      ```shell
      #! /bin/sh
      # /etc/init.d/iptables 
      
      ### BEGIN INIT INFO
      # Provides:          iptables
      # Required-Start:    $remote_fs $syslog
      # Required-Stop:     $remote_fs $syslog
      # Default-Start:     2 3 4 5
      # Default-Stop:      0 1 6
      # Short-Description: Script to set up iptables for Deluge and VPN
      # Description:       TBA
      ### END INIT INFO
      
      case "$1" in
        start)
          echo "Starting iptables"
          iptables -A OUTPUT -m owner --gid-owner debian-deluged -o lo -j ACCEPT
          iptables -A OUTPUT -m owner --gid-owner debian-deluged -d 192.168.1.1 -j REJECT
          iptables -A OUTPUT -m owner --gid-owner debian-deluged -o eth0 -d 192.168.1.0/24 -j ACCEPT
          iptables -A OUTPUT -m owner --gid-owner debian-deluged \! -o tun0 -j REJECT
          ;;
        stop)
          echo "Stopping iptables (not implemented yet)"
          ;;
        *)
          echo "Usage: /etc/init.d/iptables {start|stop}"
          exit 1
          ;;
      esac
      
      exit 0
      ```
    2. `sudo chmod 755 /etc/init.d/iptables` to make it executable
    3. `sudo /etc/init.d/iptables start` to test it works
        - Test it works with the VPN on
            1. `sudo service openvpn start` to start the VPN
            2. `sudo -u debian-deluged ping -c 4 8.8.8.8` and it should transmit the packets
        - Test it fails without the VPN
            1. `sudo service openvpn stop` to stop the VPN
            2. `sudo -u debian-deluged ping -c 4 8.8.8.8` and it should transmit no packet
        - In case of problem, you can use `sudo iptables -F INPUT` to flush the ip table.
    4. `sudo update-rc.d iptables defaults` to make it run at boot once you've checked it works

## VPN for Deluge only
- Nearly impossible to do without a VM
- Another way is to use a router with OpenWRT (Working on that...)

## Duckdns
1. `mkdir ~/.duckdns && cd ~/.duckdns`
2. `nano duck.sh` and enter the line provided by [www.duckdns.org](https://www.duckdns.org/install.jsp), similar to:

   ```shell
   echo url="https://www.duckdns.org/update?domains=user&token=xxxxxxxxxxxxxxxxxxx&ip=" | curl -k -o ~/duckdns/duck.log -K -
   ```
3. `sudo chmod 700 duck.sh` to change permissions
4. `crontab -e` and append `*/5 * * * * ~/.duckdns/duck.sh >/dev/null 2>&1`
5. `./duck.sh ` to launch duckdns now
6. `cat duck.log` to check it works
7. `sudo service cron start` to make it check each 5 minutes

## Portfolio
1. `cd ~/ftp/drive`
2. `git clone https://github.com/qdm12/Portfolio.git && cd Portfolio`
3. `sudo pip install -r requirements.txt` to install Python packages
4. Replace *.csv* and *.xlsx* files with real ones
5. Adjust a Resilio shared directory and enter `sudo chmod 777 resilio_dir`
6. `sudo python main.py` to test the program in the command line
7. `sudo nano /lib/systemd/system/portfolio.service` and enter the following:

   ```ini
   [Unit]
   Description=Portfolio Python program
   After=multi-user.target
   
   [Service]
   Type=simple
   ExecStart=/usr/bin/python ~/ftp/drive/Portfolio/main.py
   
   [Install]
   WantedBy=multi-user.target
   ```
8. `sudo chmod 644 /lib/systemd/system/portfolio.service` to change permissions
9. `sudo chmod +x ~/ftp/drive/Portfolio/main.py` to make it executable
10. `sudo systemctl daemon-reload`
11. `sudo systemctl enable portfolio.service`
12. `sudo systemctl start portfolio.service`
13. `sudo systemctl status portfolio.service` to check it works

## BellyButton
1. Copy the source files to the directory `~/ftp/drive/BellyButton`
2. `cd ~/ftp/drive/BellyButton` to install Python packages
3. `sudo pip install -r requirements.txt`
4. `sudo nano /lib/systemd/system/bellybutton.service` and enter the following:

   ```ini
   [Unit]
   Description=Belly Button Python program
   After=multi-user.target
   
   [Service]
   Type=simple
   ExecStart=/usr/bin/python ~/ftp/drive/BellyButton/notify.py
   
   [Install]
   WantedBy=multi-user.target
   ```
5. `sudo chmod 644 /lib/systemd/system/bellybutton.service` to change permissions
6. `sudo +x ~/ftp/drive/BellyButton/notify.py` to make it executable
7. `sudo systemctl daemon-reload`
8. `sudo systemctl enable bellybutton.service`
9. `sudo systemctl start bellybutton.service`
10. `sudo systemctl status bellybutton.service` to check it works

## HTTP server
1. Move files to be served to *~/ftp/drive/http/*
2. `cd ~/ftp/drive/http`
3. `sudo nano http.py` and paste this content

   ```python
   from bottle import get, static_file, run
   from os import chdir, path

   @get('/Mods/<path:path>')
   def get_Mods(path):
       return static_file(path, root='Mods')

   @get('/usermaps/<path:path>')
   def get_usermaps(path):
       return static_file(path, root='usermaps')

   if __name__ == '__main__':
       chdir(path.dirname(path.realpath(__file__)))
       run(server='paste', host='0.0.0.0', port=16257)
   ```
4. Install some Python packages with `sudo pip install bottle paste`
5. `sudo nano /lib/systemd/system/http.service` and enter the following:

   ```ini
   [Unit]
   Description=HTTP Python program
   After=multi-user.target
   
   [Service]
   Type=simple
   ExecStart=/usr/bin/python ~/ftp/drive/http/http_cod4.py
   
   [Install]
   WantedBy=multi-user.target
   ```
6. `sudo chmod 644 /lib/systemd/system/http.service` to change permissions
7. `sudo +x ~/ftp/drive/http/http_cod4.py` to make it executable
8. `sudo systemctl daemon-reload`
9. `sudo systemctl enable http.service`
10. `sudo systemctl start http.service`
11. `sudo systemctl status http.service` to check it works

## Webcam
1. `sudo apt-get remove libavcodec-extra-56 libavformat56 libavresample2 libavutil54` to remove conflicting libraries
2. `wget https://github.com/ccrisan/motioneye/wiki/precompiled/ffmpeg_3.1.1-1_armhf.deb` to download FFMPEG
3. `sudo dpkg -i ffmpeg_3.1.1-1_armhf.deb` to install FFMPEG
4. `sudo rm ffmpeg_3.1.1-1_armhf.deb`
5. `sudo apt-get install curl libssl-dev libcurl4-openssl-dev libjpeg-dev libx264-142 libavcodec56 libavformat56 libmysqlclient18 libswscale3 libpq5` to install packages required by FFMEG.
6. `wget https://github.com/Motion-Project/motion/releases/download/release-4.0.1/pi_jessie_motion_4.0.1-1_armhf.deb` to download Motion.
7. `sudo dpkg -i pi_jessie_motion_4.0.1-1_armhf.deb` to install Motion
8. `sudo rm pi_jessie_motion_4.0.1-1_armhf.deb`
9. `sudo nano /etc/motion/motion.conf` and change the following:
    - `daemon on`
    - `width 640`
    - `height 480`
    - `framerate 20`
    - `output_pictures first`
    - `ffmpeg_output_movies off`
    - `target_dir ~/ftp/drive/webcam` (and **uncomment** it)
    - `stream_port 25081`
    - `stream_maxrate 20`
    - `stream_localhost off`
    - `stream_auth_method 1`
    - `webcontrol_port 25080`
    - `webcontrol_authentication user:password` and **replace** with your credentials
10. `sudo nano /etc/default/motion` to setup the daemon and change:
    - `start_motion_daemon=yes`
11. `sudo service motion start`
12. 

## Bitcoin full node
- Working on it

## Useful commands
1. `sudo nano ~/.bashrc` and append the following:
   ```bash
   alias myip='wget -q -O - ipecho.net/plain'
   alias myipmore='curl ipinfo.io/$(wget -q -O - ipecho.net/plain)'
   ```
