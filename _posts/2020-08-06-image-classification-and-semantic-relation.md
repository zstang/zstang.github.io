---
layout: default
title: 图像识别与语义相关性
---
# 图像识别与语义相关性
  
### key word  

多模态 / visual-semantic embedding / zero-shot learning  
  

## 概述
针对图像和文本的相关性建模，一般都使用双塔结构，一个分支对应视觉部分的embedding，一个
分支对应文本部分的embedding；目前的相关研究对于文本部分一般都采用预训练词向量的方式处理，
部分模型会对文本向量进行额外的变换，另一部分则直接将视觉特征学习变换到文本语义特征空间。  
  
在模型中考虑视觉和语义的相关性，目的主要包括几种：  
- 使得视觉分支对图像输出的特征有较好的语义信息，对于Image Caption类的任务生成更好的特征
- zero shot learning, 使得模型对未参与训练的类别有一定的识别能力，尽管模型不能提供相关的保证
- 增强模型的可解释性，在模型识别出错时，尽管识别错误，仍然是语义上相关的结果，使不懂技术的人更能够认可和接受
  

## 相关论文
  
### Large scale image annotation: learning to rank with joint word-image embeddings

算法优缺点  
该算法对于训练样本包含的每一个标签，给定了一个 embedding，在训练时，对 image 的 feature 做变换,
将 image 的 feature 映射到 label 的 embedding 空间。跨模态对应文本的一端仅限于已知的 label,
对新增类别不能很好的处理  
  
  

### DeViSE: A Deep Visual-Semantic Embedding Model *2013*
  
