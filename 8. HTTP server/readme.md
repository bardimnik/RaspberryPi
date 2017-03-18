# Raspberry Pi

## HTTP server

### What is it about
- Item 1

### Instructions
1. Create a directory for the files to be accessed with:

   ```shell
   mkdir ~/ftp/drive/http
   ```
   
   and move your files to be accessed to this directory
   
2. Create a Python script with:

   ```shell
   sudo nano http.py
   ```

   And enter the following content:

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

3. Install some Python packages with:

   ```shell
   sudo pip install bottle paste
   ```
   
4. Create a service *http.service* with:

   ```shell
   sudo nano /lib/systemd/system/http.service
   ```
   
   And enter the following:

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
6. Change the permissions of the service with:

   ```shell
   sudo chmod 644 /lib/systemd/system/http.service
   ```
   
7. Make the Python script executable with:
   
   ```shell
   sudo +x ~/ftp/drive/http/http.py
   ```
   
8. Reload the daemon with:

   ```shell
   sudo systemctl daemon-reload
   ```
   
9. Enable, start, and test the service with

   ```shell
   sudo systemctl enable http.service
   sudo systemctl start http.service
   sudo systemctl status http.service
   ```