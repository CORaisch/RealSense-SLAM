# Installation
TODO

# Usage
## rs_d400_and_t265_fixed_mount.launch
Launchfile allows to stream all necessarry data from t265 and d400 sensors. All relevant tf transforms are setup assuming that both sensors are mounted on the 3D printed mount given in `data/bracket_*.stl`.
### Example usage:
* run `roslaunch realsense2_camera ds_d400_an_t265_fixed_mount.launch` to stream only depth and pose data. No images will be streamed by default to reduce bandwidth.
* run `roslaunch realsense2_camera ds_d400_an_t265_fixed_mount.launch enable_{color|infra|fisheye"}:=true` to stream depth and pose data alongside the enbaled images (e.g. `enable_infra:=true` will enble both stereo infrared imagers of the d400).
* run `roslaunch realsense2_camera ds_d400_an_t265_fixed_mount.launch allow_no_texture_points:=false` to stream depth and pose data. The pointcloud only contains those points where color information is available (use for cleaner visualization if combined with `enable_color:=true`).
### Example ROS Topics:
TODO
