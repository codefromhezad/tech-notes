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


## Get Video framerate

    ffmpeg -i src.mp4 2>&1 | sed -n "s/.*, \(.*\) fp.*/\1/p"



## Get Video duration in int seconds

    float_vid_seconds=$(ffprobe -i src.mp4 -show_format -v quiet | sed -n 's/duration=//p')
    vid_seconds=${float_vid_seconds%.*}

vid_seconds will contain the duration.



## Script to generate a fixed number of "equidistant" frames from a video

Copy/Paste in a bash script file (we'll call it extract-frames.sh for the usage exemple)

    Usage:
    ./extract-frames.sh <source-video-file> [<num_frames> <dest_folder>]
        num_frames:  Number of frames to extract (default = 30)
        dest_folder: Destination folder path (default = "./dist")


### Actual Script:

    #!/bin/bash

    # Set Default Opts Values
    readonly DEFAULT_NUM_EXTRACT_FRAMES=30
    readonly DEFAULT_DIST_FOLDER="./dist"

    # Check mandatory first argument is passed
    if [[ $# -eq 0 ]] ; then
        echo 'Error: Not enough arguments.'
        echo
        echo 'Usage:'
        echo './extract-frames.sh <source-video-file> [<num_frames> <dest_folder>]'
        echo '  num_frames:  Number of frames to extract (default = '$DEFAULT_NUM_EXTRACT_FRAMES')'
        echo '  dest_folder: Destination folder path (default = "'$DEFAULT_DIST_FOLDER'")'
        exit 0
    fi

    # Retrieve the source file name
    readonly SOURCE_FILENAME="$1"
    readonly SOURCE_BASENAME=$(basename $SOURCE_FILENAME)

    readonly NUM_EXTRACT_FRAMES=${2:-30}
    readonly DIST_FOLDER=${3:-"./dist"}

    # Get Video Duration in seconds and build string "num_frames/num_seconds" 
    # to pass as an argument for the ffmpeg -fps option
    float_vid_seconds=$(ffprobe -i $SOURCE_FILENAME -show_format -v quiet | sed -n 's/duration=//p')
    vid_seconds=${float_vid_seconds%.*}
    vid_fps_string=$NUM_EXTRACT_FRAMES"/"$vid_seconds

    # Create tmp dist folder
    mkdir -p $DIST_FOLDER

    # Actual extracting
    ffmpeg -i $SOURCE_FILENAME -vf fps=$vid_fps_string -qscale:v 2 -f image2 -c:v mjpeg $DIST_FOLDER/$SOURCE_BASENAME-frame-%03d.jpg