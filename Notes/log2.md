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
  - LoRA 微调的做法是：**固定原始模型权重，只引入几个小矩阵（称为低秩矩阵）来“调节”特定特征**
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

### 🧾 LoRA 文件是什么？
- LoRA 文件（通常是 .safetensors 或 .pt 格式） 是在已有大模型（如 Stable Diffusion）上“微调”得到的**小模型**。
- 它的作用是：让原始模型在**保持基础能力的同时，学会一些“新特征”或“新风格”**，比如某个人物、某种服装、某种画风。
- LoRA 文件 本质上**只包含一小部分权重矩阵（参数），而不是整个大模型的全部参数**。主要内容包括：

| 内容          | 作用                                 |
| ----------- | ---------------------------------- |
| 📐 一组“低秩矩阵” | 对 Stable Diffusion 原有层的某些部分进行小范围调整 |
| 🧠 新学到的特征   | 这些矩阵中的元素蕴含着某角色的脸型、服饰结构、发色、画风等信息 |
| 💡 微调范围和方法  | 记录哪些模块（如 UNet、Text Encoder）应该被微调以及微调方法  |

- 💡 LoRA 的核心作用：原始模型就像一位“经验丰富但通用”的画师，而LORA文件可以让 Stable Diffusion “临时获得” 某种能力或风格，但**不会改变原始模型本身**，如同一个临时插件，随插随用，可以随时切换、组合、卸载。

###  一种风格的 LoRA文件 是如何训练出来的（LORA微调的过程）？
- 用**一批风格一致的图像 + 对应文字描述**，对大模型进行“**低秩参数微调（LORA微调）**”，训练出一个**轻量的风格增强模块（LoRA 文件）**。
- 📌 详细流程分 5 步（以 Stable Diffusion LoRA 为例）：
#### 🔹 步骤1：收集数据（最重要）
- 需要准备 30～300 张特定风格的图片
- 每张图片配有一段对应描述（Prompt）
  - 如："cyberpunk city, neon lights, rainy street, night, high detail"
  - 描述越准确，模型学得越好
- ✅ 工具推荐：
  - 自动打标签：BLIP, DeepBooru
  - 手动精调关键词效果更好

#### 🔹 步骤2：图像预处理（规范化）
- 调整图片大小（如：512x512 或 768x768）
- 图像格式统一（PNG或JPEG）
- 可选操作：
  - 自动裁剪/增强（保持风格）
  - 使用caption工具生成标签

