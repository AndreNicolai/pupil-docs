+++
date = "2018-02-09T14:08:14+07:00"
title = "unity"
section_weight = 4
page_weight = 1.3
+++

## Unity - Getting Started Guide

### Installation 

This Unity 3D plugin is intended to be used with the Pupil Capture/Service applications, the related eye tracking hardware and Virtual/Augmented Reality headsets (Head Mounted Displays). You can get the latest versions here 

- [Pupil GitHub Repo](https://github.com/pupil-labs/pupil/releases/latest)

The plugin is developed and tested with the latest versions of Unity 3D (2017 releases), publicly available at 

- [Unity download](https://store.unity.com/)

Much like the Pupil software, this plugin is developed in the open and made available on GitHub 

- [HMD-Eyes on GitHub](https://github.com/pupil-labs/hmd-eyes)

Once downloaded, three different projects are included in the main directory 

- python_reference_client 
- unity_pupil_plugin_hololens 
- unity_pupil_plugin_vr 

This document will describe the use of the latter two in more detail. Both share a common code basis but also contain implementations for their respective platforms. Here, VR is the main focus with the most features and demos, while the HoloLens implementation had to be stripped down due to the UWP environment. 

To open either project, start Unity and select `unity_pupil_plugin_hololens` or `unity_pupil_plugin_vr` as source folder. 

**The VR Build and Player Settings should look like this**


![VR Build And Player Settings](https://github.com/AndreNicolai/pupil-docs/blob/master/images/unity/VRBuildAndPlayerSettings.png)
The software has been tested for both Oculus and OpenVR SDKs. Please make sure to select the correct one for your type of headset. 

**As for HoloLens**


![HoloLens Build And Player Settings](https://github.com/AndreNicolai/pupil-docs/blob/master/images/unity/HoloLensBuildAndPlayerSettings.png)
The `Build Settings` debugging option `Unity C# Projects` will allow you to debug the code at runtime while deploying to HoloLens from Visual Studio. As an alternative debugging option, Unity`s `Holographic Emulation` is also supported. 

As for the Player Settings, make sure the following `Capabilities` are enabled under `Publishing Settings` 

- InternetClient, PrivateNetworkClientServer, Microphone, SpatialPerception 

### General Setup 

The Unity scene `pupil_plugin/Calibration.unity`, which is included in both projects, serves as a good starting point for VR and HoloLens development. 

**In case of VR, two things are important for the initial steps**

- The `PupilGazeTracker` gameobject offers an Inspector GUI to set how to communicate with Pupil Capture/Service. Either can run on the same `Local` computer or on a `Remote` PC.

![Pupil Gaze Tracker](https://github.com/AndreNicolai/pupil-docs/blob/master/images/unity/PupilGazeTracker.png)


- `PupilSettings` is located in `pupil_plugin/Resources` and is used to save global settings that are not specific to a scene. Important for the initial steps is the `Connection`, which lets you set both the IP (in case of Pupil running remotely) as well as the port over which to communicate. If the standard port of 50020 does not work for you, please set an alternative here and also make sure that the same port is set in Pupil Capture (more on that, later). 

![PupilSettings](https://github.com/AndreNicolai/pupil-docs/blob/master/images/unity/PupilSettings.png)


**Differences for HoloLens**

- As Pupil Capture/Service does not support native execution on HoloLens/UWP but has to run on a remote PC, `PupilGazeTracker` does not need to offer any options to change this. In contrast to its VR pendant, the gameobject includes an additional component called `UDP Communication`. Here, an additional port (named `Editor Mode UDP Port`) can be set. It is needed, if you use `Holographic Emulation` and (at least on Windows machines) needs to be different from the main port, discussed next. 

![Pupil Gaze Tracker For HoloLens](https://github.com/AndreNicolai/pupil-docs/blob/master/images/unity/PupilGazeTracker_HoloLens.png)


- As mentioned before, the HoloLens implementation needed to be customized to be able to run it in the UWP environment. Our solution relies on UDP to communicate data between the Unity plugin and Pupil. Select `PupilSettings` in the `Project` tab and set the IP of the PC Pupil is running on under `Connection`. Please also make sure the port set here corresponds with the one you set in the `HoloLens Relay` plugin in Pupil Capture. 

![PupilSettings for HoloLens](https://github.com/AndreNicolai/pupil-docs/blob/master/images/unity/PupilSettings_HoloLens.png)


### Pupil Capture/Service setup 

As of late February 2018 and with release version 0.5, either Pupil Capture or Pupil Service needs to be started by the user. (Before, it was possible to let Unity do this automatically by setting the path to the corresponding executable.) My personal recommendation is to start development with Pupil Capture, as it gives the most options for feedback if things are not set correctly or if something goes wrong. Once everything is working as intended, Pupil Service is a good solution to minimize complexity on screen. In our case, one or two eye windows depending on your setup plus the new Service GUI


![Eye Window](https://github.com/AndreNicolai/pupil-docs/blob/master/images/unity/EyeWindow.png)

During the tracking process, one instance of these windows should always be open per eye camera. 

In its main GUI, Pupil Capture gives real time feedback on how the confidence level is doing 


![Pupil Capture Main GUI](https://github.com/AndreNicolai/pupil-docs/blob/master/images/unity/PupilCaptureMainGUI.png)
One of the most common problems is the standard communication port `50020` being blocked. In that case, Pupil Capture will choose an alternate one. To be able to check this, activate `Pupil Remote` in Plugin Manager 


![Pupil Capture Plugins](https://github.com/AndreNicolai/pupil-docs/blob/master/images/unity/PupilCapturePlugins.png)
Also included in the image above are screenshots of the `HoloLens Relay` and the `Blink Detector` plugin. The former allowing you to adapt the port for HoloLens communication, the latter to change blink detection variables. 

### Running a Calibration 

Once a connection has been established, the next step usually is to start a calibration. As this is a critical point, we added two feedback features that should be considered before doing so


![Before Starting A Calibration](https://github.com/AndreNicolai/pupil-docs/blob/master/images/unity/BeforeStartingCalibration.png)

- A circle indicating the extend of where the calibration marker will be placed
- An image for each eye camera to help you make sure the headset is set up correctly. To disable this feature, select the `Pupil Demo Manager` and toggle the `Display Eye Images` checkmark

The positioning of the calibration marker is very important, as it is used as reference for the Pupil eye tracking algorithms. If you are having trouble with the accuracy, try adapting the calibration settings:

Go to `pupil_plugin/PupilSettings` and select `Calibration` 
 
![PupilSettings for Calibration](https://github.com/AndreNicolai/pupil-docs/blob/master/images/unity/PupilSettingsCalibration.png)

- `Points` defines the number of points per circle, the first being in the center of it. 

- `Marker Scale` determines the size of the calibration marker on screen 

- `Vector Depth Radius` can be adapted to define the dimension of the circle 

	- 2D calibration: We place the markers based on viewport coordinates, starting at the center position (0.5,0.5). Depth translates to the distance from the camera at which marker is placed. Radius defines the distance from the center point (in viewport space).

    - 3D calibration: We place the markers based on XYZ coordinates in local camera space. As we need to calibrate for depth, as well, we have multiple distances at which the circle pattern is applied. In the standard case at a distance of `0.6`, `1` and `2`. Radius defines the XY distance from the current center point (0,0,Depth).

- `Samples Per Depth` defines the amount of calibration points recorder for each marker position. We reduced this number for 3D calibration to a third of the samples of 2D calibration so it takes the same duration of time. 

- `Samples To Ignore For Eye Movements` defines how much time the user is expected to need to move from one calibration marker position to the next. During this period, the gaze positions will not be recorded. 

During calibration, the marker color will indicate whether there is a problem with the Pupil confidence level. While white corresponds to perfect levels, red indicates the opposite with confidence for both eyes being close to zero. If only eye ID 0 values are bad, the marker color shifts towards pink and if eye ID 1 values are close to zero, the color shifts to yellow. Every color in between points to something not being optimal, but the closer to white the marker is, the better the confidence levels are. 

Once the calibration is finished and all reference points are sent to Pupil, the software will respond with either a `success` or `failure` message.  

- In case of success, the PupilDemoManager with activate the gameobjects for the actual scene and display the respective gaze visualizations 

- In case of a failure, please use Pupil Capture to see if the confidence levels are good for both eyes when the user is wearing the headset. Unity accepts gaze data with a confidence level greater than 0.6, but values above 0.9 should be targeted. Also have a look at the console, as it might give you a hint at what is going wrong. 

### Pupil Demo Manager 

Other than the `Blink` and `Frame Publishing` Unity scene, most demos contain the PupilDemoManager gameobject. Its corresponding script is listening for Pupil events such as the connection being established/quit and the calibration being started, stopped or failing. The Demo Manager is also available as Unity prefab (to be found in `pupil_plugin/Prefabs`), so that it can easily be added to existing scenes. It brings its own camera and a simple GUI to guide the user through the connection and calibration process. A successful calibration results in activating the actual scene objects, so for example the 3D market scene. If you use the prefab for your own scene, please specify the gameobject to activate by adding them to the `Game Objects To Enable` list, exposed in the Unity Inspector. 

### Accessing Data 

After a successful calibration, you need to call `PupilTools.Subscribe(string topic)` to receive messages for the `topic` you specify. Most of the demos included in this project are based on subscribing to `gaze` and its implementation shall serve as an example on how to do it for other topics. Message interpretation is handled inside the code block starting in line 127 of `Connection.cs`. Pupil messages for the `gaze` topic contain dictionaries, which are deserialized using the `MessagePackSerializer` classes (line 167) and stored to `PupilTools.gazeDictionary`. `PupilTools.UpdateGaze()` goes through the data, storing relevant information (e.g. the gaze positions) through `PupilData.AddGazeToEyeData(string key, float[] position)`. Based on the chosen calibration type, this can either be 2D or 3D. To access the data, use 

- `PupilData._2D.LeftEyePosition`, `PupilData._2D.RightEyePosition` or `PupilData._2D.GazePosition`, which will provide the current viewport coordinates in camera space for the respective eye (used e.g. for the three colored markers) 

- `PupilData._2D.GetEyePosition (Camera sceneCamera, int eyeID)`, which will apply an additional frustum center offset for each eye (used e.g. for the shader implementations) 

- `PupilData._3D.GazePosition`, which contains the camera relative gaze position 

Working with 2D calibration and viewport coordinates, Unity provides methods to translate these to 3D space, which is e.g. used for marker positioning 

- `PupilMarker.UpdatePosition(Vector2 newPosition)`, line 56 

	gameObject.transform.position = camera.ViewportToWorldPoint(position); 

An alternative is used by the laster pointer implementation in the `2D Calibration Demo` scene  

- `MarketWith2DCalibration.Update()`, line 63 

	Ray ray = sceneCamera.ViewportPointToRay(viewportPoint); 

This solutions requires the use of Unity Colliders, though, which, when hit by the above defined ray, return the 3D hit point position. 

### Recording Data

The Unity VR plugin allows to trigger recordings in Pupil Capture (Pupil Service does not support this feature). Recordings can be started through the interface GUI of `PupilGazeTracker` or by pressing the 'R' key (in either case only once a connection has been established). On the plugin side of things, two additional processes are started
- a screen recording, saving the current view to a video file 

- saving Pupil gaze data and their corresponding Unity positions in CSV format using this structure
	- `Timestamp,Identifier,PupilPositionX,PupilPositionY,PupilPositionZ,UnityWorldPositionX,UnityWorldPositionY,UnityWorldPositionZ`

The resulting files are saved to the path set in `PupilSettings/Recorder/File Path`. You can change the it manually, there, or through the `PupilGazeTracker` Inspector GUI under `Settings` by activating `CustomPath` and clicking the `Browse` button.
### Demo Scenes 

Here a short description of the included examples and what should be visible on screen, once the calibration (if needed) is finished. 

**Calibration** 

This scene will display three colored markers representing the left (green) and right (blue) eye plus the center point (red). If everything calibrated as intended, these markers should be very close to each other. 


![Calibration Scene Three Colored Markers](https://github.com/AndreNicolai/pupil-docs/blob/master/images/unity/CalibrationSceneThreeColoredMarkers.png)

**Blink** 

A barebones implementation for users who do not need gaze data or want a simple example on how to subscribe to a topic and read data from the socket. As suggested by its name, this demo utilizes the Blink_Detection Pupil plugin. It does not require to run through the calibration process. While dictionary setup is usually kept within PupilTools, BlinkDemoManager contains all blink-specific variants to give a better overview of what is involved. This includes starting/stopping the plugin, un-/subscribing to `blinks` and receiving the dictionary packages from Pupil. 

**Frame Publishing**

The `Frame Publishing` implementation (which replaced the old `Operator Monitor`) in another example of a single script encompassing all elements of communication with Pupil - this time for subscribing to eye images. Much like the Blink demo, it also does not require to go through the calibration process.


![Frame Publishing Demo](https://github.com/AndreNicolai/pupil-docs/blob/master/images/unity/FramePublishing.png)

**2D/3D Calibration Demo (VR)**

These scenes will display a 3D market scene, based on Unity assets available for free on the Asset Store. While the 3D calibration scene only includes a simple white marker visualization, the 2D calibration pendant includes the three colored variant used in the Calibration demo scene and two additional visualizations 

- A laser pointer going to the center of your gaze (depicted on the left) 

- A shader-based implementation that grays out the area around each of the eyes position (right) 

	![2D Demo Visualization](https://github.com/AndreNicolai/pupil-docs/blob/master/images/unity/2DDemoVisualizations.png) 

**2D/3D Calibration Demo (HoloLens)**

As it is not a common use-case for HoloLens to visualize complete scenes, we reduced the market scene to a single object (the `sharkman`) for the user to look at. The laser pointer visualization has also been removed. 

**Spherical Video demo**

As many users asked for this feature, we now include a demo that can load and display a 360 degree video. Combined with Pupil, this allows to visualize what the user is looking at. There are two ways to load a spherical video, select the `VideoSphere` gameobject to toggle between them 

- Using Unity`s internal player, for which the video has to be part of the project files. The included example is based on the market demo scene 

- Using `ffmpeg` to load external videos and access videoframes at runtime. Depending on the machine it is running on and if it is running in Unity Editor or standalone, this can be slow 

**Heatmap demo**

The heatmap demo allows to export gaze visualization to a spherical video or image. Eye tracking positions are translated to particles on a spherical texture, which is overlayed on the 3d scene. The heatmap is available as Prefab, as well, and can be added to existing scenes by dragging it onto the main camera of that scene. After calibration, press `h` to start recording the output video or to capture the current view to an image. The output path is the same as the one defined by the settings for PupilGazeTracker recordings. A few variables can change how the heatmap behaves 


![Heatmap Component](https://github.com/AndreNicolai/pupil-docs/blob/master/images/unity/HeatmapComponent.png)
- Mode 
	-`Particle` will color the area the user is looking at 

	- `ParticleDebug` will show it for the user, as well 

	- `Highlight` will only fill-in the area looked at 
	
	![Highlight Mode Heatmap](https://github.com/AndreNicolai/pupil-docs/blob/master/images/unity/HighlightModeHeatmap.jpg)

	- `Image` will keep all particles and color code them based on the time of creation 
	
	![Image Mode Heatmap](https://github.com/AndreNicolai/pupil-docs/blob/master/images/unity/ImageModeHeatmap.jpg)

	- `Particle Size` - The size of a single gaze visualizing particle 

	- `Remove Particle After X Seconds` - Set how many seconds a particle should be visualized (not used for Image mode) 

	- `Particle Color` - The color a particle should have. 
	- `Particle Color Final` - Color for oldest particle in Image mode. Every color in between will be interpolated 