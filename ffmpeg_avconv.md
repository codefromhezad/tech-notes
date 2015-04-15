# FFMPEG/AvConv 

Code snippets are written for ffmpeg. Some options have to be adapted if you want to use avconv.


## Encoding for HTML5 Video

libvpx and libx264 encoders have to be installed.

### WebM

	ffmpeg -i src.mp4 -c:v libvpx -preset slow -s 1024x576 -qmin 0 -qmax 50 -an -b:v 400K -pass 1 output.webm


### Encode to MP4

	ffmpeg -i src.mp4 -c:v libx264 -preset slow -an -b:v 370K output.mp4




## Extract each frame from a video

Warning: This will extract *each* frame from the video. Eg: for a 25 FPS video that is 60 seconds long, this will generate 1500 jpg images.

	ffmpeg -i src.mp4 -qscale:v 2 -f image2 -c:v mjpeg output-%d.jpg

The qscale:v option defines the quality of the extracted image. For jpg, it can be between 1 and 31 (0=best, 31=worst)
Recommended value seems to be in the range 2-5 for a good quality image.

Source: [Stack Overflow](http://stackoverflow.com/questions/10225403/how-can-i-extract-a-good-quality-jpeg-image-from-an-h264-video-file-with-ffmpeg)


## Get Video duration in int seconds

    float_vid_seconds=$(ffprobe -i src.mp4 -show_format -v quiet | sed -n 's/duration=//p')
    vid_seconds=${float_vid_seconds%.*}

vid_seconds will contain the duration.

