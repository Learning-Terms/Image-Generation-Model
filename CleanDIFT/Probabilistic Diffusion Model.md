# Probabilistic Diffusion Model

#### [Bilibili](https://www.bilibili.com/video/BV1b541197HX/?buvid=XX68ACF6051B519928AE6B6F405AD2D005DC3&from_spmid=playlist.playlist-detail.0.0&is_story_h5=false&mid=DrRwFpk%2BbcbkpzyW8K9UaQ%3D%3D&plat_id=116&share_from=ugc&share_medium=android&share_plat=android&share_session_id=7ec5d6cc-eb1b-40d0-ae1d-b8d96e6a2a6f&share_source=WEIXIN&share_source=weixin&share_tag=s_i&spmid=united.player-video-detail.0.0&timestamp=1750868703&unique_k=9RxcO7z&up_id=373596439&vd_source=4546f071f0c029794e831dd59335ab6f)

#### [2020 Denoising Diffusion Probabilistic Models](https://proceedings.neurips.cc/paper/2020/file/4c5bcfec8584af0d967f1ab10179ca4b-Paper.pdf)

#### [2015 Deep Unsupervised Learning using Nonequilibrium Thermodynamics](http://proceedings.mlr.press/v37/sohl-dickstein15.pdf)

#### [参考笔记](https://raw.githubusercontent.com/Learning-Terms/Image-Generation-Model/refs/heads/main/CleanDIFT/1.webp)

#### [Diffusion Models Tutorial](https://colab.research.google.com/github/azad-academy/denoising-diffusion-model/blob/main/diffusion_model_demo.ipynb)

####[Latex常用数学符号](https://blog.csdn.net/LCCFlccf/article/details/89643585)

## 第1章 扩散模型是生成式AI模型的一种

#### 📌 一句话定义
生成式AI模型是一类通过**学习训练数据分布**，从而可以生成**新的、具有相似特征**数据的模型，包括**文本、图像、音频、代码**等多模态内容的自动生成。

#### 🧭 按时间顺序梳理的生成式AI模型发展图谱

| 年代            | 类别                       | 代表模型                                                | 简介                             | 状态                 |
| ------------- | ------------------------ | --------------------------------------------------- | ------------------------------ | ------------------ |
| **2013–2014** | 🔸**变分自编码器 VAE**         | VAE (2013), CVAE                                    | 概率建模 + 重构误差 + KL正则化，训练稳定       | ⚠️已过时（生成质量较低）      |
| **2014–2016** | 🔸**对抗生成网络 GAN**         | GAN (2014), DCGAN, WGAN, StyleGAN                   | 两网络博弈训练，生成图像质量高但不稳定            | ⚠️部分过时（不擅长文本，训练困难） |
| **2016–2018** | 🔸**自回归模型 AR**           | PixelRNN, PixelCNN, WaveNet                         | 逐点生成，每步依赖前序项，适用于语音/图像          | ❌过时（速度慢）           |
| **2017**      | 🔸**Transformer 引入**     | Transformer（原论文）                                    | 非生成模型，但为后续奠定架构基础               | ⭐️关键转折点            |
| **2018–2020** | 🔹**自回归 Transformer 系列** | GPT-1 (2018), GPT-2 (2019), GPT-3 (2020)            | 使用 Transformer 架构进行文本生成        | 🔥 主流方向            |
| **2019–2021** | 🔸**BERT风格 Masked生成**    | BERT, BART, T5                                      | Masked/denoising语言建模，适合理解和翻译任务 | ✅可配合生成，但非生成专用      |
| **2020–2022** | 🔹**扩散模型 Diffusion**     | DDPM, DDIM, Stable Diffusion                        | 通过逐步加噪/去噪生成图像，稳定且高质量           | 🔥 图像/音频生成最强之一     |
| **2022–2023** | 🔹**多模态生成模型**            | DALL·E 2, Imagen, Parti, Flamingo, Gato             | 文生图/图生文/多模态交互生成                | 🔥 快速发展中           |
| **2023–2024** | 🔹**指令微调 + 大模型**         | ChatGPT (GPT-3.5/4), Claude, Gemini, Mistral, LLaMA | 多任务泛化，支持对话、创作、代码等              | 🔥 主流方向            |
| **2023–2024** | 🔹**图像生成 SOTA 模型**       | Stable Diffusion XL, Midjourney v6, DALL·E 3        | 支持高分辨率图生图、风格迁移、控制生成            | 🔥 图像生成主力军         |
| **2024–**     | 🔸**多模态Agent/视频生成**      | Sora（OpenAI）、Make-A-Video、LLaVA、IDEFICS             | 实时交互 + 视频生成 + 图文理解与生成          | 🔥 最前沿方向           |
| **即将淘汰**      | 🔸**GAN系列**              | StyleGAN, CycleGAN, BigGAN                          | 难以扩展到文本与多模态，渐被扩散模型替代           | ❌ 基本退出前沿           |