#### 🔹 步骤3：设置训练参数
- 使用工具如：
  - [Kohya LoRA Trainer](https://github.com/bmaltais/kohya_ss)（最常用）
  - 或者 DreamBooth + LoRA 支持工具链
设置内容包括：
| 项目     | 示例值                 | 说明             |
| ------ | ------------------- | -------------- |
| 基础模型   | Anything v5         | 训练是在这个模型基础上进行的 |
| Epochs | 10\~30              | 训练轮数           |
| 学习率    | 1e-4 \~ 5e-5        | 通常比较低          |
| Rank   | 4 / 8 / 16          | 越大表示 LoRA 影响越强 |
| 网络模块   | UNet / Text Encoder | 决定训练哪些部分       |
| 输出格式   | `.safetensors`      | 通常为安全权重格式      |

#### 🔹 步骤4：开始训练（用显卡跑）
- 用显卡（如 RTX 3060/3090/4090）运行训练脚本
- 每轮训练模型会生成中间结果
- 完成后输出一个小型的 .safetensors 文件（几MB）

#### 🔹 步骤5：测试与微调
- 加载这个 LoRA 文件，结合提示词在 Stable Diffusion 中测试效果
- 如果画面不理想：
  - 调整训练集、再训练
  - 调整权重值（如 <lora:cyberpunk_v1:0.6>）
  - 优化提示词

#### ✅ 总结：LoRA 风格训练关键点
| 要素             | 说明                     |
| -------------- | ---------------------- |
| 🎨 数据风格统一      | 所有训练图最好是同一画风           |
| 🧾 Prompt 质量   | 训练时的提示词越精准，模型越好学       |
| 🧠 模型基底选择      | 选接近目标风格的基础模型作为起点更容易成功  |
| ⚙️ Rank / 模块控制 | 控制 LoRA 的“学习强度”与“学习范围” |

### 关键词：低秩矩阵 


### 什么是正向提示词和负向提示词？
#### 🟢 正向提示词（Positive Prompt）是什么？
正向提示词就是你**希望图像中出现的内容**，用来“引导 AI 去生成”某些元素或风格。

✅ 举例：
```
1girl, blue hair, school uniform, anime style, looking at viewer, soft lighting, highly detailed
```
- 有一个女孩（1girl）
- 她有蓝色头发（blue hair）
- 穿着校服（school uniform）
- 风格是动漫（anime style）
- 细节丰富、光线柔和


#### 🔴 负向提示词（Negative Prompt）是什么？
负向提示词是你**不希望图像中出现的内容**，用于**排除不需要的元素、风格、瑕疵、错误**等。
✅ 举例：
```
low quality, blurry, bad anatomy, extra limbs, deformed hands, text, logo, watermark, bad proportions
```
- 不要模糊（blurry）
- 不要手指变形（deformed hands）
- 不要奇怪的肢体（extra limbs）
- 不要水印、文字等杂质（logo, text）

#### 📊 正向 vs 负向提示词对比表
| 项目      | 正向提示词（Positive）            | 负向提示词（Negative）             |
| ------- | -------------------------- | --------------------------- |
| 📌 作用   | 想要生成的内容                    | 想排除的内容                      |
| 📸 示例   | "1girl, long hair, sunset" | "lowres, bad hands, blurry" |
| 🔧 控制目标 | 加入画面元素/风格                  | 剔除错误/瑕疵/杂物                  |
| 💡 效果   | 定义画面主体与风格                  | 提高质量、清晰度                    |

#### ✅ 实战例子（正负提示词组合）
🎨 想生成一个“动漫风格的和服少女”：
```
正向提示词：
1girl, kimono, japanese temple background, cherry blossoms, anime style, looking at viewer, detailed eyes

负向提示词：
blurry, bad hands, extra fingers, long neck, deformed, watermark, text, lowres
```
- 负向提示词可以通用: 可以将负向提示词写好后保存下来，在多个作品中复用.

#### 负向提示词模板
##### ✅ 一般通用负向提示词模板（适用于大多数图像）
```
lowres, bad anatomy, bad hands, text, error, missing fingers, extra digit, fewer digits, cropped, worst quality, low quality, jpeg artifacts, signature, watermark, username, blurry
```
##### 🎨 二次元插画专用负向提示词（动漫风格强化版）
```
nsfw, lowres, bad anatomy, bad hands, deformed, blurry, watermark, signature, text, error, jpeg artifacts, ugly, duplicate, morbid, mutated, extra fingers, missing fingers, fused fingers, long neck, low quality, worst quality, normal quality, distorted limbs, cross-eye, bad proportions
```
##### 👤 人像专用负向提示词（适合写实/二次元人脸）
```
deformed, mutated, extra limbs, bad hands, extra arms, extra legs, malformed hands, missing arms, missing legs, mutated hands, fused fingers, long neck, long face, bad eyes, cross-eyed, blurry, jpeg artifacts, watermark, text
```
##### 🌆 风景类专用负向提示词（清晰度提升）
```
blurry, lowres, overexposed, underexposed, low contrast, watermark, signature, text, distorted, jpeg artifacts, noise, bad composition
```

##### 💡 提示词使用技巧
| 技巧                      | 说明                                      |
| ----------------------- | --------------------------------------- |
| ✅ 放在 Negative prompt 栏中 | 大多数绘图界面（如 WebUI、ComfyUI）都支持负向提示栏        |
| 🔁 可以常驻使用               | 可以设为默认项，除非风格需要特殊调整                      |
| 🔧 逐步调整                 | 若画面有问题，可以根据错误现象增删关键词                    |
| 🔣 支持权重控制               | 如使用 `(bad hands:1.4)` 让其影响更强（在支持该语法的平台） |

