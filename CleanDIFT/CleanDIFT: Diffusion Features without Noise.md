# CleanDIFT: Diffusion Features without Noise
### [Arxiv](https://arxiv.org/pdf/2412.03439)   [Bili](https://www.bilibili.com/video/BV1b541197HX?buvid=XX68ACF6051B519928AE6B6F405AD2D005DC3&from_spmid=playlist.playlist-detail.0.0&is_story_h5=false&mid=DrRwFpk%2BbcbkpzyW8K9UaQ%3D%3D&plat_id=116&share_from=ugc&share_medium=android&share_plat=android&share_session_id=7ec5d6cc-eb1b-40d0-ae1d-b8d96e6a2a6f&share_source=WEIXIN&share_tag=s_i&spmid=united.player-video-detail.0.0&timestamp=1750868703&unique_k=9RxcO7z&up_id=373596439&share_source=weixin)


## Abstract
- **Internal features** from large-scale pre-trained diffusion models have recently been established as **powerful semantic descriptors** for **a wide range of downstream tasks**.
- Works that use these features generally need to **add noise to images before passing them through the model to obtain the semantic features**, as the models do not offer the most useful features when given images with little to no noise.
- We show that **this noise has a critical impact on the usefulness of these features that cannot be remedied by ensembling with different random noises（噪声的添加为特征质量带来了不确定因素 潜在特征的质量对添加的噪声敏感）**.
- We address this issue by **introducing a lightweight, unsupervised fine-tuning method that enables diffusion backbones to provide highquality, noise-free semantic features**.
- We show that these features readily outperform previous diffusion features by a wide margin in a wide variety of extraction setups and downstream tasks, offering better performance than even ensemble-based methods at a fraction of the cost.
------------
## 摘要
- 📝 研究背景：大规模预训练扩散模型中的 内部特征（intermediate features）近年来被证明可作为强大的语义描述符，广泛用于下游任务（如图像分类、检索、分割等）。
- 🚨 问题发现：当前提取这些特征的方法通常需要人为给输入图像加噪声，因为扩散模型在处理“干净图像”时，输出的特征不具备良好的语义表达力。**但是噪声的添加为特征的质量引入了不确定性，中间特征对所添加的噪声敏感**，即使使用多个随机噪声进行 ensemble（集成），也无法有效解决特征质量问题。
- 💡 作者提出的方法：提出一种 轻量级的、无监督的 fine-tuning 方法，对 diffusion backbone 进行微调，使其在 无噪声输入下也能输出高质量的语义特征。
- 📈 效果与贡献：该方法在多种特征提取场景和下游任务中，显著优于原始的扩散特征（即带噪声提取的特征）。并且不依赖 ensemble 技术，而在效果上优于 ensemble 方法，且计算成本更低。
-------------
## 技术术语与理论解释
### 🌀 Diffusion Models（扩散模型）
- 扩散模型是一类生成模型，其训练流程包括：
  - 正向过程（Forward Process）：逐步往图像中添加高斯噪声
  - 反向过程（Reverse Process）：通过神经网络学习如何逐步去噪，还原原始图像
  - DDPM：Denoising Diffusion Probabilistic Models (Ho et al., 2020)

### 🎯 Intermediate Semantic Features（中间语义特征）
- 在扩散模型中，特征通常指 U-Net 网络结构中不同层的中间输出。
- 研究发现，这些中间特征能**有效编码（体现）输入图像的高层语义信息**，与 CLIP 等模型中的“图像嵌入”有类似用途。

### ❌ 为什么需要加噪声才能提取语义特征？
- 扩散模型的 U-Net 通常是为“噪声输入”设计的，因此在输入图像几乎没有噪声时，其处理方式**与训练阶段存在分布偏差（distribution shift）**，导致：
  - 特征表达弱
  - 模型激活不充分
- 加噪声是人为“模拟训练时的输入”，**但这会引入不稳定性（对随机噪声敏感）**

