# 小迈步视频学习

>zhangqq  
>Nanjing  
>April 11, 2022
---



## 2022 小迈步车辆建模与仿真系列讲座
### Part 2: 搭建整车动力学模型用于底盘控制开发及整车性能评估
>楚骏楠&emsp;MathWorks中国

![image](https://user-images.githubusercontent.com/48160597/163537240-74689d09-ee86-41af-ad3c-3e98009d9c77.png)


#### [Vehicle_3DOF_Basix](Vehicle_3DOF_Basix.slx)

  ![image](https://user-images.githubusercontent.com/48160597/162764920-a7d72f55-d1e1-47a2-88ca-6f86e7eb0d22.png)

🌾 **Vehicle Body 3DOF Dual Track:** 三自由度车辆模型（1235)

Simulink中的车辆自由度名称：

  | DOF | Name | Description |
  | :-: | :-: | :-: |
  | 1 | Surge | 纵向 |
  | 2 | Sway | 横向 |
  | 3 | Heave | 垂向 |
  | 4 | Roll | 侧偏 |
  | 6 | Yaw | 横摆 |
  | 5 | Pitch | 俯仰 |
  

#### [Vehicle_3DOF_Basix_add_plot](Vehicle_3DOF_Basix_add_plot.slx)
  
将以下部分合并为子系统3D_Visualization:

  ![image](https://user-images.githubusercontent.com/48160597/162965023-124d6cfe-71ad-4996-8ad9-225c04f3e696.png)


从matlab自带的CRReferenceApplication模型中将子系统CRReferenceApplication/Visualization/Vehicle XY Plotter/XY View Ref复制过来：

  ![image](https://user-images.githubusercontent.com/48160597/162964703-8468c7a4-c3dc-47f9-96d5-5f30e9eba04e.png)
  
并将其修改成如下：
  - 双击编辑Vehicle XY Plotter，关掉选项Display speed, RPM and Gear和Display tire forces，并把三个错误提示的参数Distance to front axle from CG, a [mm], Distance to front axle from CG, b [mm]和Track width, w [mm]均改为1.4。
  - 双击修改所有Bus selector的传输信号：将原来的信号删除，并添加新的selected element（和之前3D_Visualization的信号一样)：InertFrm.Cg.X, InertFrm.Cg.Y, InertFrm.Cg.Z, InertFrm.Ang.psi。
  - 此时发现所有Bus selector的名称是隐藏的，且Hide Automatic Block Names是关闭状态。选中单个Bus selector（不能多选），鼠标移到改Bus selector顶部，出现蓝色三个点的省略号，把鼠标移到省略号上面，选择Show block name。
  
  ![image](https://user-images.githubusercontent.com/48160597/162971916-b0b66e03-93b3-421f-a121-80c252061794.png)



Enter the matlab command *openExample('vdynblks/VehicleLateralAccelerationAtDifferentSpeedsExample')* to open the CRReferenceApplication project （the command would be copied from the example [Vehicle Lateral Acceleration at Different Speeds](https://www.mathworks.com/help/vdynblks/ug/vehicle-lateral-dynamics-at-different-speeds.html?searchHighlight=CRReferenceApplication&s_tid=srchtitle_CRReferenceApplication_1)).

#### [Vehicle_3DOF_Basix_demon](Vehicle_3DOF_Basix_demon.slx)

模拟停车场

  - 修改车辆初始角度位置：Vehicle Body 3DOF Dual Track，yaw -> Initial yaw angle 1.75 *(≈90°) from 0*.
  - 修改车速：constant (connect to xdotin of the vehicle): 0.5 *from 10*。
  - 修改方向盘灵敏度：Kinematic Steering -> General -> Steering Range: 15 *from 1.25\*pi*
  - 修改场景：Simulation 3D Scene Configuration -> Scene name: Large parking lot

#### 横向动力学特性

![image](https://user-images.githubusercontent.com/48160597/163531898-f84e40cc-e53c-4312-b751-ecd6e4ec4324.png)


#### 6自由度车辆模型

6自由度模型指的是车身的自由度（即完整的刚体自由度），而非车辆的自由度，车辆自由度还有轮胎的滚动。

![image](https://user-images.githubusercontent.com/48160597/163537752-5116db10-37d0-4ad5-8770-7676014f49eb.png)

![image](https://user-images.githubusercontent.com/48160597/163537802-cd9e4deb-0ed1-4a34-bd14-43092dc8a832.png)

两种建模工具箱：

![image](https://user-images.githubusercontent.com/48160597/163538297-e079cfac-647d-42a6-b748-7b13d67a4796.png)




