# Hamamatsu

## install DCAM-API for linux 

Intro: application interface, including common methods.

Linux version: `18.04` (don't use `20.04`).

Camara type: `C12300-321B`.

### Install the latest `gcc`

check gcc version

```
gcc --version
```

if you don't have `gcc`, run

```
sudo apt update
``` 

and 

```sudo apt install build-essential```.

### Install the linux-headers: 

```
sudo apt-get linux-headers-4.15.0-1100-oem
``` 
  
### Install the linux-image:
  
```
sudo apt-get linux-image-4.15.0-1100-oem
```

You should reboot your PC with this kernel version. It can be found in the GRUB menu - Ubuntu Advanced Option.

### BIOS setting

Go to BIOS when rebooting, disable the `security boot`, enable `legacy support`, 

some PCs only need to disable `security boot` and they don't need to enable `legacy support`.

### running install.sh

Follow the guidelines in the `README.txt` in the folder `api` to install all `install.sh` files in `api`, `api/driver/firebird`, and `api/runtime`.

Read and follow the guidelines in the `FireBird_QuickStart_Linux.pdf` file in the directory `DCAM-API_for_Linux_v4.0.5868_r2/DCAM-API_for_Linux_v4.0.5868/api/driver/firebird/as-dcam-lin64-8.13.3/as-dcam-lin64-8.13.3-1`.

## install DCAM-SDK

intro: software development kit

### DCAM API modules

#### initialization, termination, HDCAM handle

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

#### acquisition buffer control

- allocation, attach, and release
  - allocation (prepare a receiving buffer):
      - method 1: dcambuf_alloc() -> dcambuf_lockframe()/dcambuf_copyframe() -> dcambuf_release(). allocate the frame buffer in the DCAM module -> access the data or copy the data -> release the internal receiving buffer in the DCAM module
      - method 2: dcambuf_attach() -> dcambuf_release(). attach a momery buffer and the image will be written to the user buffer (may not be supported) -> detach the memory

- lockframe and copyframe
  - dcambuf_lockframe(): return a pointer to the data so we can access the image
  - dcambuf_copyframe(): copy the image from primary buffer to reserved memory 

#### capture control

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
  
#### recording control

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