### 🛠️ 作者提出的 Unsupervised Fine-tuning 方法
- 无需标注数据，通过调整扩散模型参数，使其在“无噪声输入”下也能提取高质量语义特征。
- 特点：
  - 轻量：微调参数量较小
  - 无监督：无需下游任务的标签
  - 效率高：去除 ensemble 带来的冗余计算
------------

## Introduction
- Learning meaningful visual representations that capture a vast amount of world knowledge remains a key problem in the field of computer vision. **Diffusion models can be trained at scale in a self-supervised manner** and have rapidly advanced the state of the art in image [12, 41, 51] and video generation [13, 25, 58], making them a good candidate to learn visual representations.
- Many early works have already achieved impressive results using internal features from large-scale pretrained diffusion models for a wide variety of tasks, such as semantic correspondence detection [62, 66, 67], semantic segmentation [2, 39, 63], panoptic segmentation [64], object detection [8], and classification [31].
- However, <mark>the optimal approach to extract this world knowledge from a diffusion model remains uncertain</mark>.
---------
- To understand why that is the case, we take a look at how diffusion models are trained: <mark>a varying amount of noise is added to a clean input image (forward process) and the model is tasked to remove the noise from the image (backward process)</mark>.
- The amount of added noise is dependent on **the diffusion timestep**. As a result, the model learns to operate on noisy images and also **becomes dependent(依赖) on the noise timestep** as different noise levels require the model to perform different tasks [1, 4].
- Since noisy images inherently contain less information than clean images (cf. Figure 2), we hypothesize that **this harms the internal feature representation** of diffusion models [9] and, thus, the extractable world knowledge.
- Furthermore, the timestep acts as a hyperparameter that influences the internal feature representation and needs to be picked independently for every downstream application (cf. Figure 14).
-----------
## 导引第一节
### 🧠 一句话总结
- 虽然大规模预训练的扩散模型在图像和视频生成上取得突破，并能用于多种视觉任务，但如何最有效地提取其内部语义特征（世界知识）仍是未解问题，其根源在于：模型是在有噪声输入上训练的，导致其内部特征质量与噪声水平密切相关，进而影响特征可用性和稳定性。

### 🧪 作者的假设（hypothesis）
- 因为噪声图像本身信息量少于原始图像，模型学到的内部表示可能也受限
- 加噪程度（timestep）变成了一个高敏感的超参数，因为不同任务需要选择不同 timestep 来添加噪声 以让扩散模型提取特征 → 可迁移性差，调参成本高
- **因此需要解决的问题如下：噪声损害语义表示 + timestep超参数敏感**

###  📊 小结图示
```
扩散模型训练输入 = 原始图像 + （对于不同任务）设定不同的timestep添加噪声
        ↓
故模型特征依赖于噪声程度和timestep
        ↓
🚨 待解决问题:噪声损害语义表示 + timestep超参数敏感
        ↓
导致下游任务提取特征时不稳定 & 难以泛化
```
------------
- We propose a **novel feature extraction method** that <mark>(1) eliminates the need to destroy information by adding noise to the input; and (2) produces **timestep-independent** generic diffusion features useful for a wide range of down-stream tasks, alleviating the need to tune a noising timestep per down-stream task</mark>.
- We show how to **adapt an off-the-shelf large-scale pre-trained diffusion backbone to provide these features at minimal cost** (approximately 30 minutes of finetuning on a single A100 GPU) and demonstrate improved performance across a wide range of downstream tasks.
- We achieve this by viewing a diffusion model as a family of T feature extractors that operate on images with different noise levels and provide features with different characteristics. We consolidate all T feature extraction functions in ourfeature extractor by aligning their internal representations.
- Specifically, we initialize our feature extractor as a trainable copy of the diffusion model; fine-tune it with clean images and no timestep input; and align its features with all T timedependent feature extractors of the diffusion model.
- We evaluate our improved features across a wide variety of downstream tasks, such as semantic correspondence matching, monocular depth estimation, semantic segmentation, and classification, and find that they consistently improve upon approaches based on standard diffusion features. These improvements are most evident for dense visual tasks such as semantic correspondence matching, where our features show substantial performance gains across a wide variety of setups [62, 66, 67] and set a new state-of-the-art for unsupervised semantic correspondence matching.
- Additionally, our proposed method eliminates the need for noise or timestep ensembling [62], offering substantial speed gains (e.g., 8ˆ over DIFT [62]), on top of improved quality. Our method is generic and integrates easily with established methods, such as fusing diffusion and DINOv2 features [66, 67].
------------------
## 导引第二节
### 🧠 一句话总结
- 作者提出了一种**无需加噪、timestep无关**的**基于扩散模型的特征提取方法**，通过**轻量微调**即可获得高质量、通用、速度更快的视觉表示，广泛提升下游任务表现，尤其在语义匹配等密集任务中达到SOTA水平。

