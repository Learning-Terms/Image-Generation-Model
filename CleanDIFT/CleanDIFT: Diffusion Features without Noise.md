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
