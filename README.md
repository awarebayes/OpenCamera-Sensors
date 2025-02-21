![OpenCamera Sensors logo](https://imgur.com/7qjCtgp.png)

[![Build Status](https://travis-ci.org/MobileRoboticsSkoltech/OpenCamera-Sensors.svg?branch=master)](https://travis-ci.org/MobileRoboticsSkoltech/OpenCamera-Sensors)

OpenCamera Sensors is an Android application for synchronized recording of video and IMU data. It records sensor data (accelerometer, gyroscope, magnetometer) and video with frame timestamps synced to the same clock.

## Install

[<img src="https://fdroid.gitlab.io/artwork/badge/get-it-on.png"
     alt="Get it on F-Droid"
     height="80">](https://f-droid.org/packages/com.opencamera_sensors.app/)

Or get the [latest APK from GitHub releases](https://github.com/MobileRoboticsSkoltech/OpenCamera-Sensors/releases/latest/download/app-release.apk)

## Description

OpenCamera Sensors is an Android application for synchronized recording of video and IMU data. It records IMU data and video with frame timestamps synced to the same clock.

This project is based on [Open Camera](https://opencamera.org.uk/)  —  a popular open-source camera application with flexibility in camera parameters settings, actively supported by the community. By regular merging of Open Camera updates our app will adapt to new smartphones and APIs — this is an advantage over the other video + IMU recording applications built from scratch for Camera2API.

## Usage

![screenshot settings](https://imgur.com/Md2O0sO.png)

- Go to preferences, enable Camera2API and press the **“Enable sync video IMU recording”** switch in "IMU settings..."
- (Optional) **Disable video stabilization** in video preferences of OpenCamera Sensors to minimize preprocessing effects
- (Optional) Enable **save frames** option if you want to verify recorded data correctness
- (Optional) Enable **flash strobe** and specify its frequency in additional sensor settings
- **Switch to video**, setup ISO and exposure time
- **Record video**
- **Get data** from ```DCIM/OpenCamera```:
    - Video file
    - Sensor data and frame timestamps in the directory ```{VIDEO_DATE}```:
        -```{VIDEO_NAME}_gyro.csv```, data format: ```X-data, Y-data, Z-data, timestamp (ns)```
        - ```{VIDEO_NAME}_accel.csv```, data format: ```X-data, Y-data, Z-data, timestamp (ns)```
        - ```{VIDEO_NAME}_magnetic.csv```, data format: ```X-data, Y-data, Z-data, timestamp (ns)```
        - ```{VIDEO_NAME}_location```, data format: ```latitude, lognitude, altitude, timestamp (ns)```
        - ```{VIDEO_NAME}_rotation```, data format: ```azimuth, pitch, roll, timestamp (ns)```
        - ```{VIDEO_NAME}_gravity```, data format: ```X-data, Y-data, Z-data, timestamp (ns)```
        - ```{VIDEO_NAME}_timestamps.csv```, data format: ```timestamp (ns)```
        - ```{VIDEO_NAME}_flash.csv```, data format: ```timestamp (ns)``` (timestamps of when the flash fired)

### Remote recording

- **Connect** smartphone to the same network as PC
- Use scripts provided in ```./api_client/``` directory to **send requests** for the application.
    - *Note: phase, which is returned by* ```start_recording``` *method, can be used to perform synchronization with external devices*
 ![remote control methods](https://www.websequencediagrams.com/files/render?link=6txhpHrdgaebT4DYz2C3SaEQjHM1esYDkJZJvPZcgCJHbRAg3c8hqcJYgOmGirze)

### Synchronized recording on multiple smartphones (RecSync)

**Important**: smartphones are required to support real-time timestamping to be correctly synchronized. This can be checked on the preview message when RecSync is enabled ("Timestamp source" should be "realtime").
![screenshot timestamp source](https://imgur.com/vQHufyV.png)

**Leader smartphone setup:**
- Start a **Wi-Fi hotspot**
- Open OpenCamera Sensors, go to preferences - "RecSync settings..." and enable the **"Use RecSync"** switch
- (Optional) Enable **phase alignment** option if synchronization precision better than half of a frame duration is required
- (Optional) Choose which camera settings will be broadcasted to client smartphones in the **"Sync settings"** section
- Switch to video, adjust the camera settings as needed and press the **settings synchronization button**
- Wait for client smartphones to connect if needed
- (Optional) If phase alignment was enabled, press the **phase alignment button** to start the alignment and wait for it to finish ("Phase error" on the preview indicates how much the current phase differs from the targeted one -- when it becomes green, the phase is considered aligned)
- **Start a video recording**

![screenshot_recsync_buttons](https://i.imgur.com/iQS8zpc.png)

**Client smartphones setup:**
- **Connect** to the leader's Wi-Fi hotspot
- Open OpenCamera Sensors, go to preferences - "RecSync settings..." and enable the **"Use RecSync"** switch
- Adjust the camera settings as needed (the ones that will not be broadcast by the leader) and wait for the leader to start the recording

_Note: the phase needs to be re-aligned before every recording._

## Good practices for data recording

- When recording video with audio recording enabled, MediaRecorder adds extra frames to the video to match the sound.
Due to this problem, the **audio recording** feature **is disabled** in our app by default.

- To minimize the amount of preprocessing done by the smartphone, we also disable **video stabilization** and **OIS** options.

## Restrictions

One important restriction is that our app requires full Camera2API support.

Another restriction of our application is that synchronized timestamping for camera and IMU data isn’t available on all the devices with Camera2API support.
You can check whether your device supports this feature in preferences.

## Contribution

The project follows [AOSP Java Code Style](https://source.android.com/setup/contribute/code-style), main principles:

- Non-public fields should start with ```m```, constants are ```ALL_CAPS_UNDERSCORES``` 
- Standard brace style:
```java
if () {
    //...
} else {
    //...
}
```
- Limit line length