### 📌 核心创新点
| 项目          | 内容                                                        |
| ----------- | --------------------------------------------------------- |
| 🎯 **目标**   | 从扩散模型中提取**高质量、通用、不依赖噪声或timestep**的语义特征                    |
| 🧪 **问题**   | 传统方法需加噪 + 调整 timestep，且特征对 timestep 敏感                    |
| 💡 **方法**   | 提出一种新的特征提取方法，使模型能在**无噪声+无timestep输入**下提取特征                |
| 🔧 **实现策略** | 视扩散模型为由多个 timestep 特征提取器组成的集合，通过**特征对齐**将这些特征整合进一个单一模型    |
| ⚙️ **训练步骤** | 微调一个可训练副本，使用干净图像、无 timestep 输入，将其特征对齐到原模型不同 timestep 的特征上 |
| 🧩 **适配性**  | 方法轻量（30分钟微调），兼容已有架构（如 DINOv2），易集成                         |
| 🚀 **优势**   | 无需加噪或 ensemble、推理速度快（比 DIFT 快约 8 倍）、表现更好                  |
| 🧠 **效果验证** | 多个任务验证（语义匹配、深度估计、分割、分类），**在密集视觉任务上显著领先**                  |

### 🔍 关键技术点讲解
#### 1. ✅ 无需加噪、无timestep输入
- 原始扩散模型是“给定噪声程度 + 图像”→ 提取特征(给图像添加噪声的过程中需要设置timestep)
- 作者提出：直接用干净图像+不输入 timestep，也能提取语义特征
- 通过**特征对齐**实现这一能力

#### 2. 🧠 将扩散模型视作一个“T步特征提取器集合”
- 原始模型对每个 timestep 都学会“不同的特征提取任务”
- 作者将所有 timestep 的特征输出视作“一个集合”，然后：
  **训练一个基于扩散模型的统一模型，使得这个模型输出的特征能够 对齐所有这些 timestep 的特征输出**

#### 3. ⚙️ 训练方法细节
- 初始化为 diffusion backbone 的副本（可训练）
- 用干净图像训练，不加噪声、不输入 timestep
- 训练目标：使它输出的特征尽可能与原始扩散模型所有 timestep 的特征对齐（可能用 MSE 或 contrastive loss）

#### 4. 📈 实验结果与性能提升
- 下游任务包括：
  - 语义匹配（semantic correspondence matching）
  - 单目深度估计
  - 语义分割、图像分类等
- 在所有任务上，均优于原始扩散特征
- 密集任务（如语义匹配）效果提升最显著，达到新的 SOTA 水平

#### 5. ⚡ 无需ensemble，速度快 8倍+
- 不再需要在不同 timestep 或不同噪声上 ensemble（例如 DIFT 做的）
- 推理效率大大提升（例如：比 DIFT 快约 8 倍）
- 同时性能更好

