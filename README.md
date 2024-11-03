# PS4_Stereo_ROS_SLAM

Development of a ROS node for the PS4 VR usb camera. This will allow SLAM to be performed using the VR camera and ROS Noetic.

## Requirements

The following packages and softwares are required. A good understanding of how each of them function will also help mitigate any issues.

1. Ubuntu 20.04
2. ROS Noetic
3. PS4 Camera (modified to connect through USB 3.0)
4. pyusb. Install with:

```bash
sudo apt install python3-usb
pip3 install pyusb
```

5. camset. Used to test the camera. Follow the [installation guide](https://github.com/azeam/camset?tab=readme-ov-file#installation) of the official repo.

6. camera_calibration ROS package ([ROS page](http://wiki.ros.org/camera_calibration)).
   Install with:

```bash
sudo apt install ros-noetic-camera-calibration
```

7. stereo_image_proc ROS package ([ROS page](https://wiki.ros.org/stereo_image_proc)).
   Install with:

```bash
sudo apt install ros-noetic-stereo-image-proc
```

8. rtabmap_ros ROS package ([ROS page](http://wiki.ros.org/rtabmap_ros)).
   Install with:

```bash
sudo apt install ros-noetic-rtabmap-ros
```

## Check that the camera is working

After having modified the camera to connect through USB 3.0, connect it to your computer and run:

```bash
lsusb
```

In the output you should see a record like `OmniVision Technologies, Inc. USB Camera-OV580`. If you do not see this and you modified the cable instead of using an adapter, check again the modification you made.

## Setup

1. Create a folder for your project. Inside this folder create a `catkin_ws` folder and inside this create an `src` folder:

```bash
mkdir -p catkin_ws/src
```

2. Move inside the `src` folder and clone this repository

3. To build the project, move to the `catkin_ws` folder and run:

```bash
catkin_make
```

4. From the `catkin_ws` folder run:

```bash
source ./devel/setup.bash
```

5. To load the firmware on to the PS4 camera run:

```bash
sudo python3 ./src/Firmware_loader/ps4eye_init.py
```

The output of the script should confirm that the camera has been successfully setup.

6. Run `camset` and mark down the input device number of the PS4 camera. For example, if your device shows up as `/dev/video2` the input device number is 2. In camset, set the cameras exposure to _shutter priority_ instead of _automatic_.

7. In `psvr_cam_publisher.py` set the `CAM_NUMBER` variable to the device number marked down from the previous step.
8. Complete the [Camera calibration](#camera-calibration).
9. Run the `psvr_launch` launch file. `roslaunch PSVR_cam_core psvr_launch.launch`

## Camera calibration

The following steps should be taken to calibrate your PS4 camera. The repository comes with calibrations files but more accurate results can be achieved after calibrations.

1. Download, print, and build a calibration board. You can use the calibration board that you can find in the `extras` folder. Mount it to a flat surface.
2. Follow the [tutorial](https://wiki.ros.org/camera_calibration/Tutorials/StereoCalibration) for a stereo camera.
3. At the end of the calibration, instead of committing the calibration parameters to the camera, press the `Save` button so that the parameters will be saved to a file. It is usually saved in the `tmp` folder with the name of `calibrationdata.tar.gz`. You can check the output of the terminal where you ran camera_calibration to find the exact location of the file.
4. Extract the contents of the file.
5. Copy and paste the left and right camera calibration parameters to their respective .yaml files found in the calibration folder. Be sure to read the included comments in each file.

## Node Map

If setup correctly the node map should look like the following.

![example nodemap here](https://i.imgur.com/3PNwTHu.png)

## Extras

A few extras have been included in this package.

1. The printable handle as an .stl file
2. A small calibration board
3. Two rqt_reconfigure tunning files. One for StereoBM and one for StereoSGBM
