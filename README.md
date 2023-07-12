# FFMPEG
## Use from FFMPEG
At the first download ffmpeg tool from its [website](https://www.ffmpeg.org) and set PATH configuration in system.
### windows
if use from windows, you should add it to environment variables. use this flow:
- System propertise
- Advance tab
- Environment Variables
- Path variable
- Edit
- New
- Address fo ffmpeg.exe file

## Project structure
For using ffmpeg for encryption, we need to this files:
```
structure:
|_ key
    |_ enc.key
    |_ enc.txt
|_ input.mp4
|_ outpu
    |_ out.m3u8
    |_ out%d.ts (replace %d with number of file)
|_ index.html (for playing)
```
For encryption and decryption, we need to a key. ffmpeg use from the `enc.key` for this operation. this file content a string with 16 Byte lentgh. (for example we use from `1234567890123456` for its)
<br>`enc.txt` is a file that use for `hls_key_info_file`. this file invole three line:
```
Server URI
enc.key absolute address
IV (optional in hex)
``` 
After preparing the files, with this command we can split and encrypt the file:
``` shell
ffmpeg -y -i video.mp4 -hls_time 10 -hls_key_info_file key/key.txt -hls_playlist_type vod -hls_segment_filename "out/file%d.ts" out/file.m3u8
```
With this command, we have some `.ts` files and a `.m3u8` file for playlist. `.ts` files encrypted with IV/key and `.m3u8` file decrypted with URI/IV .
<br> In playlist file we see this lines:
```
#EXTM3U
#EXT-X-VERSION:3
#EXT-X-TARGETDURATION:17
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-KEY:METHOD=AES-128,URI="httphttp://localhost:5500/key/enc.key",IV=0xecd0d06eaf884d8226c33928e87efa33
#EXTINF:16.666667,
file0.ts
#EXTINF:16.666667,
file1.ts
#EXTINF:16.666667,
file2.ts
.
.
.
#EXT-X-ENDLIST
```
For playing, just add this tag to `<video>` tag:
``` html
<source src="http://localhost:5500/file/file.m3u8" type="application/x-mpegURL">
```
## Note
We need to a webserver (for example `live server` in vscode)