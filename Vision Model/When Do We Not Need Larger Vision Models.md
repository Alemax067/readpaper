# When Do We Not Need Larger Vision Models?  
- [Paper](http://arxiv.org/abs/2403.13043)  
- [Code](https://github.com/bfshi/scaling_on_scales)  

2024.11.4 Liu.  

## 任务概述  
视觉理解一系列任务中视觉底座模型被证明越大效果越好，如何用小模型达到大模型的性能？  

## 相关工作   
1. Multi-scale representation 如多尺度ViTs和分层ViTs  
相关论文待阅读  
[10] Chun-Fu Richard Chen, Quanfu Fan, and Rameswar Panda. Crossvit: Cross-attention multi-scale vision transformer for image classification. In Proceedings of the IEEE/CVF international conference on computer vision, pages 357–366, 2021.  
[24] Haoqi Fan, Bo Xiong, Karttikeya Mangalam, Yanghao Li, Zhicheng Yan, Jitendra Malik, and Christoph Feichtenhofer. Multiscale vision transformers. In Proceedings of the IEEE/CVF international conference on computer vision, pages 6824–6835, 2021.  
[36] Youngwan Lee, Jonghee Kim, Jeffrey Willette, and Sung Ju Hwang. Mpvit: Multi-path vision transformer for dense prediction. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, pages 7287–7296, 2022.  
[78] Jianwei Yang, Chunyuan Li, Pengchuan Zhang, Xiyang Dai, Bin Xiao, Lu Yuan, and Jianfeng Gao. Focal attention for long-range interactions in vision transformers. Advances in Neural Information Processing Systems, 34:30008–30022, 2021.  
[42] Ze Liu, Yutong Lin, Yue Cao, Han Hu, Yixuan Wei, Zheng Zhang, Stephen Lin, and Baining Guo. Swin transformer: Hierarchical vision transformer using shifted windows. In Proceedings of the IEEE/CVF international conference on computer vision, pages 10012–10022, 2021.  
[58] Chaitanya Ryali, Yuan-Ting Hu, Daniel Bolya, Chen Wei, Haoqi Fan, Po-Yao Huang, Vaibhav Aggarwal, Arkabandhu Chowdhury, Omid Poursaeed, Judy Hoffman, et al. Hiera: A hierarchical vision transformer without the bells-and-whistles. arXiv preprint arXiv:2306.00989, 2023.  
2. Scaling Vision Models 增加参数量，使用更高分辨率的图像做预训练  
 
## 方法概述  
Scaling on Scales (S<sup>2</sup>)  
![](../imgs/截屏2024-11-04%2016.18.40.png)
将小图像如 224x224 通过插值变成 448x448，然后将大图像切分为四个小图像，再将四个小图像和原来的小图像输入到同一个模型中，只后将四个小图像的特征拼接起来，再做平均池化得到和原图像一样大小的特征，最后将这个特征和原来的小图像特征相加，得到最终输出。  

关键设计：
1. 将大图像分割成小子图像,而不是直接处理整个大图像,避免了自注意力机制中的二次计算复杂度,并防止了由于位置嵌入插值而导致的性能下降。   
2. 处理独立的子图像,而不是使用窗口注意力机制,使得可以使用不支持窗口注意力的预训练模型,并避免了从头训练额外参数(如相对位置嵌入)。  
3. 将大特征图插值到常规尺寸,确保输出令牌数量保持不变,防止在下游应用(如多模态语言模型)中产生计算开销。

## 实验结果
### ViT DINO-V2 OpenCLIP ：图像分类 语义分割 深度估计
![](../imgs/截屏2024-11-04%2022.38.24.png)  
### 多模态大模型 ：图像细节理解 VQA-Benchmark MLLM-Benchmark  
![](../imgs/截屏2024-11-04%2022.36.08.png)  
![](../imgs/截屏2024-11-04%2022.39.49.png)  
### 机器人操作  
![](../imgs/截屏2024-11-04%2022.34.15.png)  

### 大模型困难样本泛化更强

### (S<sup>2</sup>) Scaling预训练可以增强小模型的能力