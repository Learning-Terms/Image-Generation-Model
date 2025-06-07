# 利用AI模型来创作二次元风格插画-概要
## 🎨 AI 二次元插画创作完整工作流
### 🧭 总览流程图
```
确定创作目标 → 模型准备 → 提示词构思 → 模型加载与参数设置 → 图像生成 → 精调与后处理 → 导出/投稿
```
#### 🧩 步骤详解
##### 🔹 Step 1：明确创作目标
- 是角色立绘、动态插图、头像、场景图？
- 风格是清新日系、厚涂、赛博朋克还是魔幻？
- 希望有背景？多人物？复杂服饰？
✅ 输出：基本设想 + 关键词草稿

##### 🔹 Step 2：准备模型与工具
✅ 工具推荐（选其一）：
| 工具                            | 说明              | 推荐人群   |
| ----------------------------- | --------------- | ------ |
| **Automatic1111 WebUI**       | 最全功能，支持扩展插件（推荐） | 中高级用户  |
| **ComfyUI**                   | 可视化工作流，逻辑清晰     | 高级用户   |
| **Mage.space / PlaygroundAI** | 免安装在线平台         | 新手快速上手 |

✅ 模型推荐：
| 模型名称                   | 风格              | 下载网站    |
| ---------------------- | --------------- | ------- |
| **Anything v5 / v4.5** | 通用二次元风格         | Civitai |
| **MeinaMix**           | 柔和少女风           | Civitai |
| **PastelMix**          | 轻柔粉彩画风          | Civitai |
| **Counterfeit v3**     | 带有油画感和现实质感的二次元风 | Civitai |

##### 🔹 Step 3：提示词（Prompt）撰写
```
masterpiece, best quality, 1girl, looking at viewer, long hair, blue eyes, school uniform, cherry blossom, anime style, soft lighting, full body, detailed background

```
- 正向提示词（positive prompt）：描述你想要的形象、细节、风格
- 负向提示词（negative prompt）：去除不需要的内容（如“blurry, low quality, bad anatomy”）