#### 🧠 按类型划分的生成模型种类与代表

| 类型               | 原理              | 代表模型                           | 特点            | 状态       |         
| ---------------- | --------------- | ------------------------------ | ------------- | -------- | 
| **VAE类**         | 编码→隐变量→解码       | VAE, β-VAE, CVAE               | 简单稳定，生成质量较差   | ⚠️边缘化    |         
| **GAN类**         | 判别器-生成器博弈       | GAN, StyleGAN, CycleGAN        | 图像质量高，但训练难    | ❌逐渐过时    |         
| **自回归AR类**       | 按序建模  | PixelCNN, GPT                   |  理论清晰，速度慢 | ✅改进后仍在用 |
| **扩散模型类**        | 加噪 → 去噪反向生成     | DDPM, Stable Diffusion, Imagen | 稳定可控，图像主流     | 🔥强势发展中  |         
| **Transformer类** | 编码/解码全局注意力机制    | GPT-3/4, LLaMA, Claude         | 文本/代码生成主力     | 🔥最核心架构  |         
| **多模态生成类**       | 图+文、视频+文本联合     | DALL·E, Flamingo, Sora         | 实现视觉-语言理解与生成  | 🔥新顶会热点  |         
| **生成Agent类**     | 大模型 + 工具 + 多轮交互 | OpenAgents, LangGraph, ReAct   | AI助手/任务执行系统   | 🔥未来趋势   |         

#### 📌 热门 vs 过时模型总结

✅ 当前最🔥热门方向：

- 文本生成：GPT-4、Claude 3、Mistral、Gemini
- 图像生成：Stable Diffusion XL、Midjourney、DALL·E 3、ControlNet
- 多模态生成：LLaVA、IDEFICS、Video-to-Video、Sora（视频生成）
- 指令学习与微调：DPO、LoRA、RLHF
- 生成式Agent与系统整合：LangChain、OpenAgents、Toolformer

❌ 逐渐过时方向：

- PixelRNN/PixelCNN（过慢）
- 原始GAN结构（训练不稳定）
- 传统VAE（生成质量不足）
- 基于 RNN/LSTM 的生成（已被Transformer取代）

## 第2章 条件概率公式和高斯分布的KL散度

条件概率的一般形式 基于马尔可夫假设的条件概率 高斯分布的KL散度公式 重参数技巧
参看**参考笔记**

- 马尔科夫假设：当前时刻的概率分布只与上一时刻有关
- KL 散度（Kullback-Leibler Divergence）： 衡量**两个概率分布之间差异**的一种方法，特别是衡量一个近似分布 
$𝑞(𝑥)$ 与一个真实分布 $𝑝(𝑥)$的**距离**

$$
D_{KL}(q(x) | p(x)) = \int q(x) \log \frac{q(x)}{p(x)} \, dx
$$

  - 🚩 特点： KL散度越小，两个分布越相似
  - KL散度不是对称的 $$D_{KL}(q(x) | p(x))$$ 不等于 $$D_{KL}(p(x) | q(x))$$
  - 在 VAE 中，用 KL 散度来限制编码器输出的分布不要偏离标准正态分布 $𝑁(0,𝐼)$

