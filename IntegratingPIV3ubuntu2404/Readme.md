# **Integrating the Raspberry Pi Camera Module V3 with Raspberry Pi 4B on Ubuntu 24.04**

## **Introduction**
This guide provides step-by-step instructions for setting up and using the Raspberry Pi Camera Module V3 with a Raspberry Pi 4B running Ubuntu 24.04. It covers disabling legacy camera support, configuring the firmware, installing necessary dependencies, detecting the camera, streaming video, and integrating with ROS2.

---

## **1. Disable Legacy Camera Support**
Before setting up the Pi Camera Module V3, ensure that the legacy camera mode is disabled. If enabled, follow these steps:

### **1.1 Install and Run raspi-config**
```bash
sudo apt-get install raspi-config
sudo raspi-config
```

### **1.2 Disable Legacy Camera Mode**
1. Open `raspi-config`.
2. Navigate to **"Interfacing Options"**.
3. Locate **"Legacy Camera Mode"** and disable it.
4. Save and exit.

Reboot the Raspberry Pi after making these changes:
```bash
sudo reboot
```

---

## **2. Enable Camera in Firmware**
Ubuntu does not automatically detect the camera module, so it must be enabled manually by modifying the firmware configuration.

### **2.1 Edit the Configuration File**
Run the following command to open the `config.txt` file:
```bash
sudo nano /boot/firmware/config.txt
```

### **2.2 Add the Following Lines at the End**
```bash
dtoverlay=imx708
camera_auto_detect=0
gpu_mem=256
```

### **2.3 Save and Exit**
- Press **CTRL + X** to exit.
- Press **Y** to confirm saving.
- Press **Enter** to apply changes.

### **2.4 Reboot the Raspberry Pi**
```bash
sudo reboot
```

---

## **3. Install Necessary Dependencies**
Install `libcamera`, the required library for handling camera operations:
```bash
sudo apt-get install libcamera
```

---

## **4. Check if the Camera is Detected**
After installation, verify that the camera is detected using:
```bash
cam -l
```
### **Expected Output (Example)**
```plaintext
[0:05:06.068627967] [3945] ERROR IPAModule ipa_module.cpp:172 Symbol ipaModuleInfo not found
[0:05:06.068800134] [3945] ERROR IPAModule ipa_module.cpp:292 v4l2-compat.so: IPA module has no valid info
[0:05:06.069074153] [3945]  INFO Camera camera_manager.cpp:284 libcamera v0.2.0
[0:05:06.254767133] [3948]  WARN RPiSdn sdn.cpp:39 Using legacy SDN tuning - please consider moving SDN inside rpi.denoise
[0:05:06.258797351] [3948]  INFO RPI vc4.cpp:401 Registered camera /base/soc/i2c0mux/i2c@1/imx708@1a to Unicam device /dev/media1 and ISP device /dev/media2
Available cameras:
1: 'imx708' (/base/soc/i2c0mux/i2c@1/imx708@1a)
```
The detected camera will appear at the bottom of the output.

---

## **5. Streaming Video (For General Use)**
If you are not using ROS2, you can test the camera stream using GStreamer.

### **5.1 Test Streaming with GStreamer**
Run the following command to stream video from the camera to an OpenGL-accelerated display:
```bash
gst-launch-1.0 libcamerasrc camera-name="/base/soc/i2c0mux/i2c@1/imx708@1a" ! queue ! glimagesink
```

---

## **6. Integrating with ROS2**
For users working with ROS2, the `camera_ros` package can be used to integrate the Pi Camera Module V3.

### **6.1 Install `camera_ros` for ROS2 Jazzy**
```bash
sudo apt-get install ros-jazzy-camera-ros
```

### **6.2 Run the ROS2 Camera Node**
```bash
ros2 run camera_ros camera_node
```

### **6.3 View the Camera Stream using RQT**
To visualize the camera stream, run the `rqt_image_viewer` node:
```bash
ros2 run rqt_image_viewer rqt_image_viewer
```
Once the interface opens, select the topic `/camera/image_raw` to view the video feed.

---