✅ 可使用提示词网站如：
- [PromptHero](https://prompthero.com/)
- [Civitai Prompt Examples](https://civitai.com/)

##### 🔹 Step 4：进阶控制（可选）
| 功能      | 工具名                               | 说明            |
| ------- | --------------------------------- | ------------- |
| 姿势/构图控制 | ControlNet (OpenPose/Depth/Canny) | 用草图或参考图控制姿势   |
| 风格/角色控制 | LoRA、Textual Inversion            | 指定画风、角色设定、服装等 |
| 多人物     | Regional Prompt / Tiled Diffusion | 控制每一部分区域提示词   |


##### 🔹 Step 5：生成图像（Sampling）
- 设置采样步数（20-40）、分辨率（512x768 或 768x1024）
- 选择采样器（DPM++ 2M Karras 常用）
- 如果不满意可以用 img2img 进行调整（修改姿势/构图）

##### 🔹 Step 6：图像后处理
- 放大高清：使用 Upscaler（如 ESRGAN）
- 细节修复：使用 Inpaint（局部修复工具）
- 背景替换：在 PS 或 Clip Studio Paint 里手动修改也很方便

##### 🔹 Step 7：保存导出与分享
- 可导出为 PNG 或 JPEG，选择性去除水印
- 可以分享至 Pixiv、Twitter、ArtStation 等平台
- 记得标注 AI 生成标签（例如 #AIart #StableDiffusion）

## 🛠️ 附加工具推荐（进阶）
| 名称                                | 作用             |
| --------------------------------- | -------------- |
| **Civitai**                       | 查找模型、LoRA、风格预览 |
| **Tiled Diffusion**               | 生成超高分辨率图       |
| **Dynamic Prompts**               | 自动组合提示词进行批量创作  |
| **Clip Studio Paint / Photoshop** | 后期精修           |

## ✅ 示例工作流组合（推荐）
| 场景      | 工具组合                                         | 说明        |
| ------- | -------------------------------------------- | --------- |
| 快速生成头像  | Mage.space + Anything v5                     | 新手最容易上手   |
| 姿势+角色设定 | Automatic1111 + ControlNet + MeinaMix + Lora | 中高级创作者    |
| 分镜头漫画场景 | ComfyUI + Regional Prompt                    | 需要结构性构图时用 |

--------------------

## 适合动漫插画的 AI 模型推荐
| 模型/工具名                                 | 类型   | 特点                                 | 使用平台      |
| -------------------------------------- | ---- | ---------------------------------- | --------- |
| **Stable Diffusion + Anime 模型**        | 开源模型 | 基于Stable Diffusion微调，支持高质量动漫风格     | 本地 / Web  |
| **Anything v3 / v4 / v5**              | 模型权重 | 最经典的二次元SD模型，画风细腻，泛用性强              | 支持SD平台    |
| **Counterfeit / PastelMix / MeinaMix** | 模型权重 | 各有不同风格，适合插画创作、女性角色、美型风格            | 本地 / Web  |
| **ComfyUI / Automatic1111 WebUI**      | 工具框架 | 提供图形界面调用SD模型，支持各种控制功能（如ControlNet） | 本地部署      |
| **NovelAI Diffusion**                  | 商业服务 | 高质量二次元画风，界面简洁，无需本地部署               | 官方网站      |
| **Mage.space / Civitai**               | 平台   | 在线生成，或分享模型权重、Lora、Prompt范例         | Web       |
| **Midjourney（V5/6）**                   | 商业模型 | 虽非专门为动漫设计，但用特定提示词也能生成不错的二次元风插画     | Discord平台 |

## 使用方式推荐
### 🔸 1. 本地部署（高自由度，适合持续创作）
- 安装 [Stable Diffusion + WebUI（如Automatic1111）]
- 下载模型权重（如Anything v5 / MeinaMix）
- 可搭配 ControlNet、Lora 微调模型使用，控制姿势、构图、背景等
- 推荐网站：
  - Civitai.com：模型、Lora下载中心
  - Hugging Face：部分动漫模型托管地

### 🔸 2. 在线平台（免安装，快速生成）
- Mage.space（https://www.mage.space/）：选择Anime模型，输入提示词即可生成
- PlaygroundAI（https://playgroundai.com/）：支持SD和Anime模型
- Leonardo AI：注册即可使用，画风精致，适合设计师
- NovelAI（https://novelai.net/）：适合生成日式风格女性角色插画

## 创作建议和技巧
### ✍️ 提示词撰写（Prompt Engineering）
动漫插画生成效果很大程度取决于提示词的质量。以下是一些例子：
```
masterpiece, best quality, 1girl, long hair, blue eyes, looking at viewer, school uniform, cherry blossom, anime style, soft lighting, detailed background

```

### 🌟 推荐关键词
| 类别   | 关键词示例                                                       |
| ---- | ----------------------------------------------------------- |
| 风格   | anime style, illustration, pastel color, cinematic lighting |
| 构图   | full body, close-up, dynamic pose, from below               |
| 背景   | classroom, sakura, fantasy forest, starry sky               |
| 人物设定 | 1girl, long hair, twintails, armor, school uniform          |

### 🎨 Lora 模型微调（可选）
- 利用 Lora 加载“风格/角色/服装”等微调模型，提高创作自由度
- 在 WebUI 里载入 .safetensors 的 Lora 文件并使用 〈lora:xxx:weight〉格式添加

## ✅ 如果你是初学者，推荐路线如下：
- 先在 Mage.space 或 PlaygroundAI 上试试写提示词生成动漫图
- 再考虑安装本地 Stable Diffusion + Automatic1111
- 下载如 Anything v5 等 Anime 模型，用 ControlNet 控制人物姿势
- 学习提示词、Lora 调用技巧，提高创作的精准度
