---
title: "扩散模型加入条件的三种方式"
date: "2025-05-28T00:00:00Z"
math: true
categories:
    - 工作
tags:
    - 技术
---


[扩散模型中三种加入条件的方式](https://zhuanlan.zhihu.com/p/695359814)
[条件扩散模型是如何保证模型学到条件的](https://www.zhihu.com/question/609516596)

## 三种加入条件的总结
扩散模型中三种加入条件的方式
- **Vanilla Guidance**：直接在原本的无条件扩散生成模型的神经网络中，再开一个条件信息进入的入口，然后训练这个神经网络
- **Classifier Guidance**：训练一个分类器神经网络，在去噪生成的过程中进行条件引导
- **Classifier-Free Guidance**：为了不训练额外的 classifier，classifier-free guidance 融合了 unconditional model 以及 vanilla guidance。它鼓励模型在 guidance 方向抽样，同时偏离 unconditional model 的抽样方向。其公式为：

$$
\nabla_{x} \log p(x \mid c)=w \nabla_{x} \log p(x \mid c)+(1-w) \nabla_{x} \log p(x)
$$

在扩散模型（Diffusion Models）中，加入条件的方法主要有三种：Vanilla Guidance、Classifier Guidance 和 Classifier-Free Guidance。这些方法通过不同的方式将条件信息引入模型，进而影响生成结果。以下是对这三种方法的详细解释，并附上相关公式。

### **Vanilla Guidance（传统引导）**

Vanilla Guidance 是最基本的一种方法，其中条件信息是通过改变模型的潜在空间来引导生成过程。其基本思想是将条件信息作为模型的输入之一，在每一步的反向扩散过程中，根据条件信息调整生成的分布。

**过程：**
- 假设你有一个条件 $c$，并且模型希望生成与 $c$ 相关的样本。
- 在标准的扩散模型中，我们有一个由噪声逐渐反向扩散生成数据的过程。给定条件 $c$，我们希望模型的生成结果符合该条件。

**公式：**
反向扩散的步骤通常使用如下的更新规则：

$$\mathbf{x}_{t-1} = \mathbf{x}_t + \Delta \mathbf{x}_t$$

其中，$\Delta \mathbf{x}_t$ 是通过条件信息 $c$ 调整的预测噪声。具体地，调整后的噪声预测可以通过以下方式得到：

$$\Delta \mathbf{x}_t = f(\mathbf{x}_t, c)$$

其中，$f$ 是一个函数，它将当前图像 $\mathbf{x}_t$ 和条件 $c$ 作为输入，并输出一个噪声更新量。这个方法可以通过调整 $f$ 来控制条件对生成结果的影响。

### **Classifier Guidance（分类器引导）**

Classifier Guidance 通过引入一个分类器来帮助模型在反向扩散过程中进行更有针对性的引导。该方法通常使用一个预训练的分类器网络来根据条件 $c$ 调整反向扩散过程中的噪声预测。

**过程：**
- 使用一个额外的分类器网络来计算条件信息与当前生成样本之间的关系。
- 分类器网络 $q(c | \mathbf{x})$ 预测在当前步骤 $\mathbf{x}_t$ 下的条件 $c$，然后通过调整噪声的预测来引导模型生成更符合条件的信息。

**公式：**
假设我们有一个生成样本的目标分布 $p_{\theta}(\mathbf{x}_t | c)$，在引导过程中，我们通过分类器提供的梯度来调整反向过程的噪声更新：

$$\Delta \mathbf{x}_t = \mathbf{\epsilon}_\theta(\mathbf{x}_t, t) + \lambda \nabla_{\mathbf{x}_t} \log q(c | \mathbf{x}_t)$$

其中，$\mathbf{\epsilon}_\theta(\mathbf{x}_t, t)$ 是标准的噪声预测，$\lambda$ 是引导的强度超参数，$\nabla_{\mathbf{x}_t} \log q(c | \mathbf{x}_t)$ 是分类器的梯度，用来指导模型生成更符合条件 $c$ 的样本。

### **Classifier-Free Guidance（无分类器引导）**

Classifier-Free Guidance 是一种不依赖于外部分类器的引导方法，通常通过将条件信息直接融入模型的噪声预测中，来实现对生成过程的引导。与分类器引导不同，它不需要一个预训练的分类器，而是将条件信息嵌入到模型的结构中。

**过程：**
- 在每一步的反向扩散过程中，模型通过将条件信息与噪声预测进行结合来实现引导。具体地，模型在预测噪声时，会根据是否提供条件来调整输出。
- 通过条件输入，模型学会在生成过程中自我引导，无需额外的外部分类器。

**公式：**
假设模型在反向扩散时的噪声预测是基于条件 $c$ 和无条件输入的组合：

$$\Delta \mathbf{x}_t = \mathbf{\epsilon}_\theta(\mathbf{x}_t, c, t) - \mathbf{\epsilon}_\theta(\mathbf{x}_t, \emptyset, t)$$

其中：
- $\mathbf{\epsilon}_\theta(\mathbf{x}_t, c, t)$ 是基于条件 $c$ 的噪声预测；
- $\mathbf{\epsilon}_\theta(\mathbf{x}_t, \emptyset, t)$ 是无条件的噪声预测（即没有给定条件的预测）。
- 这种方式通过计算有条件和无条件的差异来引导生成过程。

### 总结

- **Vanilla Guidance** 直接将条件作为输入，影响反向扩散过程的噪声更新。
- **Classifier Guidance** 使用一个外部预训练的分类器来提供梯度信息，调整噪声预测。
- **Classifier-Free Guidance** 不依赖于外部分类器，而是通过条件和无条件模型的差异来引导生成。

每种方法有不同的优缺点。Vanilla Guidance 实现简单，但可能较难控制生成结果；Classifier Guidance 通过分类器精细调控生成结果，但需要预训练分类器；Classifier-Free Guidance 则去除了对分类器的依赖，减少了外部资源的需求，但可能在某些任务上效果不如分类器引导。


