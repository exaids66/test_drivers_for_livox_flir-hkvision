# test_drivers_for_livox_flir-hkvision
a private repo for test drivers

****

### flir_ws

这个驱动主要实现的是图像时间戳的获取逻辑。我们**实际上需要的是图像曝光结束后上传图像的时间**，而不是相机收到pps信号开始曝光的时间——这个就是原始驱动的逻辑。这样我们才能**把时间戳的标准统一：同一个时间坐标系下&在上传图像/点云的瞬间打上时间戳**。

由于livox半固态激光雷达的特性，**点云每次上传的帧是前一段时间内点云的累积，因此接收到pps信号的瞬间就能上传点云信息**；但**图像需要曝光时间，因此图像的时间戳总是比点云的时间戳晚一些**。



在原始的驱动中，我们无法得到图像在自动曝光模式下的曝光时间，这样因为每张图片的曝光时间不可控，会在数据分析时引入无法消除的误差——因此，我们在使用这些驱动时需要**固定相机的曝光时间**，以此减小同步误差，以便于对采集到的同步数据进行进一步的处理和使用。（固定的方法：修改驱动的config文件即可，不需要对驱动源码进行修改）

- flir_ws

  原版的flir相机驱动的工作空间，其驱动由开源社区维护

  参考链接：

  - https://www.flir.com.au/support-center/iis/machine-vision/application-note/using-ros-with-spinnaker/
  - https://github.com/ros-drivers/flir_camera_driver

- flir_ws_test1

  在原本的基础上更改了图像时间戳的获取逻辑，改为从共享空间shm_timer获取。但改动还不完善，循环逻辑没写完。后面需要把原本的try...逻辑改为循环逻辑

  updated：改动完毕，具体位置：**Spinnakeramera.cpp的339-394行**

  该驱动需要配合修改过后的livox驱动（本仓库中是livox_ws_test1）使用，共享空间中的时间由livox驱动写入。

  参考链接：

  - https://www.jianshu.com/p/d4b5cf3c1475
  - https://github.com/vell001/livox_ros_driver
  - https://github.com/vell001/mvs_ros_pkg

- flir_ws_test2

  待完善

  目标：能够配合r2live的livox驱动实现同步，可能需要参考r2live的驱动进行修改适配（设想是仅需要将时间戳对齐到系统坐标系即可）

  参考链接：

  - https://github.com/ziv-lin/livox_ros_driver_for_R2LIVE.git

### livox_ws

- livox_ws

  原版的livox驱动程序的工作空间，没有做任何修改（不适用于mid360）

  参考链接：

  - https://github.com/Livox-SDK/livox_ros_driver

- livox_ws_test1

  在原本的livox驱动的基础上进行了改动，将pps触发后的系统utc时间作为该驱动的基准时间，并写入共享空间中供相机驱动调用，以此实现相机和激光雷达时间坐标系的对齐。（原版的livox驱动的时间戳是雷达启动后从0开始的）

  目前测试发现该驱动存在问题，待完善：

  - 目前的问题在于在实机测试中发现该驱动破坏了原本lidar和imu的同步，avia在使用该驱动录制的rosbag在运行fast-lio中会出错

  参考链接：

  - https://www.jianshu.com/p/d4b5cf3c1475
  - https://github.com/vell001/livox_ros_driver

- livox_ws_test3

  即为rxlive修改的港大版本，主要修改了IMU和Lidar话题的时间戳赋值，从原本的从lidar启动开始从0赋值修改为系统时间

  主要修改的部分：lddc.cpp文件41-46行，562-564行，622-645行

  参考链接：

  - https://github.com/ziv-lin/livox_ros_driver_for_R2LIVE

### ublox_ws

- ublox_ws

  HKUST-Aeria-Robotics实验室撰写的用于在ubuntu系统中接收和解析ublox设备上传的信息的驱动。

  我们主要用到其将本地系统时间同步到gprmc消息中提供的utc时间的功能

  待修改（目前只能手动同步，且精度有限）

  参考链接：

  - https://github.com/HKUST-Aerial-Robotics/ublox_driver

