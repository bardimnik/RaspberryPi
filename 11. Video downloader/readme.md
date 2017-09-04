# Raspberry Pi

## Video downloader

### What is it about
- Install youtube-dl and ffmpeg
- Use a few basic commands to download music or videos

### Instructions
1. Download and install **youtube-dl** with:

   ```shell
   sudo curl -L https://yt-dl.org/downloads/latest/youtube-dl -o /usr/local/bin/youtube-dl
   ```
   
2. Make it executable with:

   ```shell
   sudo chmod a+rx /usr/local/bin/youtube-dl
   ```
   
3. Install **FFMPEG** for video conversion that youtube-dl can do with:

   ```shell
   sudo apt install ffmpeg
   ```
   
3. Use it with these 3 main commands:
   1. Download one video
      ```shell
	  youtube-dl https://www.youtube.com/watch?v=n-adgQWZYxI
	  ```
	  
   2. Download the audio from a video
      
	  ```shell
	  youtube-dl -x --audio-format mp3 --audio-quality 0 https://www.youtube.com/watch?v=n-adgQWZYxI
	  ```
   3. Download videos from a text file containing one link per line, and ignoring errors
      
	  ```shell
	  youtube-dl -i -a file.txt
	  ```

4. See more information about youtube-dl on its [Github page](https://github.com/rg3/youtube-dl/blob/master/README.md#options)