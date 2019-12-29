# Project Description
An Interactive, command line, c++ program to easily add/remove segments from a single gstreamer pipeline.

The program should be a command-line interface to start/stop any of the following three functions at any time:

1. Preview camera on display with a PNG graphic overlay/watermark
2. Capture h.264 video/audio to a file path
3. Stream audio/video to a RTMP server url

GPU accelerated GStreamer functions by Nvidia should be used. Documentation here: https://developer.nvidia.com/embedded/dlc/l4t-accelerated-gstreamer-guide-32-1

I can provide remote access to computer with attached camera.

# Program Specifications

Arch: Arm
Language: C++

Program should stay alive, accepting commands via stdin, returning information through stdout and errors through stderr.

### Commands:
`preview-start`<br />
Adds nvoverlaysink to pipeline, showing live preview on display <br />

`preview-stop` <br />
Removes nvoverlaysink <br />

`capture-start {/path/to/filename.mp4}`<br />
Adds filesink to pipeline, recording h264 encoded video <br />

`capture-stop`<br />
Stops filesink <br />

`stream-start {RTMP Server URL}`<br />
Adds rtmpsink to pipeline, streaming audio/video to server <br />

`stream-stop`<br />
Stops rtmpsink <br />

`quit`<br />
Stops pipeline and exits program <br />



# PREVIEW ONLY
### Problem: Missing PNG overlay
```gst-launch-1.0 -v v4l2src device=/dev/video0 ! 'video/x-raw, format=(string)UYVY, width=(int)1920, height=(int)1080, framerate=(fraction)30/1' ! videoflip method=upper-right-diagonal ! nvvidconv ! 'video/x-raw(memory:NVMM), format=(string)NV12' ! nvoverlaysink```

# RECORD VIDEO ONLY
### Problem: Missing audio
```gst-launch-1.0 -v v4l2src device=/dev/video0 ! 'video/x-raw, format=(string)UYVY, width=(int)1920, height=(int)1080, framerate=(fraction)30/1' ! videoflip method=upper-right-diagonal ! nvvidconv ! 'video/x-raw(memory:NVMM), format=(string)NV12' ! nvv4l2h264enc bitrate=8000000 ! h264parse ! qtmux ! filesink location=video.mp4 -e```

# STREAM TO YOUTUBE
### Problem: Missing microphone audio
```gst-launch-1.0 -v v4l2src device=/dev/video0 ! 'video/x-raw, format=(string)UYVY, width=(int)1920, height=(int)1080, framerate=(fraction)30/1' ! videoflip method=upper-right-diagonal ! videoconvert ! x264enc bitrate=2000 byte-stream=false key-int-max=60 bframes=0 aud=true tune=zerolatency ! h264parse ! flvmux name=mux audiotestsrc ! queue ! audioconvert ! voaacenc ! aacparse ! mux. mux. ! rtmpsink location="RTMP URL"```
