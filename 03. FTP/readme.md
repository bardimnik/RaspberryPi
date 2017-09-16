# Raspberry Pi

## FTP server for LAN only

### What is it about
- Setup LAN FTP unsecured server
- How to access the FTP server

### Instructions
1. Install the FTP package with:

   ```bash
   sudo apt-get -y install vsftpd
   ```
   
2. Create an ftp directory with:
   
   ```bash
   mkdir ~/ftp
   ```
   
3. Change permissions with:

   ```bash
   sudo chmod -R 700 ~/ftp
   ```
   
4. Enter this to change ownership (not necessary maybe) and change USERNAME to yours (or **pi**):

   ```bash
   sudo chown -R USERNAME:pi ~/ftp
   ```
   
5. Edit the FTP configuration file with:

   ```shell
   sudo rm /etc/vsftpd.conf && sudo nano /etc/vsftpd.conf
   ```
   
   and enter the following:
   
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
   allow_writeable_chroot=YES
   ```
   
6. Restart the FTP server with the new configuration with:

   ```shell
   sudo service vsftpd restart
   ```

7. You can now connect to it at [ftp://192.168.1.181/](ftp://192.168.1.181/), 
   with your username and password. This is accessible at the standard FTP port 21.
   **DO NOT USE THIS OUT OF YOUR LAN NETWORK IT IS UNSECURED**.
   
