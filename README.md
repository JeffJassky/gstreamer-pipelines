# Broken GStreamer Pipelines

I’m am creating a photo/video recording application that mimics a standard smartphone camera application. The application is an nvidia/linux-based kiosk photo/video recording booth.

The goal of using gstreamer in the application is to preview, capture, and stream live audio and video from a UVC camera and asla/pulse audio device.

I am new to gstreamer. I have pieced together these pipelines from reading documentation and examples. They are close to what I need but need improvements or debugging. I’ve done my best to document the pipelines below. 

## Pipeline #1 - Camera Preview
**Goal:** Preview a 1280x960 live feed of the camera on the display<br/>
**Improvement:** I’d like to overlay a 1280x960 PNG graphic over the preview (See preview-overlay.png).<br/>
```gst-launch-1.0 -vv v4l2src device=/dev/video0 ! image/jpeg,width=1280,height=960,framerate=30/1 ! jpegparse ! jpegdec ! video/x-raw ! videoflip method=upper-left-diagonal ! nvoverlaysink sync=false```

## Pipeline #2 - Recording a Video
**Goal:** Preview a 1280x960 live feed of the camera on the display + capture 1280x960 video and audio to a compressed Quicktime mp4 file.<br/>
**Improvement:** Pipeline locks up. I cannot tell why. Preview missing png overlay.<br/>
```gst-launch-1.0 -vv v4l2src ! image/jpeg,width=1280,height=960,framerate=30/1 ! jpegparse ! jpegdec ! video/x-raw ! videoflip method=upper-left-diagonal ! tee name=t t. ! queue ! omxh264enc bitrate=6000000 preset-level=2 control-rate=1 EnableTwopassCBR=true profile=8 ! video/x-h264,stream-format=byte-stream,framerate=30/1 ! h264parse ! qtmux0. pulsesrc ! audio/x-raw,rate=44100,channels=2 ! queue ! audioconvert ! audiorate ! voaacenc bitrate=128000 ! queue ! qtmux faststart=true ! queue ! filesink location="video.mp4" t. ! queue ! nvoverlaysink sync=false -e```

## Pipeline #3 - Livestream
**Goal:** Preview a 1280x960 live feed of the camera on the display + stream appropriately sized and compressed video and audio to Facebook and YouTube RTMP servers.<br/>
**Improvement:** Audio/video not syncing. Significant buffering and performance issues. Preview missing png overlay.<br/>
```gst-launch-1.0 -v flvmux name=mux streamable=true ! rtmpsink location="URL HERE" v4l2src ! "image/jpeg,width=1280,height=960,framerate=30/1" ! jpegparse ! jpegdec ! videoconvert ! videorate ! videoflip method=upper-left-diagonal ! identity sync=true ! x264enc bitrate=2500 key-int-max=60  ! video/x-h264,profile=baseline ! h264parse ! queue ! mux. autoaudiosrc ! audio/x-raw,rate=44100,channels=2 ! identity sync=true ! voaacenc bitrate=96000 ! queue ! mux.```
