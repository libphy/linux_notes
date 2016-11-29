## ffmpeg
ffmpeg is a simple and quite powerful editor for audio and video files.

### Video edit (cut, split) examples
```
ffmpeg -i input.avi -vcodec copy -acodec copy -ss 00:00:00 -t 00:30:00 output1.avi
ffmpeg -i input.avi -vcodec copy -acodec copy -ss 00:30:00 -t 01:00:00 output2.avi
```

### Audio conversion
(ex) downsampleing to 22050 Hz and mono (1 channel)
```
ffmpeg -i input.wav -ar 22050 -ac 1 output.wav
```
(ex) change audio formats (it supports many audio file formats including but not limited to wav, mp3, flac, ogg, aiff, aif)
```
ffmpeg -i input.XXX [-options] output.YYY
```
To change many files at once, one can do using python subprocess and for-loop.
