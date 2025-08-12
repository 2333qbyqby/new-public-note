### 论文笔记
#### 创新点
- **新型生成框架​**​：提出了一种创新的生成框架，能够学习具身角色控制器以执行多样化的动作技能。
- ​**​离散动作流形的端到端训练方法​**​：通过隐空间中两个类别概率分布的匹配，实现了离散动作流形的端到端训练及采样机制。
- ​**​多模态输入支持​**​：支持超越传统三点输入的多样化传感器模态（如摇杆或按钮控制），同时保留用户原始动作上下文。
- ​**​新型AR/VR动作数据集​**​：构建了一个面向增强现实（AR）与虚拟现实（VR）应用的多样化人体动作数据集，为训练与验证提供基础资源。
### 项目Readme概括
### 项目概览

- 技术：面向具身角色控制的类别型“代码本匹配”（Categorical Codebook Matching）。
- 目标：在不同输入（鼠标、三点追踪、手柄等）下，生成多样、真实、响应性强的全身动作。
- 联系方式：[sebastian.starke@mail.de](mailto:sebastian.starke@mail.de), [paulstarke.ps@gmail.com](mailto:paulstarke.ps@gmail.com)

### 环境与资源准备

 克隆仓库。
 下载并解压 MotionAssets.zip 到 Assets/Projects/CodebookMatching/MotionCapture
 下载并解压 Networks.zip 到 Assets/Projects/CodebookMatching/
 已编译演示可用：Windows 与 Mac；也可在 Unity Editor 中打开 Demo_Standalone
### 主要演示内容（Assets/Projects/CodebookMatching/Demo_*）

   Standalone 鼠标输入 Demo（30fps 预期）
     Locomotion Control（不追踪上半身）
       可视化切换：Draw Control / Draw Sequence / Draw History / Draw Codebook
      - 参数：
        - KNN：从代码本采样候选数，增大可提升多样性与稳定性
        - Rollout：滚动使用多帧预测，平滑度↑但响应↓
    - Hybrid Control（追踪上半身）
      - 将三点输入与鼠标/摇杆/按钮混合控制
      - 可视化：Draw Control / Draw Body Prediction / Draw Future Prediction
      - 切换参考资产可测试不同上半身参考；进度结束或切换后会重置
      - 操作技巧：移动时配合旋转目标，质量更佳
    - 运行提示
      - Windows：运行 Unity.exe

 VR Demo（三点追踪与混合模式）
  - 设备：Quest2/Quest3（头显+双手柄）
  - 安装到头显
    - 下载 VR Demo.apk，使用 Meta Quest Developer Hub 安装
    - 在设备 Library → Applications → Installed Prototypes 启动
    - 进入后按左手柄 X 校准（基于头部位置）
    - 模式与操作：
      - X：高度校准（正视前方）
      - A：三点追踪模式（头显+手柄）
      - B：混合模式（头显+手柄+左摇杆移动）
      - 左摇杆：移动（混合模式）
  - 从 PC 串流运行
    - 在 Unity 打开 Demo_ThreePointTracking 场景
    - 连接 Link，启用 Link 模式，进入 PlayMode
    - 左手柄主按钮校准（正视，避免低头/倾斜造成误差）
    - 混合模式可在虚拟中行走，同时嵌入真实上半身动作

 训练质量测试 Demo（Assets/Projects/CodebookMatching/Demo_TrainingTest）
  - TrackingSystem_Only.unity
    - 仅用三点信号重建当前上半身并估计未来运动
    - 组件：TrackingSystem
      - Tracking Network（MLP）：基于三点历史预测当前上半身姿态与根节点更新
      - Future Network（MLP）：估计未来上半身与根运动
    - 在 MotionEditor 回放参考动作对齐检查（红色上半身与根应对齐）
  - ThreePointTracking_ReferenceClipViaMotionEditor.unity
    - 通过 MotionEditor 回放参考片段驱动全身
    - 组件：MotionController
      - Lower-Body Network（CM）：自回归预测下半身未来序列，使用 TrackingSystem 的未来控制信号
      - Tracked-Upperbody Network（MLP）：基于追踪与下半身历史预测当前上半身
    - 对比黑色（预测）与灰色（真值）
  - ThreePointTracking_MoveTrackingSystemViaMouseDrag.unity
    - 通过拖动 TrackingSystem 物体的位置/旋转来生成相应下半身动作

 结果复现与训练流程
  1. 打开 Assets/Projects/CodebookMatching/MotionCapture/Scene.unity
  2. 选中场景中的 MotionEditor
  3. 打开 AssetPipeline（Header → AI4Animation → Tools → AssetPipeline），设置：
     - ExportPipeline
     - Write Mirror：勾选
     - Subsample Target Framerate：勾选
     - Sequence Length：15
     - Mode：对应要导出的网络类型（详见论文或上文各 Demo 说明）
  4. 点击 Refresh 与 Process，生成训练数据至 SIGGRAPH_2024/PyTorch/Dataset
  5. 在 PyTorch/Datasets 新建对应网络名文件夹，将上一步数据拷入
  6. 按网络类型进入 PyTorch/Models/CodebookMatching 或 PyTorch/Models/MultiLayerPerceptron
  7. 运行 Network.py 开始训练（对应所选网络）

- 其他提示
  - Demo 操作对动作表现有影响，尤其是鼠标移动时的朝向控制需要练习
  - 两类核心网络家族：
    - Codebook Matching（CM）：用于下半身自回归序列预测
    - 多层感知机（MLP）：用于上半身当前/未来、以及追踪融合
  - 资源目录关键路径：
    - Motion 数据：Assets/Projects/CodebookMatching/MotionCapture
    - 预训练网络：Assets/Projects/CodebookMatching/Networks
    - 数据集导出：SIGGRAPH_2024/PyTorch/Dataset
    - 训练数据放置：PyTorch/Datasets/<网络名>
    - 训练脚本：PyTorch/Models/(CodebookMatching|MultiLayerPerceptron)/Network.py
### 项目问题
1、打开Unity项目的package文件需要更改，改成官方Unity地址
2、oculus需要更新机器版本来打开打包好的demo
3、调试的时候需要切换desplay才能在头显中显示
### 项目代码

