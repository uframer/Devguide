# 光流和激光雷达
----------------------------------------------------

本文介绍了如何设置PX4Flow和LIDAR并用它们做位置估计。Using a LIDAR device is not necessary if you use the LPE estimator described below since the PX4FLOW has a sonar, but LIDAR does improve performance.

## 选择一个估计器
--------------------------------------------------------

Two estimators support optical flow based navigation, LPE and INAV. There are benefits to both, but LPE is currently recommended for new users as it has the most testing and is the most robust. INAV uses slightly less CPU.

Use the `SYS_MC_EST_GROUP` parameter to select the estimator and then reboot.


## 硬件
--------------------------------------------------------

![](images/hardware/px4flow_offset.png)

*图 1: Mounting Coordinate Frame (relevant to parameters below)*

![](images/hardware/px4flow.png)

*图 2: PX4Flow optical flow sensor (camera and sonar)*

The PX4Flow has to point towards the ground and can be connected using the I2C port on the pixhawk. For best performance make sure the PX4Flow is attached at a good position and is not exposed to vibration. (preferably on the down side of the quad-rotor).

**注意：The default orientation is that the PX4Flow sonar side (+Y on flow) be pointed toward +X on the vehicle (forward). If it is not, you will need to set SENS_FLOW_ROT accordingly.**

![](images/hardware/lidarlite.png)

*图 3: Lidar Lite*

Several LIDAR options exist including the Lidar-Lite (not currently manufacutured) and the sf10a: [sf10a](http://www.lightware.co.za/shop/en/drone-altimeters/33-sf10a.html). For the connection of the LIDAR-Lite please refer to [this](https://pixhawk.org/peripherals/rangefinder?s[]=lidar) page. The sf10a can be connected using a serial cable.

![](images/hardware/flow_lidar_attached.jpg)

*图: PX4Flow/ Lidar-Lite mounting DJI F450*

![](images/flow/flow_mounting_iris.png)

*图: This Iris+ has a PX4Flow attached without a LIDAR, this works with the LPE estimator.*

![](images/flow/flow_mounting_iris_2.png)

*图: A weather-proof case was constructed for this flow unit. Foam is also used to surround the sonar to reduce prop noise read by the sonar and help protect the camera lens from crashes.*


### 摄像头对焦

In order to ensure good optical flow quality, it is important to focus the camera on the PX4Flow to the desired height of flight. To focus the camera, put an object with text on (e. g. a book) and plug in the PX4Flow into usb and run QGroundControl. Under the settings menu, select the PX4Flow and you should see a camera image. Focus the lens by unscrewing the set screw and loosening and tightening the lens to find where it is in focus.

**Note: If you fly above 3m, the camera will be focused at infinity and won't need to be changed for higher flight.**

![](images/flow/flow_focus_book.png)

*图: Use a text book to focus the flow camera at the height you want to fly, typically 1-3 meters. Above 3 meters the camera should be focused at infinity and work for all higher altitudes.*


![](images/flow/flow_focusing.png)

*图: The px4flow interface in QGroundControl that can be used for focusing the camera*

### 传感器参数

All the parameters can be changed in QGroundControl
* SENS_EN_LL40LS
	Set to 1 to enable lidar-lite distance measurements
* SENS_EN_SF0X
	Set to 1 to enable lightware distance measurements (e.g. sf02 and sf10a)

## 局部位置估计器（LPE）
--------------------------------------------------------

LPE is an Extended Kalman Filter based estimator for position and velocity states. It uses inertial navigation and is similar to the INAV estimator below but it dynamically calculates the Kalman gain based on the state covariance. It also is capable of detecting faults, which is beneficial for sensors like sonar which can return invalid reads over soft surfaces.

### 室内飞行视频
{% youtube %}https://www.youtube.com/watch?v=CccoyyX-xtE{% endyoutube %}

### 室外飞行视频
{% youtube %}https://www.youtube.com/watch?v=Ttfq0-2K434{% endyoutube %}

For outdoor autonmous missions with LPE estimator, see tutorial on (Optical Flow Outdoors)[./optical-flow-outdoors.md]

### 参数

The local position estimator will automatically fuse LIDAR and optical flow data when the sensors are plugged in.

* LPE_FLOW_OFF_Z - This is the offset of the optical flow camera from the center of mass of the vehicle. This measures positive down and defaults to zero. This can be left zero for most typical configurations where the z offset is negligible.
* LPE_FLW_XY - Flow standard deviation in meters.
* LPW_FLW_QMIN - Minimum flow quality to accept measurement.
* LPE_SNR_Z -Sonar standard deviation in meters.
* LPE_SNR_OFF_Z - Offset of sonar sensor from center of mass.
* LPE_LDR_Z - Lidar standard deviation in meters.
* LPE_LDR_Z_OFF -Offset of lidar from center of mass.
* LPE_GPS_ON - You won't be able to fly without GPS if LPE_GPS_ON is set to 1. You must disable it or it will wait for GPS altitude to initialize position. This is so that GPS altitude will take precedence over baro altitude if GPS is available.

**NOTE: LPE_GPS_ON must be set to 0 to enable flight without GPS **

## 惯性导航估计器（INAV）
--------------------------------------------------------

INAV has a fixed gain matrix for correction and can be viewed as a steady state Kalman filter. It has the lowest computational cost of all position estimators.


### 室内飞行视频
{% youtube %}https://www.youtube.com/watch?v=MtmWYCEEmS8{% endyoutube %}

### 室外飞行视频
{% youtube %}https://www.youtube.com/watch?v=4MEEeTQiWrQ{% endyoutube %}


### 参数
* INAV_LIDAR_EST
	Set to 1 to enable altitude estimation based on distance measurements
* INAV_FLOW_DIST_X and INAV_FLOW_DIST_Y
	These two values (in meters) are used for yaw compensation.
	The offset has to be measured according to Figure 1 above.
	In the above example the offset of the PX4Flow (red dot) would have a negative X offset and a negative Y offset.
* INAV_LIDAR_OFF
	Set a calibration offset for the lidar-lite in meters. The value will be added to the measured distance.


### 高级参数

For advanced usage/development the following parameters can be changed as well. Do NOT change them if you do not know what you are doing!

* INAV_FLOW_W
	Sets the weight for the flow estimation/update
* INAV_LIDAR_ERR
	Sets the threshold for altitude estimation/update in meters. If the correction term is bigger than this value, it will not be used for the update.
