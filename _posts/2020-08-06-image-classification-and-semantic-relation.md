---
layout: post
title: 图像识别与语义相关性
---
# 图像识别与语义相关性

## key word
多模态 / visual-semantic embedding / zero-shot learning

## Large scale image annotation: learning to rank with joint word-image embeddings
算法优缺点
该算法对于训练样本包含的每一个标签，给定了一个 embedding，在训练时，对 image 的 feature 做变换，
将 image 的 feature 映射到 label 的 embedding 空间。跨模态对应文本的一端仅限于已知的 label，
对新增类别不能很好的处理



## DeViSE: A Deep Visual-Semantic Embedding Model

模型结构
![DeViSE模型](https://github.com/zstang/zstang.github.io/img/visual-semantic-v1/devise.png)

算法分三部分
第一部分是使用wikipedia文本训练词向量，文本包含了训练样本的标签文本，使用skip-gram进行训练，设置的
窗口大小为20，向量维度 500 和1000较佳，作者训练了不同维度的向量，从100-2000维不等
第二部分是使用AlexNet训练图像识别模型，基于ImageNet数据集，1000个类别，softmax输出
第三部分是将图像识别模型的输出改为标签对应的词向量，先固定AlexNet特征提取部分，训练变换矩阵，然后第二阶段再对AlexNet整体进行fine-tune

相似度计算使用余弦距离，loss使用margin loss

![Loss函数](https://github.com/zstang/zstang.github.io/img/visual-semantic-v1/devise-loss.png)

算法优缺点
已有算法，尽管也有对视觉和文本联合建模的，但是都有某些缺陷，要么类别限于训练样本包含的类别，
扩展到新的类别不方便，要么模型评估的类别数量远远低于本文数据集，不能满足实际应用需求，或者
依赖于额外的信息如wordnet层次结构，或者辅助的知识库描述信息。
本文算法对于语义特征的学习基于无标注数据。

结论：
使用大规模文本语料训练标签的embedding，然后将视觉模型输出投射到文本向量空间。文本使用大规模
语料训练，向量距离代表文本的语义相关性。通过构建视觉特征和文本特征的相关性，在预测时通过向量距
离衡量与标签的相关性，带来两方面的结果，一是在模型出现误判时，召回的仍旧是向量空间中满足距离约
束的标签，即语义相关的标签；二是对于未参与训练的类别的数据，仍然有较高的几率被识别。即 zero 
shot learning



## Learning Two-Branch Neural Networks for Image-Text Matching Tasks




## Hierarchy-based Image Embeddings for Semantic Image Retrieval




## 论文
- [DeViSE: A Deep Visual-Semantic Embedding Model](https://static.googleusercontent.com/media/research.google.com/zh-CN//pubs/archive/41473.pdf)
- [Learning Two-Branch Neural Networks for
Image-Text Matching Tasks](https://arxiv.org/pdf/1704.03470.pdf)
- [Large scale image annotation: learning to rank with joint word-image embeddings](http://bengio.abracadoudou.com/cv/publications/pdf/weston_2010_ecml.pdf)
- [Hierarchy-based Image Embeddings for Semantic Image Retrieval](https://arxiv.org/pdf/1809.09924.pdf)
