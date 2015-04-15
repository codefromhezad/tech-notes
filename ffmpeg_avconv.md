# AvConv

## Install avconv with "HTML5 Codecs"

	sudo apt-get install libav-tools libvpx1 libx264-142



# FFMPEG 

## Encode to WebM

	ffmpeg -i src.mp4 -c:v libvpx -preset slow -s 1024x576 -qmin 0 -qmax 50 -an -b:v 400K -pass 1 output.webm



## Encode to MP4

	ffmpeg -i src.mp4 -c:v libx264 -preset slow -an -b:v 370K output.mp4



## Extract images from video

	ffmpeg -i src.mp4 -qscale:v 2 -f image2 -c:v mjpeg output-%d.jpg

The qscale:v option defines the quality of the extracted image. For jpg, it can be between 1 and 31 (0=best, 31=worst)

Recommended value seems to be in the range 2-5 for a good quality image.

Source: [Stack Overflow Subject](http://stackoverflow.com/questions/10225403/how-can-i-extract-a-good-quality-jpeg-image-from-an-h264-video-file-with-ffmpeg)