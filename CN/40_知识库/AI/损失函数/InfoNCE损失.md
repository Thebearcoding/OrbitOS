---
area: "[[AI]]"
tags: [损失函数, 对比学习, 多模态]
created: 2026-02-11
---
# InfoNCE 损失

## 定义

InfoNCE（Noise-Contrastive Estimation）是 [[对比学习]] 中最核心的损失函数，目标是让匹配的样本对在嵌入空间中更近，不匹配的样本对更远。本质上是一个 **N 选 1 的分类问题**。

## 公式

$$L = -\log \frac{\exp(\text{sim}(v_i, t_i) / \tau)}{\sum_{j=1}^{N} \exp(\text{sim}(v_i, t_j) / \tau)}$$

- $v_i, t_i$：匹配的图文嵌入对
- $\tau$：温度参数（通常 0.07），越小分布越尖锐，区分度越高
- $N$：批次大小（CLIP 使用 32,768）

## 要点

- **分子**：正样本对的相似度（越大越好）
- **分母**：所有样本对的相似度之和（让正样本脱颖而出）
- **大 batch 是关键**：负样本越多，模型区分能力越强
- **双向计算**：图→文 和 文→图 的损失取平均
- **温度参数**：控制 softmax 分布的平滑程度

## 示例

### 直觉理解

一个 batch 有 4 对图文，对角线是正样本对：

```
        文本1   文本2   文本3   文本4
图片1   ✅ 0.9   0.1    0.2    0.3
图片2    0.2   ✅ 0.8   0.1    0.3
图片3    0.1    0.3   ✅ 0.7   0.2
图片4    0.3    0.1    0.2   ✅ 0.9
```

InfoNCE 让对角线的值尽可能大。

### 代码实现

```python
import torch
import torch.nn.functional as F

def info_nce_loss(image_features, text_features, temperature=0.07):
    # 归一化
    image_features = F.normalize(image_features, dim=-1)
    text_features = F.normalize(text_features, dim=-1)

    # 计算相似度矩阵 (N x N)
    logits = image_features @ text_features.T / temperature

    # 标签：对角线就是正样本 → [0, 1, 2, ..., N-1]
    labels = torch.arange(len(logits), device=logits.device)

    # 双向损失：图→文 + 文→图
    loss = (F.cross_entropy(logits, labels) +
            F.cross_entropy(logits.T, labels)) / 2
    return loss
```

## 相关概念

- [[对比学习]] - InfoNCE 是对比学习的核心目标函数
- [[CLIP模型]] - CLIP 使用 InfoNCE 进行图文对齐训练
- [[多模态表示学习]] - InfoNCE 帮助构建跨模态统一表示

## 参考资料

- [CLIP 论文](https://arxiv.org/abs/2103.00020) - Radford et al., 2021
- [CPC 论文 (InfoNCE 提出)](https://arxiv.org/abs/1807.03748) - Oord et al., 2018
