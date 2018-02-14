# Raspberry Pi

## BellyButton

### What is it about
- Item 1

### Instructions
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