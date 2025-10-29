# user-in-the-box-simulator
基于Gymnasium和MuJoCo的仿真器，集成生物力学模型、感知模块与强化学习任务，支持分层强化学习与可视化渲染

一、环境准备
1. 虚拟环境创建与激活
```powershell
# 切换到项目根目录
cd C:\Users\86186\user-in-the-box

# 创建Python 3.9虚拟环境（兼容性最优）
python -m venv venv --python=3.9

# 激活虚拟环境（Windows PowerShell）
\venv\Scripts\Activate.ps1
```
2. 依赖安装
使用国内镜像源加速安装：
```powershell
# 核心依赖
pip install gymnasium==1.2.1 mujoco==2.3.5 stable-baselines3==2.2.1 pygame==2.5.2 opencv-python==4.9.0.80 -i https://pypi.tuna.tsinghua.edu.cn/simple

# 辅助依赖
pip install numpy==1.26.4 scipy==1.11.4 matplotlib==3.8.4 ruamel.yaml==0.18.6 certifi -i https://pypi.tuna.tsinghua.edu.cn/simple
```
二、核心文件说明
1. simulator.py（仿真器核心）
功能：继承 gym.Env，实现仿真环境的初始化、步骤推进（step）、环境重置（reset）和可视化渲染（render），集成生物力学模型、感知模块和任务逻辑。
运行方式：需通过调用脚本（如 test_simulator.py）运行，示例见 “三、运行步骤”。
3. main.py（辅助脚本）
功能：基于 certifi 查询 CA 证书信息（路径或内容），用于验证网络请求的安全性。
运行方式：
```powershell
# 查看证书路径
python main.py

# 查看证书内容
python main.py -c
```
三、运行步骤
1. 仿真器运行（simulator.py）
步骤 1：创建运行脚本 test_simulator.py
在项目根目录新建 test_simulator.py，写入以下代码：
```python
from uitb.simulator import Simulator

# 加载仿真器（需替换为实际仿真器路径，如 "uitb/simulators/arm_simulation"）
simulator_folder = "uitb/simulators/your_sim_folder"  
env = Simulator.get(
    simulator_folder=simulator_folder,
    render_mode="human",  # "human" 弹出Pygame窗口；"rgb_array" 输出图片数组
    render_mode_perception="embed"  # 感知图像嵌入主窗口
)

# 重置环境
obs, info = env.reset(seed=42)
print("初始观测值：", {k: v.shape for k in obs})

# 运行 1000 步仿真（随机动作示例）
for step in range(1000):
    action = env.action_space.sample()
    obs, reward, terminated, truncated, info = env.step(action)
    if step % 100 == 0:
        print(f"第 {step} 步 | 奖励：{reward:.2f} | 终止状态：{terminated}")
    if terminated or truncated:
        obs, info = env.reset()

# 关闭仿真（释放资源）
env.close()
```
步骤 2：执行脚本
```powershell
python test_simulator.py
```
此时会弹出 Pygame 窗口，展示仿真过程（如机械臂运动、感知模块渲染）。
2. 辅助脚本运行（main.py）
在终端执行以下命令，查看证书信息：
```powershell
# 查看证书路径
python main.py

# 查看证书内容
python main.py -c
```
四、依赖清单
|库名称	|版本	|用途|
|----------|------|--------------|
|gymnasium |1.2.1|强化学习环境接口|
|mujoco|2.3.5|	物理仿真引擎|
|stable-baselines3	|2.2.1|	强化学习算法库|
|pygame	|2.5.2|	可视化渲染|
|opencv-python |4.9.0.80	|图像感知处理|
|numpy	|1.26.4	|数值计算|
|scipy	|1.11.4	|科学计算|
|matplotlib|	3.8.4|	数据可视化|
|ruamel.yaml|	0.18.6|	配置文件解析|
|certifi|	2025.10.10|	CA 证书管理|
