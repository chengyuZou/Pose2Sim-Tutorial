# Pose2Sim-Tutorial
A guide to generating motion skeletons in OpenSim from real-world video footage.
Devices: Two GoPro Hero12 cameras, set to Linear mode.

Calibration is very important. Calibration Inaccuracy can lead to many errors
---------------------------------------------------------------------------------------------------------------------------------------------------
Create Calibration folder like this.
Calibration
  -intrinsics
     -cam01
     -cam02
  -extrinsics
     -cam01
     -can02

I have two cameras, so I created folders named cam01 and cam02. If you have more cameras, create additional folders like cam03, and so on.
![Test - 文件资源管理器 2025_5_17 17_32_32](https://github.com/user-attachments/assets/5b14c9de-e2e0-4ac8-a97a-2769aaaf10f8)
![Test - 文件资源管理器 2025_5_17 18_05_34](https://github.com/user-attachments/assets/f9adc337-1efb-474c-be5f-d4e44c9f66a1)
![intrinsics - 文件资源管理器 2025_5_17 18_05_52](https://github.com/user-attachments/assets/f0ead25d-3af4-46aa-97fd-93598e60b83e)

Record a video of the checkerboard using each camera individually, then extract still frames from the video to use as intrinsics images(more than 6 frames).

Video:
https://github.com/user-attachments/assets/7af3a238-14e3-4549-8331-4e9c9051003d

Extract Frames:
![cam01 - 文件资源管理器 2025_5_17 18_15_22](https://github.com/user-attachments/assets/35707035-cf5b-4407-9dd4-8b78090f39f2)

Then translate this section(calibration.calculate) in the Config file
![rotate_camera py – Config toml 2025_5_17 18_19_09](https://github.com/user-attachments/assets/65f1d4a9-a0c5-4ba7-be16-83cac69a9997)

Translate 
1. "intrinsics_square_size" to your size of every square
2. "intrinsics_corners_nb"
3. "overwrite_intrinsics"  Intrinsic parameters usually need to be calculated only once in their lifetime.

---------------------------------------------------------------------------------------------------------------------------------------------------
Record the videos and put them in the Videos folder in order(cam01 - cam02 -cam03 - cam04 and so on)
![videos - 文件资源管理器 2025_5_17 18_28_38](https://github.com/user-attachments/assets/35714554-901b-4f56-9058-bb03b6a95425)
https://github.com/user-attachments/assets/696546e6-277e-4ee1-a22b-9e2a615b68b0

Also take a frame from your video as an external reference for calibration
![01](https://github.com/user-attachments/assets/86ef1686-fcd2-42d6-9e10-0542de39ccf0)
![cam02 - 文件资源管理器 2025_5_17 18_34_10](https://github.com/user-attachments/assets/ce1dd739-d033-4e9a-94b6-d46bf34b0fd8)

Then translate this section(calibration.calculate.extrinsics.scene]) in the Config file

"object_coords_3d"must be translated. Manually measure the 3D coordinates of 10 or more points in the scene
We use the four corners of the floor tile as a reference, and each floor tile is 80cm in length and width.
So my object_coords_3d is
[[0.0 , 0.0 , 0.0],
[0.8 , 0.0 , 0.0],
[1.6 , 0.0 , 0.0],
[0.0 , 0.8 , 0.0],
[0.8 , 0.8 , 0.0],
[1.6 , 0.8 , 0.0]]


Note"extrinsics_extension" change your entension(jpg or png)

---------------------------------------------------------------------------------------------------------------------------------------------------
run Pose2Sim.calibration()
I have calibrated the internal reference before and set "overwrite_intrinsics" to false, so I will start with the external reference.
![C__Windows_system32_cmd exe - _E__Anaconda3_condabin_conda bat_  activate Pose2Sim 2025_5_17 18_44_17](https://github.com/user-attachments/assets/61cd17ff-1c2e-416d-aa3b-f5b57d5faa07)

---------------------------------------------------------------------------------------------------------------------------------------------------
run Pose2Sim.poseEstimation()
![C__Windows_system32_cmd exe - _E__Anaconda3_condabin_conda bat_  activate Pose2Sim 2025_5_17 18_47_27](https://github.com/user-attachments/assets/1bc15b07-ec1e-4b96-a9c4-d50bac33e2d5)

If you get an error at this step, set "pose_model" (in Config) to 'HALPE_26'

---------------------------------------------------------------------------------------------------------------------------------------------------
run Pose2Sim.synchronization()
Theoretically, the Offset is 0. If offset is too large, it will cause errors.

![C__Windows_system32_cmd exe - _E__Anaconda3_condabin_conda bat_  activate Pose2Sim 2025_5_17 18_55_03](https://github.com/user-attachments/assets/dbbfaf8c-e8fe-4117-819c-0d9c7e6a046c)

---------------------------------------------------------------------------------------------------------------------------------------------------
run Pose2Sim.personAssociation() if There are more than two people in the video 
else pass this

---------------------------------------------------------------------------------------------------------------------------------------------------
run Pose2Sim.triangulation()
![C__Windows_system32_cmd exe - _E__Anaconda3_condabin_conda bat_  activate Pose2Sim 2025_5_17 19_00_49](https://github.com/user-attachments/assets/406ed215-587c-4b12-982f-bd0baace2bc2)
If you encounter like "no person" and other problems,I suggest recalibrating the external parameters.

---------------------------------------------------------------------------------------------------------------------------------------------------
run Pose2Sim.filtering()
Generally this step will not go wrong

---------------------------------------------------------------------------------------------------------------------------------------------------
I passed Pose2Sim.markerAugmentation() and run Pose2Sim.kinematics()
![C__Windows_system32_cmd exe - _E__Anaconda3_condabin_conda bat_  activate Pose2Sim 2025_5_17 19_03_58](https://github.com/user-attachments/assets/7d4db3ce-27b5-47df-825f-4709ff4e0f2e)

If total squared error is bigger than 1.0 the kinematics results are so bad
recalibrating the external parameters usually can solve this

---------------------------------------------------------------------------------------------------------------------------------------------------
our OpenSim motion
https://github.com/user-attachments/assets/e1ba1b4e-4503-4043-8dc7-33b7f7f5f0a4





