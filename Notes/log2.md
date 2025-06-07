# 基于AI的二次元风格绘画创作-实践

## 操作链接
- [AILib1](https://www.liblib.art/modelinfo/3fa1d8b7e1634317b07ca8b4da0e1127?versionUuid=9fc0d6a88c594e74a2f6ca88b99457fd)
- [AILib2](https://www.liblib.art/sd?id=1749306479557)

## 常见问题
### 什么是LORA？
- LoRA（Low-Rank Adaptation） 是一种**参数高效的微调方法**。
- LoRA 是一种**轻量级微调“插件”**，能让**基础模型**快速掌握新风格、新角色、新元素，而**无需训练整个模型**。
-----------
- ⚙️ 技术原理（简化解释）
  - 原始模型参数是非常庞大的（几亿级别）
  - LoRA 的做法是：固定原始模型权重，只引入几个小矩阵（称为低秩矩阵）来“调节”特定特征
  - 训练出的 LoRA 文件很小（通常几 MB ~ 几百 MB），但能显著改变输出风格
-----------
- 🧠 举个例子来说明
假设你有一个**通用的动漫模型（如 Anything v5）**，但是你想画出：
  - 特定风格：比如新海诚风、EVA风、手绘漫画风
  - 特定人物：比如原创角色、初音未来、小红书虚拟博主
  - 特定场景：赛博朋克城市、和风庭院、战场背景
你只需要**加载特定风格对应的 LoRA 文件**，在**提示词中加入调用格式**，例如：
```
<lora:MyCharacter_v2:0.8>, 1girl, long hair, red eyes, ...
```
其中：
- MyCharacter_v2 是你加载的 LoRA 名称
- 0.8 是权重，决定该 LoRA 的“影响程度”
------------------
- 📦 LoRA 文件去哪找？
  - [Civitai.com](https://civitai.com/)
  - [Hugging Face](https://huggingface.co/)
--------------
- 🛠️ 如何使用 LoRA？
  - 你需要使用支持 LoRA 的平台，比如：Automatic1111 WebUI ComfyUI InvokeAI
  - 📌 使用步骤（以 WebUI 为例）：
    - 将 .safetensors 的 LoRA 文件放入 /models/Lora/ 文件夹
    - 重启 WebUI
    - 在提示词中使用格式：
```
<lora:cyberpunk_girl_v1:0.7>, 1girl, neon lights, cyberpunk city
```



### 什么是正向提示词和负向提示词？