- 重参数技巧：目的是为了梯度可以传播 采样出来的值与均值和方差之间的梯度是可导的

## 第3章 VAE回顾
参考笔记

## 第4章 Diffusion Model

### 扩散模型基本概念与图示

**参考Figure1**

- 图像本身就反映了图像的像素像素值分布 从这个分布中抽样的过程就是从图像中均等的随机抽取一个像素点
- 扩散模型的工作涉及两个过程：扩散过程和逆扩散过程
  - 扩散过程：向原始分布（目标分布）中不断添加高斯噪声 最终使得分布变为一个纯噪声的各向独立的高斯分布
  - 逆扩散过程：生成过程，也就是从纯噪声分布中逆向推理出目标分布
- 扩散模型的推理过程：只利用逆扩散过程生成近似目标分布（生成图像）
- 扩散模型的学习目标：学习到逆扩散过程的公式/方法/原理 从而预测出接近目标分布的分布

### 扩散过程 Diffusion Process

- 扩散过程，也就是添加噪声的过程是**不含可学习参数的**，所添加杂声的均值和方差都是确定的，该过程是一个马尔可夫过程
- 随着时间步t的不断增大，最终目标分布会变成一个各向独立的高斯分布
- 任意时刻的分布可以直接计算出来而不需要迭代 公式见参考笔记
- βt（噪声因子）的设置：在分布越来越接近纯噪声分布的时候 让噪声因子越来越大

### 逆扩散过程 Reverse Process

- 逆扩散过程的目的是从纯噪声高斯分布中恢复出原始分布
- 逆扩散过程是一个马尔科夫链过程

### 后验的扩散条件概率分布是可以用一个公式表达的

- 也就是说给定 $x_0$ 和 $x_1$ 我们可以计算出 $x_{t-1}$
- 公式见参考笔记 核心是贝叶斯公式 忽略系数部分而只保留指数部分

### 贝叶斯公式和其在扩散模型中的应用
#### 贝叶斯公式（条件概率公式）是什么？（Bayes' Theorem）
📌 数学表达：

$$
P(A \mid B) = \frac{P(B \mid A) \cdot P(A)}{P(B)}
$$
 
中文解释：给定事件𝐵已经发生，事件𝐴发生的概率是多少？ 
- P(A∣B)：后验概率，在观察到𝐵的条件下𝐴发生的概率
- P(B∣A)：似然，在𝐴发生的前提下观察到𝐵的概率
- P(A)：先验概率，没看到任何数据前对𝐴发生的信念
- P(B)：边际概率，事件𝐵发生的总概率

#### 贝叶斯公式在生成式AI模型中的作用
📌 通用作用：建立 **“隐变量 → 观测数据”的推理路径**

生成模型中，我们往往假设：
- 存在一个潜在变量 𝑧（隐藏语义、图像风格等高层次特征）
- 数据 𝑥是从这个潜在变量“生成”的

学习目标：我们希望**通过观察到的数据𝑥，去推断生成它的潜变量𝑧，并最终重建出观察到的输入数据**，也就是求解：

$$
p(x_{t-1} \mid x_t) = \frac{p(x_t \mid x_{t-1}) \cdot p(x_{t-1})}{p(x_t)}
$$

——这正是贝叶斯公式的用武之地！

- P(z)：先验（通常设为正态分布）
- P(x∣z)：生成器（解码器）
- P(z∣x)：推断器（编码器）

#### 贝叶斯公式在扩散模型中的应用

- 逆扩散过程中的每一步都依赖贝叶斯推理：

- 但由于 $p(x_{t−1})$ 是未知的（其他都在前向过程中已知），无法直接通过贝叶斯公式计算，因此我们得用**神经网络去学习一个近似的反向分布**，即：

$p_θ(x_{t−1}∣x_t)$ ≈some parametric Gaussian predicted by a neural network

- 这就是扩散模型的意义 逆扩散过程的训练目标是训练目标是**逼近每一个时间步的后验概率 $p(x_{t−1}∣x_t)$** ，所以本质是一个连续的贝叶斯推断过程！

## 第5章 目标数据分布的似然函数
