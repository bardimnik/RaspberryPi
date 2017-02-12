# Raspberry Pi

## First run
1. `sudo raspi-config` to change the date
2. `sudo apt-get update`
3. `sudo apt-get upgrade`
4. `sudo apt-get -y install htop git vsftpd zip openvpn python python-pip curl deluged deluge-console python-mako deluge-web`
5. `sudo apt-get -y autoremove` to do some cleanup

## Change passwords
1. `sudo passwd root` to change the password of root
2. `logout`
3. `passwd`

## SSH and SFTP
1. `cd ~/.ssh` to go to the ssh home directory (or `mkdir ~/.ssh`)
2. `sudo nano authorized_keys` and save the public keys similarly to this:
```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAA.... quentin.mcgaw@gmail.com
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAA.... Otherapplication
...
```
3. `sudo chmod 700 ~/.ssh/` to change permissions
4. `sudo chmod 600 ~/.ssh/authorized_keys` to change permissions
5. `sudo nano /etc/ssh/sshd_config` to change the SSH configuration with this content:
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
6. `sudo /etc/init.d/ssh restart` or `sudo service ssh restart`

## Local FTP
1. `mkdir ~/ftp` to create an ftp directory
2. `sudo chmod 555 ~/ftp` to change permissions
3. `sudo chown -R USERNAME:pi ~/ftp` to change ownership (not necessary maybe) and change USERNAME to yours
4. `sudo nano /etc/vsftpd.conf` and put this content:
```
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
5. `sudo service vsftpd restart` to restart the FTP server

## Hard drive mounting
1. Hard drive must be FAT32 otherwise we need special thing for NTFS
2. `sudo nano /etc/fstab` and add:
```
/dev/sda1 ~/ftp/drive  vfat    user,umask=0000   0       0
```
3. `sudo mount -t vfat /dev/sda1 ~/ftp/drive -o umask=0000` to mount it now

## Resilio
1. `mkdir ~/ftp/drive/resilio` to create a shared resilio directory
2. `mkdir ~/.resilio && cd ~/.resilio` to create a hidden resilio configuration directory
3. `wget https://download-cdn.resilio.com/stable/linux-arm/resilio-sync_arm.tar.gz` to download it
4. `tar -xf resilio-sync_arm.tar.gz` to extract it
5. `rm resilio-sync_arm.tar.gz LICENSE.txt` to remove useless files
6. `nano resilio.conf` and add the following (AND CHANGE THE PASSWORD):
```
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
7. `sudo nano /etc/init.d/rslsync` and enter the following:
```
#! /bin/sh
# /etc/init.d/rslsync
### BEGIN INIT INFO
# Provides:             Resilio
# Short-Description:    Starts/Stops/Help for Resilio
# Description:          Starts/Stops/Help for Resilio
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
- `sudo nano /etc/init.d/superscript`
```
8. `sudo update-rc.d rslsync defaults` (**to check**)
9. `sudo ./rslsync --config resilio.conf` to launch it now
10. `sudo killall rslsync` to terminate

## VPN in Romania
1. `cd /etc/openvpn`
2. `sudo wget https://www.privateinternetaccess.com/openvpn/openvpn.zip` to download files
3. `sudo unzip openvpn.zip && sudo rm openvpn.zip` to extract
4. `sudo nano file.txt` and enter your username and password on the first and second line respectively
5. `sudo nano Romania.ovpn` and replace it with:
```
client
dev tun
proto udp
remote ro.privateinternetaccess.com 1198
resolv-retry infinite
nobind
persist-key
persist-tun
cipher aes-128-cbc
auth sha1
tls-client
remote-cert-tls server
auth-user-pass file.txt
comp-lzo
verb 1
reneg-sec 0
crl-verify crl.rsa.2048.pem
ca ca.rsa.2048.crt
disable-occ
```
6. `sudo mv Romania.ovpn Romania.conf` for startup purposes...
7. `sudo rm *.ovpn` to remove all the other OpenVPN configurations
8. `sudo nano update-resolv-conf` and enter the following:
```
#!/bin/bash
#
# Parses DHCP options from openvpn to update resolv.conf
# To use set as 'up' and 'down' script in your openvpn *.conf:
# up /etc/openvpn/update-resolv-conf
# down /etc/openvpn/update-resolv-conf
#
# Used snippets of resolvconf script by Thomas Hood and Chris Hanson.
# Licensed under the GNU GPL.  See /usr/share/common-licenses/GPL.
#
# Example envs set from openvpn:
#
#     foreign_option_1='dhcp-option DNS 193.43.27.132'
#     foreign_option_2='dhcp-option DNS 193.43.27.133'
#     foreign_option_3='dhcp-option DOMAIN be.bnc.ch'
#

[ -x /sbin/resolvconf ] || exit 0
[ "$script_type" ] || exit 0
[ "$dev" ] || exit 0

split_into_parts()
{
        part1="$1"
        part2="$2"
        part3="$3"
}

case "$script_type" in
  up)
        NMSRVRS=""
        SRCHS=""
        for optionvarname in ${!foreign_option_*} ; do
                option="${!optionvarname}"
                echo "$option"
                split_into_parts $option
                if [ "$part1" = "dhcp-option" ] ; then
                        if [ "$part2" = "DNS" ] ; then
                                NMSRVRS="${NMSRVRS:+$NMSRVRS }$part3"
                        elif [ "$part2" = "DOMAIN" ] ; then
                                SRCHS="${SRCHS:+$SRCHS }$part3"
                        fi
                fi
        done
        R=""
        [ "$SRCHS" ] && R="search $SRCHS
"
        for NS in $NMSRVRS ; do
                R="${R}nameserver $NS
"
        done
        echo -n "$R" | /sbin/resolvconf -a "${dev}.openvpn"
        ;;
  down)
        /sbin/resolvconf -d "${dev}.openvpn"
        ;;
esac
```
9. `sudo nano /etc/default/openvpn` and append `AUTOSTART="Romania"`
10. `curl ipinfo.io/$(wget -q -O - ipecho.net/plain)` to check your normal public IP
11. `sudo systemctl start openvpn@Romania.service` to start the openvpn service now
12. `systemctl status openvpn@Romania.service` to check the service works
13. `curl ipinfo.io/$(wget -q -O - ipecho.net/plain)` to check the VPN public IP

