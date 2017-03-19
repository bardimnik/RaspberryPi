# Raspberry Pi

## Others

### What is it about
- Resources monitoring with HTOP
- Public IP check

### Instructions
- HTOP
    1. Install it with
    
       ```shell
       sudo apt-get install -y htop
       ```
       
   2. Run it with:

       ```shell
       htop
       ```
     
- Public IP
    1. Edit **bashrc** with:
    
       ```shell
       sudo nano ~/.bashrc
       ```
    
    2. Append the following:
   
      ```shell
      alias myip='wget -q -O - ipecho.net/plain && echo'
      alias myipmore='curl ipinfo.io/$(wget -q -O - ipecho.net/plain) && echo'
      ```