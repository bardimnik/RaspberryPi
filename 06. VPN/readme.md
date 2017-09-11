# Raspberry Pi

## VPN with PIA

### What is it about
- Setup OpenVPN with PIA and your credentials
- Make the VPN start at boot
- Open your SSH port on the VPN

### Instructions

#### OpenVPN and PIA
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

#### VPN starting at boot
1. Edit the boot VPN service with:
   
   ```shell
   sudo nano /etc/default/openvpn
   ```
   
   and append `AUTOSTART="Romania"`

2. You may have to reboot your Raspberry Pi now with

   ```shell
   sudo reboot
   ```
   
   and then reconnect to it with *ssh* via the local IP address.

3. Check the public IP with:

    ```shell
    curl ipinfo.io/$(wget -q -O - ipecho.net/plain)
    ```

4. In case your IP is not the VPN IP, launch the OpenVPN service and check it works fine with:

    ```shell
    sudo service openvpn@Romania start
    sudo service openvpn@Romania status    
    ```
    
	Then recheck your public IP and it should match the VPN's IP:

    ```shell
    curl ipinfo.io/$(wget -q -O - ipecho.net/plain)
    ```
	
#### Open your SSH port on the VPN
1. Create a script file in your home directory:

   ```shell
   sudo nano ~/port_forwarding.sh
   ```
   
   And enter the following and then save it:
   
   ```shell
#!/usr/bin/env bash
error(){
  echo "$@" 1>&2
  exit 1
}
error_and_usage(){
  echo "$@" 1>&2
  usage_and_exit 1
}
usage(){
  echo "Usage: `dirname $0`/$PROGRAM"
}
usage_and_exit(){
  usage
  exit $1
}
version(){
  echo "$PROGRAM version $VERSION"
}
port_forward_assignment(){
  echo 'Loading port forward assignment information...'
  client_id=`head -n 100 /dev/urandom | sha256sum | tr -d " -"`
  echo "Client ID is $client_id"
  json=`curl "http://209.222.18.222:2000/?client_id=$client_id" 2>/dev/null`
  if [ "$json" == "" ]; then
    json='Port forwarding is already activated on this connection, has expired, or you are not connected to a PIA region that supports port forwarding'
  fi
  echo $json
}
EXITCODE=0
PROGRAM=`basename $0`
VERSION=2.1

while test $# -gt 0
do
  case $1 in
  --usage | --help | -h )
    usage_and_exit 0
    ;;
  --version | -v )
    version
    exit 0
    ;;
  *)
    error_and_usage "Unrecognized option: $1"
    ;;
  esac
  shift
done
port_forward_assignment
exit 0
   ```
   
   (Credits to doaks from PIA)
   
2. Make it executable with:

   ```shell
   sudo chmod +x port_forwarding.sh
   ```
   
3. Restart the VPN, as you have only **the two first minutes** to request the port forwarding:

   ```shell
   sudo service openvpn restart
   ```
   
4. Run the script with:
   
   ```shell
   ./~/port_forwarding.sh
   ```

   This will give you the port forwarded for that VPN server, say **6001** for example.

5. Test it by listening to that port with:

   ```shell
   netcat -l 6001
   ```
   
   And open the browser to the address *your-public-VPN-ip-address:6001*
   
   You should then see some activity with netcat.
   
7. Change the ssh port to 6001 with:

