# model-based autonomous traffic simulation framework

>zhangqq  
>Nov2, 2021  
>Nanjing
---

This video series is from YouTube.

## Part1: 
## Part2: 
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


