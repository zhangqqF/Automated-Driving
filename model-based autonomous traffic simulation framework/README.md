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

