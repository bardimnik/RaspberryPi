# Raspberry Pi

## Webcam

### What is it about
- In progress

### Instructions
1. `sudo apt-get remove libavcodec-extra-56 libavformat56 libavresample2 libavutil54` to remove conflicting libraries
2. `wget https://github.com/ccrisan/motioneye/wiki/precompiled/ffmpeg_3.1.1-1_armhf.deb` to download FFMPEG
3. `sudo dpkg -i ffmpeg_3.1.1-1_armhf.deb` to install FFMPEG
4. `sudo rm ffmpeg_3.1.1-1_armhf.deb`
5. `sudo apt-get install curl libssl-dev libcurl4-openssl-dev libjpeg-dev libx264-142 libavcodec56 libavformat56 libmysqlclient18 libswscale3 libpq5` to install packages required by FFMEG.
6. `wget https://github.com/Motion-Project/motion/releases/download/release-4.0.1/pi_jessie_motion_4.0.1-1_armhf.deb` to download Motion.
7. `sudo dpkg -i pi_jessie_motion_4.0.1-1_armhf.deb` to install Motion
8. `sudo rm pi_jessie_motion_4.0.1-1_armhf.deb`
9. `sudo nano /etc/motion/motion.conf` and change the following:
    - `daemon on`
    - `width 640`
    - `height 480`
    - `framerate 20`
    - `output_pictures first`
    - `ffmpeg_output_movies off`
    - `target_dir ~/ftp/drive/webcam` (and **uncomment** it)
    - `stream_port 25081`
    - `stream_maxrate 20`
    - `stream_localhost off`
    - `stream_auth_method 1`
    - `webcontrol_port 25080`
    - `webcontrol_authentication user:password` and **replace** with your credentials
10. `sudo nano /etc/default/motion` to setup the daemon and change:
    - `start_motion_daemon=yes`
11. `sudo service motion start`
12. 