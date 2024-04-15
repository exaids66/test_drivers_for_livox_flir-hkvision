# flir_camera_driver

这个驱动主要实现的是图像时间戳的获取逻辑。test1的驱动中，图像时间戳获取的是图像收到pps信号后开始曝光的时间。

但我们**实际上需要的是图像曝光结束后上传图像的时间**——这个就是原始驱动的逻辑。这样我们才能**把时间戳的标准统一：同一个时间坐标系下&在上传图像/点云的瞬间打上时间戳**。

由于livox半固态激光雷达的特性，**点云每次上传的帧是前一段时间内点云的累积，因此接收到pps信号的瞬间就能publish点云信息**；但**图像需要曝光时间，因此图像的时间戳总是比点云的时间戳晚一些**。



在原始的驱动中，我们无法得到图像在自动曝光模式下的曝光时间，这样因为每张图片的曝光时间不可控，会在数据分析时引入无法消除的误差——因此，在test2驱动中，我们**固定了相机的曝光时间**，以此减小同步误差，以便于对采集到的同步数据进行进一步的处理和使用。





This repository contains packages for FlirImaging's line of cameras. This repositories intent is to make use of Flir's newly developed SDK: Spinnaker. The camera driver is an evolution of pointgrey_camera_driver. It has been updated to use the new methods provided by the SDK.

## Packages

### Spinnaker Camera Driver
The camera driver supports USB3 and GIGE cameras are planned. Note thats support for FireWire cameras is dropped in this SDK. The driver has been tested with a Blackfly S and Chameleon 3 camera. Differences between cameras requires that each camera model needs a customized interface class.  If your camera type is not included, consider contributing the interface by referring to the section bellow.

##### Contributing
Due to differences in parameter naming the configuration is separated from the main library. `camera.cpp` contains the base class `Camera` which can be extended to accommodate different cameras. The base class is based on BlackFly S and `cm3.cpp` extends it adding support for Chameleon3. To add a camera create a new derived class of `Camera` and add the model name to the check in `SpinnakerCamera::connect`.

When contributing make sure the travis job suceeds and please use [roscpp_code_format](https://github.com/davetcoleman/roscpp_code_format) to format your code.

## Licence
ROS-compatible Camera drivers originally provided by NREC, part of Carnegie Mellon University's robotics institute.
These drives are included along with modifications of the standard ros image messages that enable HDR and physics based vision.

This code was originally developed by the National Robotics Engineering Center (NREC), part of the Robotics Institute at Carnegie Mellon University. Its development was funded by DARPA under the LS3 program and submitted for public release on June 7th, 2012. Release was granted on August, 21st 2012 with Distribution Statement "A" (Approved for Public Release, Distribution Unlimited).

This software is released under a BSD license:

Copyright (c) 2012, Carnegie Mellon University. All rights reserved.  
Copyright (c) 2018, Clearpath Robotics, Inc., All rights reserved.

Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.
Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.
Neither the name of the Carnegie Mellon University nor the names of its contributors may be used to endorse or promote products derived from this software without specific prior written permission.
THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
