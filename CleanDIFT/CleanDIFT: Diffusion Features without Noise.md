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
扩散模型训练输入 = 原始图像 + （对于不同任务）不同的timestep添加噪声
        ↓
故模型特征依赖于噪声程度和timestep
        ↓
🚨 待解决问题:噪声损害语义表示 + timestep超参数敏感
        ↓
导致下游任务提取特征时不稳定 & 难以泛化
```
------------
- We propose a novel feature extraction method that (1) eliminates the need to destroy information by adding noise to the input; and (2) produces timestep-independent generic diffusion features useful for a wide range of down-stream
tasks, alleviating the need to tune a noising timestep per down-stream task.
- We show how to adapt an off-the-shelf large-scale pre-trained diffusion backbone to provide these features at minimal cost (approximately 30 minutes of finetuning on a single A100 GPU) and demonstrate improved performance across a wide range of downstream tasks.
- We achieve this by viewing a diffusion model as a family of T feature extractors that operate on images with different noise levels and provide features with different characteristics. We consolidate all T feature extraction functions in ourfeature extractor by aligning their internal representations.
- Specifically, we initialize our feature extractor as a trainable copy of the diffusion model; fine-tune it with clean images and no timestep input; and align its features with all T timedependent feature extractors of the diffusion model.
- We evaluate our improved features across a wide variety of downstream tasks, such as semantic correspondence matching, monocular depth estimation, semantic segmentation, and classification, and find that they consistently improve upon approaches based on standard diffusion features. These improvements are most evident for dense visual tasks such as semantic correspondence matching, where our features show substantial performance gains across a wide variety of setups [62, 66, 67] and set a new state-of-the-art for unsupervised semantic correspondence matching.
- Additionally, our proposed method eliminates the need for noise or timestep ensembling [62], offering substantial speed gains (e.g., 8ˆ over DIFT [62]), on top of improved quality. Our method is generic and integrates easily with established methods, such as fusing diffusion and DINOv2 features [66, 67].


