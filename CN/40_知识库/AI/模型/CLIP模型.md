---
area: "[[AI]]"
tags: [model, multimodal, vision-language, OpenAI]
created: 2026-01-27
---
# CLIP模型

## 定义

CLIP(Contrastive Language-Image Pre-training)是 OpenAI 于 2021 年发布的视觉-语言预训练模型。它通过在 4 亿图文对上进行对比学习，将图像和文本编码到共享的语义空间，实现了强大的零样本迁移能力。

## 要点

- **架构**：双编码器结构，视觉编码器(ViT/ResNet) + 文本编码器(Transformer)
- **训练数据**：4 亿网络图文对(WebImageText)
- **投影维度**：512 维共享嵌入空间
- **零样本能力**：无需微调即可应用于图像分类、检索等任务
- **开放词汇**：可以识别训练时未见过的类别，只需提供文本描述

## 示例

使用 CLIP 进行零样本图像分类：

```python
from transformers import CLIPProcessor, CLIPModel

model = CLIPModel.from_pretrained("openai/clip-vit-base-patch32")
processor = CLIPProcessor.from_pretrained("openai/clip-vit-base-patch32")

# 零样本分类
labels = ["猫", "狗", "鸟", "汽车"]
inputs = processor(
    text=[f"一张{label}的照片" for label in labels],
    images=image,
    return_tensors="pt",
    padding=True
)

outputs = model(**inputs)
probs = outputs.logits_per_image.softmax(dim=1)
predicted = labels[probs.argmax().item()]
```

**CLIP 的变体：**
- `clip-vit-base-patch32`：基础版本，平衡速度和精度
- `clip-vit-large-patch14`：更大模型，更高精度
- `clip-vit-large-patch14-336`：支持更高分辨率图像

## 相关概念

- [[对比学习]] - CLIP 的核心训练方法
- [[多模态表示学习]] - CLIP 所属的研究领域
- [[零样本学习]] - CLIP 的关键能力
- [[视觉编码器]] - CLIP 使用的 ViT/ResNet
- [[跨模态图文检索]] - CLIP 的典型应用场景

## 参考资料

- [Learning Transferable Visual Models From Natural Language Supervision](https://arxiv.org/abs/2103.00020)
- [OpenAI CLIP GitHub](https://github.com/openai/CLIP)
- [HuggingFace CLIP 文档](https://huggingface.co/docs/transformers/model_doc/clip)
