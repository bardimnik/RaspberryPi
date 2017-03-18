# Raspberry Pi

## Portfolio

### What is it about
- Configure Portfolio to work as a service
- Configure a Resilio shared directory to synchronise with other persons

### Instructions
1. Head to the hard drive directory with:
   
   ```shell
   cd ~/ftp/drive
   ```

2. Install **git** to download the project with:

   ```shell
   sudo apt-get install -y git
   ```

3. Clone the project and go to its directory with:

   ```shell
   git clone https://github.com/qdm12/Portfolio.git
   cd Portfolio
   ```

4. Install Python PIP with:

   ```shell
   sudo apt-get install -y python-pip
   ```

5. Install Python packages requirements with:

   ```shell
   sudo pip install -r requirements.txt
   ```

6. Replace *.csv* and *.xlsx* files with real ones

7. Create a Resilio shared directory with:

   ```shell
   mkdir ~/ftp/drive/portfolio_resilio
   sudo chmod 777 ~/ftp/drive/portfolio_resilio
   ```
   
   And configure it with Resilio.
   
8. Test the program with the command line with:

   ```shell
   sudo python ~/ftp/drive/Portfolio/main.py
   ```

9. Create a *portfolio.service* with:

   ```shell
   sudo nano /lib/systemd/system/portfolio.service
   ```

   and enter the following:

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
   
10. Change the permissions of the service with:

    ```shell
    sudo chmod 644 /lib/systemd/system/portfolio.service
    ```
    
11. Make the Python script executable with:

    ```shell
    sudo chmod +x ~/ftp/drive/Portfolio/main.py
    ```
    
12. Reload the daemon with:

    ```shell
    sudo systemctl daemon-reload
    ```
    
13. Enable the service, start it and check its status with:

    ```shell
    sudo systemctl enable portfolio.service
    sudo systemctl start portfolio.service
    sudo systemctl status portfolio.service
    ```