模型结构  
![DeViSE模型](https://zstang.github.io/img/visual-semantic-v1/devise.png)
  
算法分三个步骤  
- 使用wikipedia文本训练词向量，文本包含了训练样本的标签文本，使用skip-gram进行训练，设置的
窗口大小为20，向量维度 500 和1000较佳，作者训练了不同维度的向量，从100-2000维不等  
- 使用AlexNet训练图像识别模型，基于ImageNet数据集，1000个类别，softmax输出  
- 将图像识别模型的输出改为标签对应的词向量，先固定AlexNet特征提取部分，训练变换矩阵，
然后第二阶段再对AlexNet整体进行fine-tune
  
相似度计算使用余弦距离，loss使用margin loss  
  
![Loss函数](https://zstang.github.io/img/visual-semantic-v1/devise-loss.png)
  
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
  
  

### Learning Deep Structure-Preserving Image-Text Embeddings *2016*
解决的问题：图像检索
创新点：使用多种约束的loss设计，来确保不仅仅视觉特征可以和文本语义特征在向量空间的距离
关系，同时对视觉分支和文本特征分支施加对应的loss约束，使得视觉相似性和文本语义相关性仍然
可以保留。
  
  

### Learning Two-Branch Neural Networks for Image-Text Matching Tasks  *2018*
  
to be continue...  
  
### Hierarchy-based Image Embeddings for Semantic Image Retrieval *2018*

算法解决的问题：  
同样是解决图像检索场景的结果相关性问题，期望相关的类别其embedding能够靠近，不相关的类别  
能够互相远离。  
DeViSE 的做法是通过大规模语料训练词向量，基于大规模的数据来确定文本相关性。本文算法的做法是
基于人工设计的规则，结合wordnet层次结构，设计了一种相关度计算方法。对于wordnet中的任意节点，
都可以得到其与其他节点的相关性，基于规则得到的相关性，作者提供了两种方式构造文本的向量表达，一种
是基于规则，使用类似解方程的方式求解对应文本的向量，另一种是直接做矩阵分解进行近似。作者的实验
表明是人工设计的向量反而效果更好。
  
在类别对应的文本的向量已经生成的前提下，基于深度模型推理得到的图像特征投射到类别向量空间。实验
部分，作者使用了vgg， resnet-110， pyramidnet三种骨干网络。  
训练时使用图像的特征向量与文本类别对应的向量做内积作为识别的相关度s，以1-s作为loss；同时，使用
典型的分类任务作为辅助任务，以交叉熵loss来计算损失，多任务训练整个模型。  
  
相比 DeViSE，本文算法在处理的类别上限定于wordnet定义的范围。同时使用了多任务，对于样本中的类
别能得到更好的效果应是合理结果。但是不能像DeViSE一样扩展到未参与训练的类别/zero shot learning。  
  
作者还将其方法与center loss做对比，逻辑上该方法能优于center loss的部分原因在于，center loss
约束的类内方法，不能对类别的语义相关性做约束，本文方法在构造类别的embedding时已经将类别的相关性
予以考虑。  
  
  
### Unifying Visual-Semantic Embeddings with Multimodal Neural Language Models *2014*

算法解决的问题：Image Caption，图像描述  
模型结构： encoder-decoder  
算法训练过程：先将图像映射到文本向量空间，让图像的特征与文本有较好的相关性，然后使用decoder进行文本生成。  
模型结构图：  
![Image](https://zstang.github.io/img/visual-semantic-v1/Image-Caption.png)
  
*对于语言模型和文本生成相关资料看的不多，后续再完善*  
  

### Learning a Deep Embedding Model for Zero-Shot Learning *2017*

算法解决的问题：基于相关性做图像分类/zero shot learning
创新点：以视觉特征空间作为最终的向量空间，对比DeViSE,是以标签的文本向量空间作为目标特征空间  

个人理解：  
- 基于大规模文本数据训练词向量带来的结果是文本语义空间，在这个空间可以达到的预期是具有相近语义的
文本也具有相近的向量表达。其问题在于对于相近但非相同的概念会变的难以区分。  
- 本文反其道而行之，以视觉特征空间作为目标特征空间，将文本向量向视觉特征空间进行投射。最终可以对
所有标签类别生成更容易区分的特征表达，减少分类错误。  
- 逻辑上还需要基于标签相关性构造对应的loss，减少对预训练语义的破坏。  
  
  
论文提出的一个问题是高维特征空间导致的 hubness problem，具体情况待研究  
"""
The hubness problem The phenomenon of the presence
of ‘universal’ neighbours, or hubs, in a high-dimensional
space for nearest neighbour search was first studied by
Radovanovic et al. [26]. They show that hubness is an 
inherent property of data distributions in a 
high-dimensional vector space, and a specific aspect of 
the curse of dimensionality. A couple of recent studies [7, 41] 
noted that regression based zero-shot learning methods suffer 
from the hubness problem and proposed solutions to mitigate 
the hubness problem. Among them, the method in [7] relies on
the modelling of the global distribution of test unseen data
ranks w.r.t. each class prototypes to ease the hubness problem. 
It is thus transductive. In contrast, the method in [41] is
inductive: It argued that least square regularised projection
functions make the hubness problem worse and proposed
to perform reverse regression, i.e., embedding class prototypes 
into the visual feature space. Our model also uses the
visual feature space as the embedding space but achieve so
by using an end-to-end deep neural network which yields
far superior performance on ZSL.
"""
  
  
  
### 论文
- [DeViSE: A Deep Visual-Semantic Embedding Model](https://static.googleusercontent.com/media/research.google.com/zh-CN//pubs/archive/41473.pdf)
- [Learning Two-Branch Neural Networks for
Image-Text Matching Tasks](https://arxiv.org/pdf/1704.03470.pdf)
- [Large scale image annotation: learning to rank with joint word-image embeddings](http://bengio.abracadoudou.com/cv/publications/pdf/weston_2010_ecml.pdf)
- [Hierarchy-based Image Embeddings for Semantic Image Retrieval](https://arxiv.org/pdf/1809.09924.pdf) [code](https://github.com/cvjena/semantic-embeddings)
- [Unifying Visual-Semantic Embeddings with Multimodal Neural Language Models](https://arxiv.org/pdf/1411.2539.pdf) [code](https://github.com/ryankiros/visual-semantic-embedding)
- [Finding beans in burgers: Deep semantic-visual embedding with localization](https://openaccess.thecvf.com/content_cvpr_2018/CameraReady/3272.pdf) [code](https://github.com/technicolor-research/dsve-loc)
- [Learning a Deep Embedding Model for Zero-Shot Learning](https://openaccess.thecvf.com/content_cvpr_2017/papers/Zhang_Learning_a_Deep_CVPR_2017_paper.pdf) [code](https://github.com/lzrobots/DeepEmbeddingModel_ZSL)
- [Learning Deep Structure-Preserving Image-Text Embeddings](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/Wang_Learning_Deep_Structure-Preserving_CVPR_2016_paper.pdf)