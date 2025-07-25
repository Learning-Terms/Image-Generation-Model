# Probabilistic Diffusion Model

#### [Bilibili](https://www.bilibili.com/video/BV1b541197HX/?buvid=XX68ACF6051B519928AE6B6F405AD2D005DC3&from_spmid=playlist.playlist-detail.0.0&is_story_h5=false&mid=DrRwFpk%2BbcbkpzyW8K9UaQ%3D%3D&plat_id=116&share_from=ugc&share_medium=android&share_plat=android&share_session_id=7ec5d6cc-eb1b-40d0-ae1d-b8d96e6a2a6f&share_source=WEIXIN&share_source=weixin&share_tag=s_i&spmid=united.player-video-detail.0.0&timestamp=1750868703&unique_k=9RxcO7z&up_id=373596439&vd_source=4546f071f0c029794e831dd59335ab6f)

#### [2020 Denoising Diffusion Probabilistic Models](https://proceedings.neurips.cc/paper/2020/file/4c5bcfec8584af0d967f1ab10179ca4b-Paper.pdf)

#### [2015 Deep Unsupervised Learning using Nonequilibrium Thermodynamics](http://proceedings.mlr.press/v37/sohl-dickstein15.pdf)

#### [参考笔记](https://raw.githubusercontent.com/Learning-Terms/Image-Generation-Model/refs/heads/main/CleanDIFT/1.webp)

#### [Diffusion Models Tutorial](https://colab.research.google.com/github/azad-academy/denoising-diffusion-model/blob/main/diffusion_model_demo.ipynb)

#### [Latex常用数学符号](https://blog.csdn.net/LCCFlccf/article/details/89643585)

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
- $q(x_{t-1})$ 分布的均值和方差 见参考笔记
- 公式推导核心是贝叶斯公式 马尔可夫过程 忽略系数部分而只保留指数部分（正比）

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

## 第5章 目标数据分布的似然函数-确定优化的目标函数

### 统计学中似然的定义
设：
- 𝑥：观测到的样本（已确定）
- 𝜃：待估计的参数
- f(x∣θ)：样本在参数𝜃下的概率密度函数（或概率质量函数）

那么，似然函数被定义为：

$$
L(θ∣x)=f(x∣θ)
$$

- 也就是说：似然函数是 **在固定观测数据𝑥的前提下，将样本在参数𝜃下的概率密度函数 视作参数𝜃的函数**。

#### ❗ 似然与概率的区别
| 项目                          | 参数 vs 数据                   | 谁是变量？        | 目的        |
| --------------------------- | -------------------------- | ------------ | --------- |
| **概率** $P(X=x \mid \theta)$ | 固定参数 $\theta$，看数据 $x$ 的可能性 | $x$ 是变量      | 给定参数下预测数据 |
| **似然** $L(\theta \mid x)$   | 固定观测 $x$，看哪个参数更可能生成它       | $\theta$ 是变量 | 给定数据下估计参数 |

#### 概率：已知参数求数据出现的概率；似然：已知数据估参数的可能性。
#### ✅ 似然在统计中的核心作用：估计参数
- 最常见的方法是最大似然估计（MLE）：
  - 即找到最可能生成观测数据 𝑥 的模型参数 𝜃
  
