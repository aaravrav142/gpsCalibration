# iMorpheus.ai - high availability sub-meter precise GPS
Through algorithm fusion of multiple data sources from different sensors such as lidar, radar, camera, gps, imu and point cloud, iMorpheus.ai brings about an high availability precision GPS measurement to outdoor robotics developer. iMorpheus integrate a number of advanced algorithm such as slam, kalman filter, ICP, feature selection and Gaussian Process. 
#### We believe precise GPS signal should obtained by computing from cloud rather than measure the satellite, and soly software and cloud can solve the problem rather than expensive hardware. So that we committed into only software to solve the problem. 
![image](https://github.com/iMorpheusAI/gpsCalibration/raw/develop/demo/demo.gif)
## Copyrights
![License](https://img.shields.io/badge/License-Apache2.0-blue.svg)

## Current Version - alpha
The alpha version is a software package operate under off-line mode and using point clould and GPS to give you accurate GPS. Each GPS signal produced also comes with confidence level. 

## Installation Environment

### 1. Operating System
Ubuntu 14.04, 16.04

### 2. Robot Operating System - ROS
ROS provides libraries and tools to help software developers create robot applications. It provides hardware abstraction, device drivers, libraries, visualizers, message-passing, package management, and more. ROS is licensed under an open source, BSD license.
##### [Learn More](http://wiki.ros.org/ROS/Tutorials)

### 3. Point Cloud Library - PCL
PCL is a large scale, open source project for 2D/3D image and point cloud processing.
##### [Learn More](http://pointclouds.org/documentation/)

### 4. EIGEN
Eigen is a C++ template library for linear algebra: matrices, vectors, numerical solvers, and related algorithms.
##### [Learn More](http://eigen.tuxfamily.org/index.php?title=Main_Page)

## Quick Installation
Under 'install' directory of the project, We provide install scripts for beginners and pros. The scripts include installing ROS, PCL and EIGEN. <br/>
a) Basic version (Recommended for general users.) - one hour installation time.<br/>
b) Professional version (Recommended for developers.) 

- First download this repertory from github to your local system. 
- For ubuntu 14.04 basic version install, type commands:
```
$ cd install
$ sudo ./install_u1404_basic.sh 
```

## Module description
### 1. Modules
gpsCalibration has three modules include GPS, LOAM and Calibration.

#### 1.1 GPS module
This module processes GPS data and transforms them into local coordinate system.

#### 1.2 LOAM module
LOAM(Laser Odometry and Mapping) is a real-time method for state estimation and mapping using 3D lidar. The program contains two major threads running in parallel. 
The "odometry" thread computes motion of the lidar between two sweeps, at a higher frame rate. It also removes distortion in the point cloud caused by motion of the lidar. The "mapping" thread takes the undistorted point cloud and incrementally builds a map, while simultaneously computes pose of the lidar on the map at a lower frame rate. The lidar state estimation is combination of the outputs from the two threads.

To learn more about LOAM, please refer to [paper](http://www.frc.ri.cmu.edu/%7Ejizhang03/Publications/RSS_2014.pdf).

This package is a simple modified copy of loam_velodyne GitHub repository from laboshinl, https://github.com/laboshinl/loam_velodyne

We modified laserOdometry.cpp and scanRegistration.cpp to enhance lidar io, and added some code on transformMaintenance.cpp to get lidar's track with timestamp and map.

#### 1.3 Calibration module
This module calls GPS module and LOAM module and use their data as input.

We use timestamped point set registration to match GPS and LOAM tracks in two steps. First, we calculate long LOAM segments and find their least absolute deviations with GPS track by means of iteratively re-weighted least squares. Second, the weights from the first step are used to register overlapping short LOAM segments with GPS track by means of weighted least squares.

The final output is calibrated GPS track. We only work on 2D GPS tracks in this version. Altitudes are set to a constant for the purpose of demonstration in Google Earth.

#### 1.4 Flowchart
![image](https://github.com/iMorpheusAI/gpsCalibration/raw/master/demo/flowchart.jpg)
## How to compile and run the project
1.*Make sure that you have the message bag. it includes follow message types
  sensor_msgs/PointCloud2. and GPS coordinates matched with your run trail although 
  GPS is not accurate and not continuous in time.
  If you don’t have lidar or GPS data, don’t worry, we have some data in advance for you to have a try.*
##### small size demo data -> [[download-191MB]](http://www.imorpheus.ai/download/dataForDemo/smallSizeDemoData)
##### large size demo data -> [[download-2.6GB]](http://www.imorpheus.ai/download/dataForDemo/largeSizeDemoData)
Download compressed demo data and type commands to decompress: 
```
$ tar -zvxf small_size_demo_data.tar.gz
$ tar -zvxf large_size_demo_data.tar.gz 
```
After decomproession of small_size_demo_data.tar.gz, you will see:
```
$ miniDemo/
   ├── bag_0
   └── original_gps_data.txt
```
2.*Open the globalConfig.py in directory "gpsCalibration/" and set needed file directory correctly.*
```
    User Parameters:
    Input filenames:
    1. bag_input_filename:
       input point cloud bag file list
       bag_input_filename= "./data/bag_list.txt"

    2. gps_input_filename:
       original GPS data with type GPRMC
       gps_input_filename= "./data/original_gps_data.txt"
       
    Output filenames:
    3. gps_original_filename:
       original GPS track type with KML format
       gps_original_filename=  "./data/original_gps_track.kml"
       
    4. gps_improved_filename:
       imporved accurate GPS track type with KML format
       gps_improved_filename=  "./data/calibrated_gps_track.kml"
```
3.*In directory gpsCalibration, run commands:*
```
$ catkin_make 
$ source setup.sh
$ cd data
$ vi bag_list.txt
  modify the point cloud data bag path: /home/xxx/downloads/miniDemo/bag_0
$ cp /home/xxx/downloads/miniDemo/original_gps_data.txt ./
```
4.*Okay, you can run command:*
```
$ ./run.py
```

5.*Finally, you get a global position system coordinates matched with your run trail. It is accurate and reliable!*

## About system input and output
### 1. Input
#### 1.1 .bag
A bag is a file format in ROS for storing ROS message data.
 
#### 1.2 GPS
The GPSRMC is protocol of GPSRMC's communication, and the format is:
$GPRMC,085223.136,A,3957.6286,N,11619.2078,E,0.06,36.81,180908,,,A\*57

### 2. Output
*The results of the program are stored in /data. We provide calibrated GPS in kml formats.
If you want to see GPS track in Google Earth, you need to use kml.
You can find Google Earth here: https://developers.google.com/kml/?hl=en-US.*

### 3. Example
  We show the calibrated results of large size demo data. 
#### 3.1 demo results
![image](https://github.com/iMorpheusAI/gpsCalibration/raw/develop/demo/demo1.png)
![image](https://github.com/iMorpheusAI/gpsCalibration/raw/develop/demo/demo2.png)
##### [Results Download](http://www.imorpheus.ai/download/dataForDemo/largeSizeDemoResult)
Download compressed demo results and type commands to decompress: 
```
$ tar -zvxf large_size_demo_result.tar.gz
$ tree large_size_demo_result
  ├── calibrated_gps_track.kml
  └── original_gps_track.kml
```
Open these KML files in google earth, you can check your results.
##### [See More](http://www.imorpheus.ai/demo/)

## Questions
  You can ask any question [here](https://github.com/iMorpheusAI/gpsCalibration/issues) or send us [emails](product@imorpheus.ai).
