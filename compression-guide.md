# Raws
Ideally always go with the raws with the best quality. There are multiple factors to look into such as resolution and bitrate but in the end the best compression wins with the best quality. Also don't be afraid of file size. As long as they're under 1GB each it's fine. If the raw has to be re-encoded and it's 1080p you might as well downscale it to 720p right away.

## Downscaling 1080p raws
If the raws have to be re-encoded to be able to be handled in Vegas it's best to downscale them while at it (note that Wano+ episodes are (supposedly) animated in 900p, so they should remain at 1080p)

`ffmpeg -i "incompatible_raw_1080p.mkv" -c:v libx264 -c:a aac -b:a 128k -crf 18 -strict experimental -vf scale=1280x720:flags=lanczos "encoded.mp4"`

Netflix:
`ffmpeg -i video.h264 -i audio.aac -map 0:v -map 1:a -c:v libx264 -c:a aac -b:a 128k -strict experimental -crf 18 -vf scale=1280x720:flags=lanczos out.mp4`

- Strict experimental is needed for aac audio codec for some reason
- crf 18 is good
- map thing maps the audio and video
- b:a is bitrate for audio, 128k in this case but it should match the raw's audio bitrate for some reason
- scale 1280x720 with the flag lanczos which does something

Do not change the framerate, this will mess up the panning.

## Fix washed out colors
In the RAWS VEG, add LEVELS --> Studio RGB to Computer RGB preset. For some reason Vegas reads the colour space of the source file incorrectly, so this fixes the gamma shift that produces washed out blacks, and by extension different colours.

![](https://i.imgur.com/YaBW6Z9.png)

# Project settings
Make sure the project properties in vegas match the raws frame rate and resolution. (Ctrl+Enter to check the properties) The field order should be Progressive scan. Disable resample mode. Turn off adjusting source media. No deinterlacing. No motion blur. 1.00000000 pixel aspect ratio.

# Rendering
Make sure to match the resolution and frame rate of the raw. If the raws have differing frame rates use the higher one.

To render, ensure you have the Lagarith codec installed (https://lags.leetcode.net/codec.html). In Vegas, choose the HD 1080-24p YUV preset under the .AVI heading. Hit "Customize Template" and ensure that under "Video Format", it is changed to "Lagarith Lossless Codec". That may change your frame size and frame rate to different defaults, so be sure to swap those back to "Use Project Settings" and "23.976". Before comitting the render, just double check under "Configure..." next to Lagarith that it has the following settings.

![](https://i.imgur.com/37psvYm.png)
# Compression
Run the following command after render:
`ffmpeg -i "test.avi" -c:v libx265 -c:a aac -b:a 128k -crf 18 -strict experimental "encoded.mp4"`

## Streams
The streams are encoded with the One Pace Publisher (Located in Nextcloud/applications). The publisher runs a batch file called `make_stream.bat`. If you wish to configure the encoding parameters you can do so in this batch.