![MLE](https://quicklatex.com/cache3/c7/ql_4d1f56167cc08cd1bef47c7623fd2ac7_l3.png)

#### 🔁在生成式AI中的延伸意义
虽然“似然”是统计学术语，但在**生成模型训练**中，使用的也是相同逻辑：
- 假设我们从真实数据中观察到样本𝑥
- 模型 $p_θ(x)$ 就是一个似然函数
- 最大化这个似然（或其对数）即是训练目标

#### ✅ 总结表格
| 项目                    | 概念        | 固定谁？        | 变量是谁？       | 作用          |
| --------------------- | --------- | ----------- | ----------- | ----------- |
| 概率 $P(x \mid \theta)$ | 给定参数生成数据  | 参数 $\theta$ | 数据 $x$      | 预测数据        |
| 似然 $L(\theta \mid x)$ | 给定数据估计参数  | 数据 $x$      | 参数 $\theta$ | 参数估计（如 MLE） |
| 应用（AI）                | 最大化似然 以训练模型 | 训练数据已知        | 模型参数        | 学习生成能力   |


### 什么是“目标数据分布的似然函数”？
 - 目标数据分布的似然函数，是指生成模型在给定模型参数下，对**真实样本数据生成的“可能性”的度量**。

#### ✅ 更正式地说：
设：
- 𝑥：一个真实的数据样本（如图像、文本,也称为目标数据分布）
- 𝜃：生成模型的参数
- $p_θ(x)$ 模型在给定参数𝜃下生成𝑥的概率

那么有：

$$
目标数据分布的似然函数= p_θ(x)
$$

- 🚩“目标数据分布”指的是：我们训练所用的样本𝑥所服从的真实分布 $𝑝_{data}(x)$
- 🚩“似然(函数)”是：我们的生成模型在尝试逼近这个分布时，对生成样本𝑥的评估概率值 $p_θ(x)$
- 如果模型生成图像的能力很强，生成的图像非常像真实图像，那么对真实样本𝑥来说, $p_θ(x)$ 会更高 → **似然值更大**。
- 我们训练模型的目标就是：**最大化真实数据的似然函数 $p_θ(x)$** —— 也就是让生成模型更好地逼近目标数据分布。

### 似然函数在扩散模型训练中的作用
#### 📌 扩散模型训练的核心目标：最大化对真实样本 $𝑥_0$ 的似然
$$
\max_\theta \log p_\theta(x_0)
$$

但是：直接优化这个式子非常困难（因为$p_θ(x)$ 是通过马尔可夫链逐步采样得到的，求它很复杂），因此引入 **变分下界ELBO** 来近似优化这个似然。

#### ✅ DDPM 中的训练目标（来自原始论文）

![ELBO](https://quicklatex.com/cache3/ad/ql_bd9e5de2f63f0110d2de3c0103ae1cad_l3.png)

这一项就是似然的**下界（ELBO）**，可分解为多个部分，包括：
- 重构误差
- KL 散度
- 噪声预测误差（具体取决于训练方式）

#### ✅ 进一步简化后常用的训练目标

![Simple](https://quicklatex.com/cache3/ef/ql_708acae64b9fb5cda2b489dd58bdbcef_l3.png)

大多数扩散模型的训练目标，最终会变成一个噪声预测损失（等价于最大化似然）：

- 这里的 𝜖 是我们加到样本上的真实噪声
- $𝜖_𝜃$：是模型预测出的噪声
- 优化这个损失，即是在间接最大化对 $x_0$ 的似然函数！二者是等价的 经过了三步推导

## 第6章 扩散模型算法伪代码与Python实现
### 扩散模型的训练和推理
- 扩散模型在训练时**学习逐步去噪的过程**，在推理时**从纯噪声开始，逐步还原出干净数据**，达到“从噪声生成图像”的目的。
#### 训练过程（Training）
- 📘 目标：学习如何从加了噪声的数据中一步步“去噪”回原始图像。训练过程具体包含两个步骤
- 🔄 Step 1：前向扩散过程（Forward / Diffusion Process）
  - 我们人为地往数据 $𝑥_0$ 中逐步加入噪声ϵ，形成一个马尔可夫链：
![image1](https://quicklatex.com/cache3/c3/ql_2d56d0f7d350677667ea7fd4007170c3_l3.png)
  - 𝛽𝑡：时间步𝑡的噪声强度（预定义）
  - 经过 𝑇 步后，数据变成接近纯高斯噪声
  - **我们还可以直接从 $𝑥_0$ ​采样 $𝑥_𝑡$** 
![img2](https://quicklatex.com/cache3/15/ql_15554b786a681ed9119136e5bfac1e15_l3.png)

- 🔁 Step 2：学习反向过程（去噪 / 生成）
  - 📘 目标： 学习一个神经网络 $𝜖_𝜃(𝑥_𝑡,𝑡)$  使它能预测加噪时用的噪声 𝜖 ，从而实现去噪。
  - 📌 训练目标函数：针对某一个时间步t 最小化 **模型预测的噪声减去真实的噪声** 的平方
  ![img3](https://quicklatex.com/cache3/ef/ql_708acae64b9fb5cda2b489dd58bdbcef_l3.png)
  - 这意味着我们从真实图像 $𝑥_0$ 加噪得到 $x_t$，然后训练模型去预测加进去的噪声 𝜖，从而达成去噪的效果。

 - ✅ 总结一句话训练流程：模型学习如何“预测添加的噪声”，以便在生成时能把噪声去除、还原出图像。

#### 推理过程/采样过程（Inference / Sampling）
- 📘 目标：从纯高斯噪声开始，逐步去噪，最终生成一张图像。

- 💡 初始输入：高斯纯噪声
![img3](https://quicklatex.com/cache3/a5/ql_6d30784d70423ac11f941060f434dea5_l3.png)
- 🔁 每一步做什么？
  - 使用训练好的神经网络预测噪声，并从中采样 $x_{t−1}$
  - 最终得到 $x_0$，即生成图像。
​- ✅ 总结一句话推理流程：给模型一个纯噪声图像，它一步一步去掉噪声，最终输出一个看起来像“真实图片”的生成结果。

### 扩散模型算法伪代码
![扩散模型算法伪代码](https://github.com/Learning-Terms/Image-Generation-Model/blob/main/CleanDIFT/%E6%89%A9%E6%95%A3%E6%A8%A1%E5%9E%8B%E7%AE%97%E6%B3%95%E4%BC%AA%E4%BB%A3%E7%A0%81.png)
#### 算法1 训练过程
- T的最佳取值可以提前计算得出（一共需要多少个时间步）
- 从训练集中取出一些训练数据 从1--T中随机抽样一个时间步t  从高斯分布中生成随机噪声  三个采样过程
- 开始对时间步t下的目标函数 $L_{simple}$ 进行优化（梯度下降法）直到目标函数收敛到特定值以下
- 不断重复上述过程

#### 算法2 采样过程（推理过程）
- 首先获得一个高斯纯噪声 $x_T$
- 对t进行遍历 从T到1 每一个子过程中获得 $x_{t-1}$
- 完成所有遍历之后 返回 $x_0$ 也就是最终的还原结果--生成的图像

#### 🧱 结构总览图（简化版）
```
训练阶段：
x_0 → x_1 → ... → x_T       （加噪过程，前向）
           ↓
   学习 εθ(x_t, t) ≈ 加入的噪声 ε（去噪目标）

推理阶段：
x_T → x_{T-1} → ... → x_0   （去噪过程，反向）
```






