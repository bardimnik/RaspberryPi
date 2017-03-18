# Raspberry Pi

## VPN with PIA

### What is it about
- Setup OpenVPN with PIA
- Setup credentials
- Make the VPN start at boot

### Instructions
1. Install OpenVPN with:
   
   ```shell
   sudo apt-get -y install openvpn
   ```
   
2. Go to the OpenVpn directory with:
   
   ```shell
   cd /etc/openvpn
   ```
   
3. Download PIA openvpn configuration files with:

   ```shell
   sudo wget https://www.privateinternetaccess.com/openvpn/openvpn.zip
   ```

4. Install Zip with:
   
   ```shell
   sudo apt-get -y install zip
   ```
   
5. Unzip it and remove the archive

   ```shell
   sudo unzip openvpn.zip
   sudo rm openvpn.zip
   ```

6. Create your credentials file with:

   ```shell
   sudo nano file.txt
   ```

And enter your username and password on the first and second line respectively

7. Edit the Romania VPN configuration with:

   ```shell
   sudo nano Romania.ovpn
   ```
   
   Replace the line `auth-user-pass` by `auth-user-pass file.txt`

8. For boot purposes, rename the configuration file with:
   
   ```shell
   sudo mv Romania.ovpn Romania.conf
   ```
   
9. Remove the other OpenVPN configuration files with:

   ```shell
   sudo rm *.ovpn
   ```

10. Edit the boot VPN service with:
   
   ```shell
   sudo nano /etc/default/openvpn
   ```
   
   and append `AUTOSTART="Romania"`

11. You may have to reboot your Raspberry Pi now with

   ```shell
   sudo reboot
   ```
   
   and then reconnect to it with *ssh*.

12. Check your normal public IP with:

    ```shell
    curl ipinfo.io/$(wget -q -O - ipecho.net/plain)
    ```

13. Launch the OpenVPN service and check it works fine with:

    ```shell
    sudo service openvpn@Romania start
    sudo service openvpn@Romania status    
    ```
    
14. Now recheck your public IP and it should match the VPN's IP:

    ```shell
    curl ipinfo.io/$(wget -q -O - ipecho.net/plain)
    ```