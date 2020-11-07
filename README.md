# Hamamatsu

## install DCAM-API for linux 

intro: application interface, including common methods

linux version: 18.04 (don't use 20.04)

camara type: C12300-321B

install the latest gcc, check gcc version: 'gcc --version'

install the linux-headers: 'sudo apt get linux-headers-1.14.0-1100-oem' (should be this version when you boot the computer - ubuntu advanced option)

go to BIOD when rebooting, disable the secutiry boot, enable legacy support

then install all install.sh files in apt, runtime, driver and follow the guideline in the pdf

## install DCAM-SDK

intro: software development kit

## capture control

- start and stop capturing
  - allocate a buffer -> start decam capture -> stop decam capture
  - function: decambuf_alloc() -> decamcap_start() -> decamcap_stop()
  - misc: 
    - continous capturing - DACAMCAP_START_SEQUENCE: when the capturing reaches the final allocated frame, it will go back to the first frame and overwritten the existing data.
    - single cycle capturing - DCAMCAP_START_SNAP: the capture stops when the capturing reaches the final allocated frame.
