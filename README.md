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

# DCAM API modules

## initialization, termination, HDCAM handle

- initialization
  - steps: begin DCAM-API and initialize driver -> get HDCAM handle for the device 
  - functions: dcamapi_init() -> dcamdev_open()
  - dcamapi_init(): 
    - DCAMAPI_INIT: choose initialization options; iDeviceCount show how many devices are connected
  - dcamdev_open():
    - DCAMDEV_OPEN: choose a device by index (0 - IDeviceCount-1)

- termination
  - close the HDCAM handle: dcamdev_close()
  - terminate the API and unload all of the modules: dcamapi_uninit()

- HDCAM handle
  - handle is the device on HDCAM-API
  - handle specifies the camera

- get the device and DCAM information 
  - dcamdev_getstring()

## acquisition buffer control

- allocation, attach, and release
  - allocation:
    - steps: 

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
  
## recording control

- prepare, start, and stop recording
  - open decam recording file -> call recoding function -> call dcam start function 
  - function: dcamrec_open() -> call dcamcap_record() when in READY state -> call dcamcap_start() -> call dcamrec_close() (if not calling the last function, some data may be lost)

- pause, resume, and status
  - pause the disk recorder: dcamrec_pause()
  - resume the disk recording during capturing, dcamrec_resume()
  - get recording status: dcamrec_status(). The recording is in process when the flag is DCAMREC_STATUSFLAG_RECORDING

- access image data
  - access the recorded frames: dcamrec_lockframe() or dcamrec_copyframe()
  - not recommend during a recording
