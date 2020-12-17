# Installation
## Prerequisites
### ROS
See ROS installation guide [here](https://wiki.ros.org/melodic/Installation) for further informations.
### Realsense SDK 2.0 (librealsense2)
The fastest way to get the current stable version is by installing the ROS distributed Debian package:
```bash
sudo apt-get update
sudo apt-get install ros-melodic-realsense2-camera
```
For alternative ways of installation and further inforamtions about librealsense2 refer to the official [git-page](https://github.com/IntelRealSense/librealsense).

## Repo Installation
### 1) Clone Repo
Clone this repo to desired `PROJECT_BASE_DIR` and initialize the catkin workspace:
```bash
mkdir PROJECT_BASE_DIR
git clone https://github.com/CORaisch/Realsense-SLAM.git PROJECT_BASE_DIR
cd PROJECT_BASE_DIR
```
For handy usage add this to your shells rc file:
```bash
echo "source PROJECT_BASE_DIR/devel/setup.{bash|zsh|sh}" >> ~/.{bashrc|zshrc|shrc}
```
### 2) Install Realsense ROS Wrapper
Clone the official git from [here](https://github.com/IntelRealSense/realsense-ros) into your catkin workspace:
```bash
cd PROJECT_BASE_DIR/src
git clone https://github.com/IntelRealSense/realsense-ros.git
cd realsense-ros/
git checkout `git tag | sort -V | grep -P "^\d+\.\d+\.\d+" | tail -1`
```
The ROS package [dynamic-reconfigure](https://wiki.ros.org/dynamic_reconfigure) is required for building. It's a helpful tool that allows you to reconfigure parameters of a ROS node at runtime. Install it with:
```bash
sudo apt-get update
sudo apt-get install ros-melodic-dynamic-reconfigure
```
Then build your catkin workspace:
```bash
cd PROJECT_BASE_DIR
catkin_make clean
catkin_make -DCATKIN_ENABLE_TESTING=False -DCMAKE_BUILD_TYPE=Release
catkin_make install
```
### Install Octomap
```bash
sudo apt-get update
sudo apt-get install ros-melodic-octomap-server ros-melodic-octomap-rviz-plugins
```
### Install RTAB-Map
```bash
sudo apt-get update
sudo apt-get install ros-melodic-rtabmap-ros
```
## Optional
[Octovis](https://wiki.ros.org/octovis) is a nice tool to visualize octomaps and octrees. Another simple but helpful tool to quickly visualize raw images directly from a rostopic is [image-view](https://wiki.ros.org/image_view).
```bash
sudo apt-get update
sudo apt-get instsall ros-melodic-octovis
sudo apt-get instsall ros-melodic-image-view
```

# Usage
## rs_d400_and_t265_fixed_mount.launch
Launchfile allows to stream all necessarry data from t265 and d400 sensors. All relevant tf transforms are setup assuming that both sensors are mounted on the 3D printed mount given in `data/bracket_*.stl`.
### Example usage:
* run `roslaunch launcher rs_d400_and_t265_fixed_mount.launch` to stream only depth and pose data. No images will be streamed by default to reduce bandwidth.
* run `roslaunch launcher rs_d400_and_t265_fixed_mount.launch enable_{color|infra|fisheye}:=true` to stream depth and pose data alongside the enbaled images (e.g. `enable_infra:=true` will enable both stereo infrared imagers of the d400).
* run `roslaunch launcher rs_d400_and_t265_fixed_mount.launch enable_color:=true allow_no_texture_points:=false` to stream depth and pose data. The pointcloud only contains those points where color information is available and can be used for cleaner visualization in combination with `enable_color:=true`.

## octomap_mapping.launch
Launchfile will start a octomap_server for integrating the d400 pointclouds into an octomap. Run this after `rs_d400_and_t265_fixed_mount.launch`.
### Example usage:
* run `roslaunch launcher octomap_mapping.launch` to start the octomap_server alongside rviz. Octomap_server will immediately start integrating the pointclouds into the octomap.
* run `roslaunch launcher octomap_mapping.launch rviz:=false` to start without rviz visualization.
* run `roslaunch launcher octomap_mapping.launch resolution:=0.03` to start the octomap_server with a map voxel size of 0.03 meters. The bigger the resolution the more performant octomap will run.
* to save an octomap into a .ot (float format) or .bt (binary format) file run `rosrun octomap_server octomap_saver -f FILENAME.{ot|bt}`.

## rtabmap.launch
Launchfile will start rtabmap for integrating the d400 pointclouds into an optimized map. Also incorporates the d400 color image for visual optimizations. Run this after `rs_d400_and_t265_fixed_mount.launch`.
* run `roslaunch launcher rtabmap.launch` to start rtabmap alongside rviz. Rtabmap will immediately start integrating the pointclouds into the map. Rtabmap is only used for mapping, all Visual Odometry information will be taken from T265 only. Other than octomap, rtabmap will also optimize for the camera trajectory during Bundle Adjustment. For the corrected odometry rtabmap will republish a tf transform from `/map` to `/t265_odom_frame`, i.e. the odometry published at topic `/t265/odom/sample` will be automatically corrected since its defined in the same tf frame.
* run `roslaunch launcher rtabmap.launch args:=-d` to start rtabmap as above, but it will delete the databese at on startup (also achieved by `args:=--delete_db_on_start`). Without this argument rtabmap will load the database from before.
* run `roslaunch launcher rtabmap.launch rviz:=false` to start rtabmap without rviz visualization.
* run `roslaunch launcher rtabmap.launch rviz:=false rtabmapviz:=true` to start rtabmap alongside rtabmapviz, instead of rviz. Rtabmapviz is the official visualization tool coming with the rtabmap install.
* to save an octomap into a .ot (full format) or .bt (binary format) file run `rosrun octomap_server octomap_saver -f FILENAME.{ot|bt} octomap_{full|binary}:=/rtabmap/octomap_{full|binary}`.

# ROS Topics Overview
## rs_d400_and_t265_fixed_mount.launch Topics
TODO add documentation
## octomap_mapping.launch Topics
TODO add documentation
## rtabmap.launch Topics
TODO add documentation