### ✅ 三、价值与适用范围
| 项目       | 说明                                      |
| -------- | --------------------------------------- |
| 🧩 适合任务  | 所有视觉下游任务，特别是**密集预测类任务**                 |
| 🛠️ 适配能力 | 兼容预训练 diffusion backbone，可与 DINOv2 特征融合 |
| 🚀 推理速度  | 单一前向，无需噪声/多timestep，大幅提速                |
| 🎯 训练成本  | 非常轻量（单张A100 GPU，30分钟）                   |

-----------------
Our main contributions are as follows:
1. We propose CleanDIFT, a finetuning approach for diffusion models that enables them to **operate on clean images** and makes the inherent world knowledge of these models more accessible.
2. We show how to **consolidate information from all diffusion timesteps into a single feature prediction, removing the need for task-specific timestep tuning**.
3. We demonstrate significant performance gains of our diffusion feature extraction technique across a wide range of down-stream tasks, notably surpassing the current state of the art in zero-shot unsupervised semantic correspondence detection. We further demonstrate the generality of our enhanced features by showing that these performance gains transfer to advanced methods that fuse diffusion features or operate in a supervised setting.
4. Our proposed approach is significantly more efficient than previous methods that tried to address this problem by noise ensembling or supervised training.
-----------------------
| 编号    | 贡献内容                                              | 说明                                  |
| ----- | ------------------------------------------------- | ----------------------------------- |
| **1** | 提出 CleanDIFT 方法，使扩散模型能直接作用于**干净图像**               | 打破“必须加噪才能提取有用特征”的限制，使模型内在世界知识更易访问   |
| **2** | 将所有 timestep 的信息**整合为单一特征输出**，无需任务特定的 timestep 调参 | 消除对 timestep 的敏感性，提高特征的**通用性与稳定性**  |
| **3** | 在多个下游任务中取得**显著性能提升**，特别是在**零样本无监督语义匹配任务中超越SOTA**  | 同时验证所提特征在**融合方法和有监督设置中具有迁移性和广泛适用性** |
| **4** | 相比于依赖 **noise ensemble 或有监督训练** 的方法，**更高效、更轻量**   | 微调过程短（如30分钟/A100），推理速度更快，部署成本更低     |

----------------------
## Related Work
### Self-Supervised Representation Learning
- Features from large, pre-trained foundation models have been shown to yield competitive performance to supervised models for a variety of downstream tasks, both in zero-shot and
fine-tuning settings [20, 43, 48]. These foundation models are trained on different pre-text tasks like inpainting [20], predicting transformations [19], patch reordering [38, 42],
and discriminative tasks [6, 43]. 
- DINOv2 [43] uses a discriminative objective combined with self-distillation to learn general-purpose visual features that have proven useful for a variety of downstream tasks [10]. CLIP [48] learns such features by employing a contrastive objective on text-image pairs. Masked Autoencoders [20] (MAEs) are trained to reconstruct masked out patches of the input, also resulting in general-purpose visual features.

### Diffusion Models as Self-Supervised Learners 
- Diffusion models [24, 60, 61] are generative models that have defined the state-of-the-art in image generation [12, 14, 41,46, 51, 53], video generation [13, 47], and audio generation [15, 30] in recent years. Their primary purpose is to generate high-quality samples (images, videos, etc.). However, generation can also be interpreted as a pretext task for learning expressive features, since the model has to build up comprehensive world knowledge in order to generate
plausible samples [9, 17, 27, 32, 62].
- Features from diffusion models (typically referred to as diffusion features) are obtained by passing a noised image through the diffusion model and extracting intermediate feature representations.
- They have been shown to be useful for a variety of tasks such as finding semantic correspondences [21, 35, 62], semantic and panoptic segmentation [2, 39, 63, 64], classification [31], and object detection [8]. For semantic correspondence matching, features are leveraged to identify semantically matching regions across images. Existing approaches utilize diffusion features either in a zero-shot setting [21, 62] or fine-tune them for the semantic correspondence task [33, 35, 67]. Some zero-shot approaches do not fine-tune on semantic correspondence but still require tuning a prompt to activate attention maps at
the query location of the correspondence [21].
- In contrast, our approach aims to provide universal features usable for various down-stream tasks in a true zero-shot manner. Further, diffusion features have been shown to complement features from other self-supervised learning methods such as DINOv2 [18, 43, 66]. DINOv2 features provide sparse but accurate semantic information, while Diffusion features provide dense spatial information albeit with sometimes inaccurate semantics. State-of-the-art approaches for semantic correspondence detection exploit this and fuse features [66, 67]. Compared to DINOv2 features, diffusion features yield smoother, spatially more coherent correspondences [66].

