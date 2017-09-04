# Raspberry Pi

## Deluge

### What is it about
- Configure Deluge

### Instructions
1. Create a directory for Deluge, for torrents and files downloaded:

   ```shell
   mkdir ~/ftp/drive/deluge
   mkdir ~/ftp/drive/deluge/downloads
   mkdir ~/ftp/drive/deluge/torrents
   ```

2. Install Deluge with:

   ```shell
   sudo apt-get -y install deluged deluge-console deluge-web python-mako
   ```

3. Run Deluge and kill it to create some initial configuration files:

   ```shell
   deluged
   sudo pkill deluged
   ```

4. Edit the authentication file with:

   ```shell
   sudo nano ~/.config/deluge/auth
   ```

   and append your username and password in the format `username:password:10`

5. Replace the core configuration with:
   
   ```shell
   rm ~/.config/deluge/core.conf
   nano ~/.config/deluge/core.conf
   ```
   
   and write the following:   

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
     "download_location": "/home/pi/ftp/drive/deluge/downloads",
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
     "add_paused": false,
     "max_connections_per_torrent": -1,
     "remove_seed_at_ratio": false,
     "autoadd_location": "/home/pi/ftp/drive/deluge/torrents",
     "plugins_location": "/home/pi/.config/deluge/plugins"
   }
   ```

6. To make the Deluge daemon start at boot:
   
   ```shell
   sudo nano /etc/rc.local
   ```
   
   and append the following code before `exit 0`: (replace **USERNAME** with yours)

   ```shell
   # torrent client start:
   sudo -u USERNAME /usr/bin/python /usr/bin/deluged
   ```
   
7. Launch the daemon with:

   ```shell
   deluged
   ```

   You can access it at user@192.168.**XXX**.**XXX**:58846
   
8. Deluge Web UI
    1. Launch it from the Raspberry Pi with:
    
       ```shell
       deluge-web --fork
       ```
       
     2. Access it with your browser at 192.168.**XXX**.**XXX**:8112
     3. The default password is **deluge**, which you should change through the Web UI on your browser.

9. Terminate Deluged with:

   ```shell
   sudo pkill deluged
   ```
   
10. Make the VPN for Deluged only.. seems impossible? (in progress)

