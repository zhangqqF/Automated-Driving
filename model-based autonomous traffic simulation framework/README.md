# model-based autonomous traffic simulation framework

>zhangqq  
>Nov2, 2021  
>Nanjing
---

[![MustafaSaraoglu - AutonomousVehicleModeling](https://img.shields.io/static/v1?label=MustafaSaraoglu&message=AutonomousVehicleModeling&color=orange&logo=github)](https://github.com/MustafaSaraoglu/AutonomousVehicleModeling)
[![YouTube](https://img.shields.io/youtube/channel/views/UCVP9SDdAH_TcXCfGsGFQ09Q?style=social)](https://www.youtube.com/c/MOBATSim)
[![](https://img.shields.io/badge/MOBATSim-lirhtgreen.svg)](https://mobatsim.com/)

## Part1: Developing a Vehicle Model in Simulink from Scratch

Access the example of [adaptive-cruise-control-using-model-predictive-controller](https://www.mathworks.com/help/mpc/ug/adaptive-cruise-control-using-model-predictive-controller.html) to find the dynamics model between acceleration and velocity:

![image](https://user-images.githubusercontent.com/48160597/165027467-385d93de-99ed-4ea5-92e6-57f1230f1803.png)
 
### Transfer Fcn

 *denominator Coefficient*: [0.5 1 0]
 
 ![image](https://user-images.githubusercontent.com/48160597/165029299-5ac3a2cf-85e2-4a24-a891-1afae9b7e3ed.png)
 
 ![image](https://user-images.githubusercontent.com/48160597/165029483-6bea0f9d-32e9-4b4d-8ea9-4bba6fc0bcc4.png)

### Vehicle Model

![image](https://user-images.githubusercontent.com/48160597/165038961-44529c32-f59e-4916-9cff-992cde3986b4.png)

Transfer Func降一个次幂，其后增加一个积分器来弥补，积分器的Initial Condiction Source设置为Extra，即可增加Initial Speed的输入口。

Transfer Func前面的saturation限制变速大小[-3, 2]，Speed后面的saturation则是使速度不能为负[0, inf]。

目标速度减去当前速度......不太清楚这之间的关系。

![image](https://user-images.githubusercontent.com/48160597/165040266-833d654a-98e1-4fd7-a58b-992539b3bab7.png)

Mask Edit（ctrl+m）是当前路径的工作路径，不是当前文件存放路径。


[Part1_Developing_a_Vehicle_Model_in_Simulink_from_Scratch.slx](Part1_Developing_a_Vehicle_Model_in_Simulink_from_Scratch.slx)

Initial Position: 100  
Initial Speed: 20

## Part2: Using Adaptive Cruise Control Block in Simulink

- 复制Vehicle Model 1为Vehicle Model 2，在Vehicle Model 2中增加Adaptive Cruise Control System

![image](https://user-images.githubusercontent.com/48160597/165051796-2a6f711f-ddbe-4f2f-8fc5-2daf2b9fedc1.png)

对比Part 2中的模型。

修改ACC中的参数：
- Initial condition for longitudinal velocity (m/s)：30
- Prediction horizon (steps)：300
- Controller Behavior：0.8

![image](https://user-images.githubusercontent.com/48160597/165052498-7908f468-0beb-4de2-baef-0b92b581071e.png)

将Vehicle Model 2的Initial Position和Initial Speed分别改为0和30，Vehicle 1仍保持100，20，但Reference Speed改为20 from 30.

Sum使减号在上面：-+|

运行100s，可看到缓慢进程。

[Part2_Using_Adaptive_Cruise_Control_Block_in_Simulink.slx](Part2_Using_Adaptive_Cruise_Control_Block_in_Simulink.slx)

## Part3: Using 3D annotation block for vehicle simulation in simulink
1. 修改Adaptive Cruise Control System的以下参数：
    - Controller Behavior: form 0.5 to 0.59
    - Prediction horizon (steps): from 30 to 200
    - Sample time: from 0.1 to 0.5
    - Default spacing (m): from 10 to 5

    运行，模型收敛更快。

2. 将leader vehicle的initial speed改为0，运行，发现车距有负值（即ego vehicle跑到了前面），为避免这种情况发生，增加一个compare to constant->stop.

3. 添加一个VR Sink，导入PlatoonWorld.WRL文件，因为这里只计算longitudinal motion，因此只勾选translation（car red and car blue）。

    VR Signal Expander: 3, \[3]
    
    
## Part4: Implementing a Simple Vehicle Lateral Motion in Simulink
1. 在ego vehicle子系统中搭建Lateral Motion。
2. VR Sink的car blue（ego car）勾选rotation，并通过一个bus creator来连接到ego car，设置4个信号，依次为x、y、z、r，这里是绕y旋转，所以y输入的信号为1.
3. 添加unit delay使其smooth。
4. 将longitudinal和lateral连起来
5. ego car的translation接口通过，第一个接口是侧向，第二个为0，第三个是纵向
6. 将ACC控制器的Sample time改小可以使动画速度变慢


## Part5: Implementing different Driving Modes in Simulink
1. 封装ACC车辆控制模式，其控制ego car
2. 新建Free driving mode
3. 切换条件<=30
4. free driving mode的搭建是不是有问题？

## Part6: Vehicle Behavioral Planner Design Using Stateflow
1. 添加stateflow来控制driving mode

## Part 7: Creating your own 2D Traffic Visualization in Simulink
Create a subsystem named 2D animatiom, which has three input ports of longitudinal position, lateral position and steering angle. add a matrix concatenete and a matlab function.

matrix concatenate: 3, vector

comment out 3D animation

block parameters for 2D animation, then set the sample time to 0.1, so the plot will be slower.

How can use the variables of the matlab function in command window.


