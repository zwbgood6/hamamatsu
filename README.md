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

- capturing status
  - check the capturing status: dcamcap_status()
  - return # of captured images and the last frame's index: dcamcap_transferinfo()
  - VIP: call these function after the arrival of new frames to release the burden for CPU
  
- software trigger
  - use software trigger: set DCAMPROP_TRIGGERSOURCE_SOFTWARE to DCAM_IDPROP_TRIGGERSOURCE, and start capturing
  - DCAM modules fires a trigger to device, it will wait until the camera is ready to receive the trigger signal: dcamcap_firetrigger()
  
- recording
  - record images to disk simultaneously while capturing
  - steps: prepare HDCAMREC handle -> call dcamcap_record() -> call dcamcap_start()
  - one time function: call dcamcap_record() again for another capturing
  
