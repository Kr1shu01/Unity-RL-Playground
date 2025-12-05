# Unity RL Playground - MuJoCo Sim
## 1. 项目概览
本项目用于开发基于 **Unity + MuJoCo 物理引擎** 的机器人强化学习 **sim2sim 仿真环境**，支持人形机器人在复杂工业场景中的双足行走强化学习策略验证以及上肢操作与下肢移动协同控制。场景为人形机器人工业训练场（简化版模块化工业厂区仿真环境），资产为USD 格式，适用于 Unity 2021+。项目深度集成了MuJoCo 3.3.x版本的高保真物理引擎，同时通过USD Importer插件实现了复杂工业场景的高效导入和渲染。
> 🔗 项目需配合 [`loongOpen/loong_sim_sdk_release`](https://github.com/loongOpen/loong_sim_sdk_release) 使用，可形成从前端可视化、物理仿真、控制通信到强化学习决策的完整技术链条，广泛适用于工业机器人安全培训、人形机器人运动控制研究、强化学习算法性能验证以及复杂环境下人机协作测试等多个应用场景。

---
## 2. 安装操作
1、克隆本项目 Mujoco 分支：git clone -b Mujoco 仓库URL...  Unity打开后默认提示是否用安全模式，点击在安全模式下进入，之后按照提示安装以下插件<br>
| Package | 版本要求 | 说明 |
|--------|----------|------|
| MuJoCo | `3.3.x` | 启用高保真物理仿真 [`安装教程`](https://mujoco.readthedocs.io/en/latest/unity.html) |
| USD Importer | `1.0.0` | 支持 `.usd` 场景导入 [`安装教程`](https://docs.unity3d.com/Packages/com.unity.importer.usd@1.0/manual/index.html) |

2、点击 Window >Package Manager > +按钮 > Add package from git URL...  输入https://github.com/google-deepmind/mujoco.git?path=/unity<br>
3、点击 Window >Package Manager > +按钮 > Install package by name...  输入：com.unity.importer.usd<br>
4、进入 Edit > Project Settings > Player > Other Settings > Allow 'unsafe' Code  开启允许"非安全代码"<br>
---
## 3. 运行操作
操作前检查：打开 Unity 项目，确认 Console 无报错（特别是 USD 导入或 MuJoCo 组件初始化异常）。<br>
运行前修改: loong_sim_sdk_release/config/plan_rl.ini 中net配置部分，修改如下 <br>
`netNames= run,run,`<br>
`netKeys = 41,42` <br>
`netOnnxs= policy_3052.onnx,policy_3052.onnx,` <br>

1、启动后台控制脚本（在 `loong_sim_sdk_release/tools/` 目录下运行）<br>
**./run_driver.sh**          # 启动驱动，打印关节信息（等待格物端数据接通）<br>
**./run_interface.sh**       # 启动命令通信服务 <br>
**./run_locomotion.sh**      # 加载腿部运动控制器 <br>
**./run_manipulation.sh**    # 加载手臂操作控制器 <br>
**python3 py_ui.py**         # 打开图形化 UI 控制界面

2、启动 Unity 仿真端<br>
打开 Unity 项目并加载 MujocoSim 场景，点击 Play 按钮，机器人进入 **等待指令状态**

3、初始化机器人状态<br>
在 UI 控制界面 点击 en（上使能），后点击 rc（复位），此时机器人应进入 **复位状态**

4、放置机器人至地面，并实现腿足行走和手臂操纵<br>
- Display1 为第一人称可交互窗口，左下角显示机器人操作说明及实时状态（点击场景地面可实现旁观者移动，ZC为左右转向）。
- 复位完成后，按G将机器人放至脚底接触地面，再按F或取消Assist Flag勾选，此时机器人不再有外力施加，若稳定站立则可进行下一步，否则按H抬升后进行dis（下使能）再重新en（上使能）并rc（复位）放下后取消外力，直到机器人可平稳站立。
- 随后UI控制界面点击rl（强化学习行走策略），此时为策略驱动的平衡姿态，按下Q可踏步，E停止，wasdjl模拟遥杆增量，空格清零，UI另可进行手臂操作，手臂与双腿互不影响，可同时接受指令。
- 若中途机器人摔倒可再次按F施加悬挂力，机器人会回到初始位姿，而后可重新使能和复位。
---
