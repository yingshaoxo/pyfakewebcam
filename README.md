# pyfakewebcam

An API for writing RGB frames to a fake webcam device on Linux!

**Compatible with Python2.7 and Python3.x**

## Author 0
**Author:** John Emmons
**Email:** mail@johnemmons.com

**Disclaimer:** I wrote this project when I was a university
student. I now employed full time so I don't have time to keep things
update or add new features :(. Please feel free to fork!

## Author 1
**Author:** yingshaoxo
**Email:** yingshaoxo@gmail.com

**Disclaimer:** I just simply want to keep this repo alive.

## Usage

### Step 1:
install pyfakewebcam by `sudo pip3 install pyfakewebcam`

### Step 2:
install v4l2 by `sudo apt install v4l2loopback-utils`

### Step 3:
set v4l2 by `modprobe v4l2loopback devices=1` or `sudo modprobe v4l2loopback`

### Step 4:
check the right virtual cam by `ls /dev | grep -P '^video\d+$'`. use the last one, for example, `/dev/video3`

> you can also use `v4l2-ctl --list-devices` if you know what you are doing.

### Step 5:
run python script:
```py
import time
import pyfakewebcam
import numpy as np

blue = np.zeros((480,640,3), dtype=np.uint8)
blue[:,:,2] = 255

red = np.zeros((480,640,3), dtype=np.uint8)
red[:,:,0] = 255

camera = pyfakewebcam.FakeWebcam('/dev/video3', 640, 480)

while True:

    camera.schedule_frame(red)
    time.sleep(1/30.0)

    camera.schedule_frame(blue)
    time.sleep(1/30.0)
```

**Be careful, here I'm using the `/dev/video3`, you may need to change this to other number depends on previous step.**

### Step 6:
check the result by `ffplay /dev/video3`

## OLD Tutorial

### installation
```
# use pip to get the latest stable release
pip install pyfakewebcam

# use git to install the latest version
git clone https://github.com/jremmons/pyfakewebcam.git
cd pyfakewebcam
python setup.py install
```

### dependencies
```
# python
pip install numpy

#### linux
apt-get install v4l2loopback-utils

#### linux (optional)
apt-get install python-opencv # 10x performance improvement if installed (see below)
apt-get install ffmpeg # useful for debugging
```

### performance

When I run the `examples/example.py` script (640x360 resolution)
on an Intel i7-3520M (2.9GHz, turbos to 3.6 GHz), the time to
schedule a single frame is *~3 milliseconds* (with opencv
installed). **You can use this library without installing opencv**,
but it is almost 10x slower; time to schedule a frame without
opencv is *~26 milliseconds* (RGB to YUV conversion done with
numpy operations).

If your goal is to run at 30Hz (or slower) and ~26 milliseconds of
delay is acceptable in your application, then opencv may not be
necessary.

### usage

Insert the v4l2loopback kernel module.

```
modprobe v4l2loopback devices=2 # will create two fake webcam devices
```

Example code.

```python
# see red_blue.py in the examples dir
import time
import pyfakewebcam
import numpy as np

blue = np.zeros((480,640,3), dtype=np.uint8)
blue[:,:,2] = 255

red = np.zeros((480,640,3), dtype=np.uint8)
red[:,:,0] = 255

camera = pyfakewebcam.FakeWebcam('/dev/video1', 640, 480)

while True:

    camera.schedule_frame(red)
    time.sleep(1/30.0)

    camera.schedule_frame(blue)
    time.sleep(1/30.0)
```

Run the following command to see the output of the fake webcam.
```
ffplay /dev/video1
```
