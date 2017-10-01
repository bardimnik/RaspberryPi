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
     
- BashRc
    1. Add some files to the home directory with:
	
	   ```shell
	   nano ~/.bashrc_art
	   ```
	   
	   And paste some nice [ASCII art in there](http://www.kammerl.de/ascii/AsciiSignature.php)

    2. Edit **bashrc** with:
    
       ```shell
       sudo nano ~/.bashrc
       ```
    
    3. Append the following:
   
      ```shell
      alias myip='wget -q -O - ipecho.net/plain && echo'
      alias myipmore='curl ipinfo.io/$(wget -q -O - ipecho.net/plain) && echo'
      cat ~/.bashrc_art
      ```