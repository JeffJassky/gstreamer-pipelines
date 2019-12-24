# 1080p PREVIEW ONLY
```gst-launch-1.0 -v v4l2src device=/dev/video0 ! 'video/x-raw, format=(string)UYVY, width=(int)1920, height=(int)1080, framerate=(fraction)30/1' ! videoflip method=upper-right-diagonal ! nvvidconv ! 'video/x-raw(memory:NVMM), format=(string)NV12' ! nvoverlaysink```

# 1080p RECORD VIDEO ONLY
```gst-launch-1.0 -v v4l2src device=/dev/video0 ! 'video/x-raw, format=(string)UYVY, width=(int)1920, height=(int)1080, framerate=(fraction)30/1' ! videoflip method=upper-right-diagonal ! nvvidconv ! 'video/x-raw(memory:NVMM), format=(string)NV12' ! nvv4l2h264enc bitrate=8000000 ! h264parse ! qtmux ! filesink location=video.mp4 -e```

# 1080p STREAM TO YOUTUBE
```gst-launch-1.0 -v v4l2src device=/dev/video0 ! 'video/x-raw, format=(string)UYVY, width=(int)1920, height=(int)1080, framerate=(fraction)30/1' ! videoflip method=upper-right-diagonal ! videoconvert ! x264enc bitrate=2000 byte-stream=false key-int-max=60 bframes=0 aud=true tune=zerolatency ! h264parse ! flvmux name=mux audiotestsrc ! queue ! audioconvert ! voaacenc ! aacparse ! mux. mux. ! rtmpsink location="RTMP URL"```
