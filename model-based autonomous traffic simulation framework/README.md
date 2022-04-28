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
1. 在vehicle 2中增加侧向运动：

 ![image](https://user-images.githubusercontent.com/48160597/165205588-e88ee5cb-30b6-47b4-90dc-ac7b53478c0b.png)

2. 增加VR Sink的car blue（vehicle 2）的rotation，VR sink的rotation接收的是四个维度的数据（x、y、z、r），因此这里需要一个bus creator，因为是绕y旋转，所以y输入的信号为1，x和z为0：

 ![image](https://user-images.githubusercontent.com/48160597/165206035-fb8af540-b8f1-46bf-8a9a-c395b97ac99b.png)

4. 添加unit delay使其smooth（为何）：

 ![image](https://user-images.githubusercontent.com/48160597/165206166-c9f90f07-0ff6-46b4-a72b-e414f4325d34.png)

5. 将longitudinal motion和lateral motion结合起来：

 ![image](https://user-images.githubusercontent.com/48160597/165226877-d9531f03-c374-48f1-992e-6c32ce70bbd6.png)

 z在VR sink中是longitudinal motion，x是lateral motion。
 
6. 将vehicle 2连接到VR translation之间的expander改为bus creator。将转角改为pi/2，运行

 ![image](https://user-images.githubusercontent.com/48160597/165227550-cd65a206-50ef-47c5-b2e7-4176a23ad330.png)

[Part4_Implementing_a_Simple_Vehicle_Lateral_Motion.slx](Part4_Implementing_a_Simple_Vehicle_Lateral_Motion.slx)

[只加转角的过程数据](Part4_Implementing_a_Simple_Vehicle_Lateral_Motion_1.slx)






## Part5: Implementing different Driving Modes in Simulink
1. 封装ACC车辆控制模式为Following Driving Mode
2. 新建Free driving mode

 ![image](https://user-images.githubusercontent.com/48160597/165231846-7fdca675-fc60-4649-b888-4a4585b4965c.png)

不理解之前是减去速度，这里为什么直接用速度减加速度？

3. 增加切换条件>=30，满足条件，则执行Free Mode

 ![image](https://user-images.githubusercontent.com/48160597/165233265-103b0171-43e8-4aba-9691-6f5a0c4ca5d8.png)

[Part5_Impleting_Different_Driving_Modes.slx](Part5_Impleting_Different_Driving_Modes.slx)







## Part6: Vehicle Behavioral Planner Design Using Stateflow
1. 用stateflow来控制driving mode，添加chat：

 [relativeDistance <= 50]
 
 点击菜单栏MODELING->Sympols Pane，将relativeDistance和drivingMode分别设置为input和output。
 
2. 用stateflow替换compare to constant，multi switch替换switch，并增加紧急制动模式，紧急制动下加速度为-5：

 ![image](https://user-images.githubusercontent.com/48160597/165238718-182132a7-50fa-4645-97c3-f2636e198678.png)

[Part6_Vehicle_Behavioral_Planner_using_Stateflow.slx](Part6_Vehicle_Behavioral_Planner_using_Stateflow.slx)







## Part 7: Creating your own 2D Traffic Visualization in Simulink
1. 创建子系统2D Visualizatrion：
 
 ![image](https://user-images.githubusercontent.com/48160597/165242290-d36a11a5-a63e-44f9-a153-11ca227f3752.png)
 
  - 双击Matrix Concatenate修改其mode为vector

  - 双击matlab func编辑如下：
  ```matlab
  function plotVehicle(v1)

  x1 = v1(1);
  y1 = v1(2);
  yaw1 = v1(3);

  plot(x1,y1,'o');
  ```
2. 连接2D visualization，command out 3D visualization，运行

  ![image](https://user-images.githubusercontent.com/48160597/165242997-36f14cb7-04cc-4d7a-a981-c4e19f4288f6.png)

3. 用矩形代表车辆，矩形的构造公式：

 ![image](https://user-images.githubusercontent.com/48160597/165266117-4448ef80-77df-4b3b-bee4-1d3047ad0e2a.png)

4. 当matlab函数中形参增加时，block就会增加一个输入口
5. plot函数，如果不hold on，则后面的plot会抹除之前的plot。这里直接在matlab func的mask edit（ctrl+m）设置initialization：
  ```matlab
  close all;
  figure('units','normalized','outerposition',[0 0 1 1]);
  % grid on
  axis equal;
  hold on
```
6. 为置车辆于下方车道内，需将两辆车的初始y向位置设置为-2.5（车道宽为5）
7. 将车的长度考虑进collision的情况

 ![image](https://user-images.githubusercontent.com/48160597/165420916-3c4728fd-096e-41a5-9cb8-48c6d7382916.png)

改为

 ![image](https://user-images.githubusercontent.com/48160597/165421193-0402f9d6-5e3c-49e3-93e3-c6b10b35614d.png)


[Part7_Creating_2D_Traffic_Visualization.slx](Part7_Creating_2D_Traffic_Visualization.slx)







## Part 8: Cruise Control Using PID for the Third Vehicle
1. 复制vehicle 2为vehicle 3，然后将acc controller改为PID控制

 reference distance = ego * headwayTime

![image](https://user-images.githubusercontent.com/48160597/165431795-78764a18-6ed7-47f8-8251-3249e72ea21d.png)

2. 加延迟的作用是什么？

 ![image](https://user-images.githubusercontent.com/48160597/165432232-730a8136-14b0-4f56-bc32-2d3f1da1f726.png)

3. 加上PID控制器：

 ![image](https://user-images.githubusercontent.com/48160597/165447186-a0d4a10f-4c44-4ab0-962e-86398c2139e5.png)

- PID controller参数：
  - P: 2
  - I: 0.04
  - D: 2.1
  - Filter Coefficient: 230
  - Limit: [2 -3]
  - Anti-winup method: Damping

4. 增加Vehicle 3和Vehicle 2 collision的判定，并与之前vehicle 2和vehicle 1的判断用OR连接起来。

[Part8_Cruise_Control_Using_PID_for_the_Third_Vehicle.slx](Part8_Cruise_Control_Using_PID_for_the_Third_Vehicle.slx)

[Part8_Cruise_Control_Using_PID_for_the_Third_Vehicle_withoutPID.slx](Part8_Cruise_Control_Using_PID_for_the_Third_Vehicle_withoutPID.slx)









## Part 9: Lane-Changing Implementation
1. 在speed积分器中增加limit，设置lower limit为0 from -inf，然后删掉积分器后面的saturation。
2. vehicle 3的relative speed接地（termination）。
3. 将vehicle 3的collision判定接地，0接代替它接OR，即不考虑vehicle 3的collision。再将stateflow接地，代之与1接multi switch，使vehicle 3始终处于free driving mode。
4. 调整step来调节变道（搞不清楚step time等如何影响输出）

  ![image](https://user-images.githubusercontent.com/48160597/165459317-74fa2c49-7416-409d-aaf3-96db7573a105.png)

5. 仿真速度快慢的调整

[Part9_Lane_Changing.slx](Part9_Lane_Changing.slx)









## Part 10: Dashboard for Lane-Changing
构建dashboard，通过knock调整转角：

 ![image](https://user-images.githubusercontent.com/48160597/165470132-a8941693-ad66-4422-b63b-f4916c8686f4.png)


[Part10_Dashboard_for_Lane_Changing.slx](Part10_Dashboard_for_Lane_Changing.slx)







## Part 11: Unreal Engine in Simulink Using 3D Scenes

1. 在自动驾驶工具箱中可找到Simulation 3D Vehicle with Ground Following和Simulation 3D Scene Configuration，二者sample time均改为0.1，关掉2D visualization，运行
2. 所用版本和视频中的版本，unreal engine中车辆在车道中的默认位置不一样，Simulation 3D Vehicle with Ground Following中的初始位置是连同车道一起改变，而不是改变车辆与车道之间的相对位置，车辆在车道中的位置通过车辆的yPos来改变。
3. 2D visualization使仿真很卡，关掉后仿真就顺畅了
4. 无论增加多少个Simulation 3D Vehicle with Ground Following，都会在Simulation 3D Scene Configuration中一起显示，Scene view只是聚焦哪个Simulation 3D Vehicle with Ground Following。
5. 将stateflow和三辆车的初始位置、速度改成和视频一样：
  - [40 20]
  - [20 25]
  - [0 20]
 
  ![image](https://user-images.githubusercontent.com/48160597/165488335-667ae81d-03a2-4a8b-ba7a-464efe4a8339.png)

[Part11_Unreal_Engine.slx](Part11_Unreal_Engine.slx)





## Part 12: MATLAB-GitHub Integration







## Part 13: Kinematic and Dynamic Bicycle Model Implement

[Coordinate Systems in Vehicle Dynamics Blockset](https://www.mathworks.com/help/vdynblks/ug/coordinate-systems-in-vehicle-dynamics-blockset.html?s_tid=srchtitle_coordinate%2520system_4)

[Coordinate Systems in Automated Driving Toolbox](https://www.mathworks.com/help/driving/ug/coordinate-systems.html?s_tid=srchtitle_coordinate%2520system_1)


