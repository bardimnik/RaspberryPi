# Raspberry Pi

## Advanced SSH and SFTP

### What is it about
- SSH with PKI
- SFTP server

### Instructions
1. Make ssh start at boot automatically with:

   ```bash
   sudo touch /boot/ssh
   ```
   
2. For more devices to access your Pi via SSH, enter, as before:
    ```bash
    cat ~/.ssh/id_rsa.pub | ssh pi@192.168.1.XXX "mkdir -p ~/.ssh && cat >>  ~/.ssh/authorized_keys"
    ```
    
    - Answer **yes** for the host's authenticity establishment
    - Input the username pi's password you have setup previously.
    - Your SSH public key is now registered in your Raspberry Pi.
      
3. Change permissions for security purposes:

   ```bash
   sudo chmod 700 ~/.ssh/
   ```
   
4. Change permissions for security purposes:

   ```bash
   sudo chmod 600 ~/.ssh/authorized_keys
   ```
   
5. Change the SSH configuration with:

   ```bash
   sudo nano /etc/ssh/sshd_config
   ```
   
   and replace everything with:
   
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
   Subsystem sftp internal-sftp
       Match group sftpusers
       ChrootDirectory %h
       ForceCommand internal-sftp
   ```

6. Load the new configuration and restart the service ssh

   ```bash
   sudo service ssh restart   
   ```
   
7. You will then be able to access your Raspberry Pi with:

   ```bash
   ssh pi@192.168.1.XXX -p 37032
   ```
   
   and only with the SSH key pairs registered.
