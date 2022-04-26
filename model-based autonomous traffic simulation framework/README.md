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
- Prediction horizon (steps)：300 from 30
- Controller Behavior：0.8 from 0.5

![image](https://user-images.githubusercontent.com/48160597/165052498-7908f468-0beb-4de2-baef-0b92b581071e.png)

将Vehicle Model 2的Initial Position和Initial Speed分别改为0和30，Vehicle 1仍保持100，20，但Reference Speed改为20 from 30.

Sum使减号在上面：-+|

运行100s，可看到缓慢进程。

Prediction horizon (steps)影响输出曲线和仿真速度。

![image](https://user-images.githubusercontent.com/48160597/165055431-51408c67-5450-4aea-8896-1d7799285539.png)

[Part2_Using_Adaptive_Cruise_Control_Block_in_Simulink.slx](Part2_Using_Adaptive_Cruise_Control_Block_in_Simulink.slx)






## Part3: Using 3D annotation block for vehicle simulation in simulink
1. 修改Adaptive Cruise Control System的以下参数：
    - Controller Behavior: 0.59 from 0.8
    - Prediction horizon (steps): 200 from 300
    - Sample time: 0.5 from 0.1
    - Default spacing (m): 5 from 10

    运行，模型收敛变快，且曲线有所改变。

2. 将vehicle 1的initial position和initial speed改为10和1，vehicle 2仍为0和30，运行，发现车距有负值（即vehicle 2跑到了前面），为避免这种情况发生，增加一个compare to constant->stop.

3. 添加一个VR Sink，导入PlatoonWorld.WRL文件，因为这里只计算longitudinal motion，因此只勾选translation（car red and car blue）。
  - 将两个vehicle的position输出直接连到VR sink会报错，因为VR sink需要三维（三个方向）输入，而这里的position只是纵向位移，因此，需要接一个数据转换VR signal Expander。
 
  - VR signal expander：
    - Output width：3 from 4
    - Indies：[3] from [2 4]
  
4. 修改vehicle 1的initial position和initial speed为30和15，运行查看动画。
5. 点击照相机左边的图标，可进入VR sink编辑界面，可调整sample time为0.05，视图CAM_CARLotus运行查看
    
![image](https://user-images.githubusercontent.com/48160597/165201234-059b7f4f-4034-4ace-a02f-e221375828e1.png)

[Part3_Using_3D_Animation_Block.slx](Part3_Using_3D_Animation_Block.slx)
    
    
    
    
    
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