### Distillation for Diffusion Models
- Knowledge Distillation [22] is a technique used to distill knowledge from a teacher model into a student model.
- In the context of diffusion models, this is typically applied either to reduce the required denoising steps [54] or to distill a classifier-free guided [23] model into one without CFG [36]. While our approach is inspired by distillation, we consolidate features from T different models, with T being the number of discrete diffusion timesteps, because the features are different at every timestep.
----------------------
## 相关工作
### 📋 自监督表示学习、扩散模型特征与蒸馏技术总结表
| 类别                                                        | 内容总结                                                                                                 | 代表方法与特点                                                                                             |
| --------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| 🎯 **自监督视觉特征学习（Self-Supervised Representation Learning）** | 利用无监督的**预训练任务**（pre-text task）学习通用视觉特征，可用于 zero-shot 或 fine-tuning 的下游任务                             | - **DINOv2**：判别式目标 + 自蒸馏，特征稀疏但语义精确<br>- **CLIP**：文本-图像对上的对比学习，跨模态<br>- **MAE**：遮挡预测（patch 重建），结构感知强 |
| 🌀 **扩散模型作为自监督学习器（Diffusion Models as SSL Learners）**     | 扩散模型原为生成任务设计，但其生成任务本质上需要丰富的世界知识 → 可以看作一种隐式的“预训练任务”，因此其\*\*中间层特征（diffusion features）\*\*具有通用性         | - 特征通过 **输入加噪图像，提取中间层表示**<br>- 广泛用于：语义匹配、图像分割、目标检测、分类等<br>- 在语义匹配中可提供**空间连贯性强但语义偏差**的稠密特征           |
| 🔀 **与其他自监督特征的互补性**                                       | Diffusion 特征 + DINOv2 特征常融合使用：<br>Diffusion → 稠密空间结构好<br>DINOv2 → 稀疏语义信息准                            | - SOTA 语义匹配方法 \[66, 67] 就融合了两者<br>- Diffusion 特征带来**平滑且连贯的区域对齐能力**                                  |
| 🔧 **扩散模型中的蒸馏（Distillation for Diffusion Models）**        | 蒸馏常用于：<br>① 减少去噪步骤数量<br>② 移除 classifier-free guidance (CFG) 机制<br>③（本文）将 T 个 timestep 特征整合成一个统一特征提取器 | - 传统蒸馏：用于加速推理或去除控制器<br>- 本文蒸馏：**将不同 timestep 的模型特征整合成统一模型（跨时刻特征对齐）**                                |

### 📌 关键词对比简析
| 特征        | Diffusion Features    | DINOv2 Features          |
| --------- | --------------------- | ------------------------ |
| **稠密性**   | ✅ 空间结构稠密              | ❌ 稀疏关键点语义                |
| **语义准确性** | ⛔️ 语义可能不精确            | ✅ 精准语义对齐                 |
| **可迁移性**  | 可用于 dense task，多任务泛化好 | 较强但在 dense task 中有限      |
| **组合方式**  | 常用于 **特征融合**          | 与 diffusion feature 互补使用 |