## Deluge
1. `cp ~/.config/deluge/auth ~/.config/deluge/auth.old` just in case
2. `sudo nano web.conf` and append your username and password in the format `username:password:10`
3. `sudo nano core.conf` and enter the following:
```
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
```
# torrent client start:
sudo -u USERNAME /usr/bin/python /usr/bin/deluged
```
7. `deluged` to launch the daemon now. You can access it at user@192.168.1.x:58846

## IP tables


## Duckdns
1. `mkdir ~/.duckdns && cd ~/.duckdns`
2. `nano duck.sh` and enter the line provided by [www.duckdns.org](https://www.duckdns.org/install.jsp), similar to:
```
echo url="https://www.duckdns.org/update?domains=user&token=xxxxxxxxxxxxxxxxxxx&ip=" | curl -k -o ~/duckdns/duck.log -K -
```
3. `sudo chmod 700 duck.sh` to change permissions
4. `crontab -e` and append `*/5 * * * * ~/.duckdns/duck.sh >/dev/null 2>&1`
5. `./duck.sh ` to launch duckdns now
6. `cat duck.log` to check it works
7. `sudo service cron start` to make it check each 5 minutes

## Portfolio
- `cd ~/ftp/drive`
- `git clone https://github.com/qdm12/Portfolio.git && cd Portfolio`
- `sudo pip install -r requirements.txt` to install Python packages
- Replace *.csv* and *.xlsx* files with real ones
- Adjust a Resilio shared directory and enter `sudo chmod 777 resilio_dir`
- `sudo python main.py` to test the program in the command line
- `sudo nano /lib/systemd/system/portfolio.service` and enter the following:
```
[Unit]
Description=Portfolio Python program
After=multi-user.target

[Service]
Type=simple
ExecStart=/usr/bin/python ~/ftp/drive/Portfolio/main.py

[Install]
WantedBy=multi-user.target
```
- `sudo chmod 644 /lib/systemd/system/portfolio.service` to change permissions
- `sudo chmod +x ~/ftp/drive/Portfolio/main.py` to make it executable
- `sudo systemctl daemon-reload`
- `sudo systemctl enable portfolio.service`
- `sudo systemctl start portfolio.service`
- `sudo systemctl status portfolio.service` to check it works

## BellyButton
- Copy the source files to the directory `~/ftp/drive/BellyButton`
- `cd ~/ftp/drive/BellyButton` to install Python packages
- `sudo pip install -r requirements.txt`
- `sudo nano /lib/systemd/system/bellybutton.service` and enter the following:
```
[Unit]
Description=Belly Button Python program
After=multi-user.target

[Service]
Type=simple
ExecStart=/usr/bin/python ~/ftp/drive/BellyButton/notify.py

[Install]
WantedBy=multi-user.target
```
- `sudo chmod 644 /lib/systemd/system/bellybutton.service` to change permissions
- `sudo +x ~/ftp/drive/BellyButton/notify.py` to make it executable
- `sudo systemctl daemon-reload`
- `sudo systemctl enable bellybutton.service`
- `sudo systemctl start bellybutton.service`
- `sudo systemctl status bellybutton.service` to check it works

## HTTP server
- Move files to be served to *~/ftp/drive/http/*
- `cd ~/ftp/drive/http`
- `sudo nano http.py` and paste this content
```python
from SimpleHTTPServer import SimpleHTTPRequestHandler
from SocketServer import TCPServer
from os import chdir, path

if __name__ == '__main__':
    chdir(path.dirname(path.realpath(__file__)))
    Handler = SimpleHTTPRequestHandler
    httpd = TCPServer(("", 16257), Handler)
    httpd.serve_forever()
```
- `sudo nano /lib/systemd/system/http.service` and enter the following:
```
[Unit]
Description=HTTP Python program
After=multi-user.target

[Service]
Type=simple
ExecStart=/usr/bin/python ~/ftp/drive/http/http_cod4.py

[Install]
WantedBy=multi-user.target
```
- `sudo chmod 644 /lib/systemd/system/http.service` to change permissions
- `sudo +x ~/ftp/drive/http/http.py` to make it executable
- `sudo systemctl daemon-reload`
- `sudo systemctl enable http.service`
- `sudo systemctl start http.service`
- `sudo systemctl status http.service` to check it works

## Useful commands
- `wget -q -O - ipecho.net/plain`
- `curl ipinfo.io/$(wget -q -O - ipecho.net/plain)`
