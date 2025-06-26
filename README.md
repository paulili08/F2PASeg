# F2PASeg
基于SAM2的视频流分割模型，可应用于视频流场景和3D医学影像。
* ### 介绍
基于SAM-med 2D的骨性结构分割模型，主要针对颅底术内窥镜图像中的6种结构进行分割。在原版网络基础上对image encoder，mask decoder等结构进行修改从而优化网络的分割能力。  
网络结构如图所示，Image Encoder，Mask Decoder部分均可替换：  
<p align="center"><img width="800" alt="image" src="img/framework.png" alt="F2PASeg"></p> 

* ### 软件架构
    * __SAM 组件__  
segment anyting目录下包含:  
        * 3种Image Encoder: SAM-MED2D原版Image Encoder, 带有Conv-GCN branch的Image Encoder, 带有skip connection的Image Encoder;  
        * 1种Prompt Encoder: SAM-MED2D原版Prompt Encoder;  
        * 4种Mask Decoder: SAM-MED2D原版Mask Deocder, 基于Unet的Decoder,基于Swin-Transformer的Decoder和基于Swin-Unet的Decoder.  
        * SAM自动分割模组
        * 基于SAM自动分割模组的数据增强模组  
    * __工具箱Tools__  
包括可视化组件，mask分离组件，.json文件生成组件
    * __train & test__  
根据不同使用场景有不同采用不同的train & test模块


* ### 数据集PASeg
数据集总共包含7845张来自120个垂体瘤内窥镜手术视频的截图。根据临床意义，由专业外科手术医生从120个视频中选出7845帧图像，每帧图像初始分别率为1920×1080或720×576. 
<p align="center"><img width="600" alt="image" src="img/data_sample.png" alt="Anatomical structures"></p>  
如图所示，我们选取了六种结构作为分割对象：鞍底(sella floor),颈动脉(ICA prominence),视神经(optic prominence),OCR(optic-carotid recess), 斜坡神经(clival recess)和蝶鞍结节(tuberculum sella).  
数据集由专业外科神经外科一生进行像素级标注，最后分为训练集(70)/验证集(10)/测试集(20)用于模型的训练和测试。  

__解剖结构和标注的对应关系如下表:__  
| structure | Sella floor | Tuberculum_sella | ICA_prominence | Clival_recess | Optic_carotid_recess | Optic_prominence |
| --- | --- | --- | --- | --- | --- | --- |
| label | 1 | 2 | 3 | 4 | 5 | 6 |
| colormap(BGR) | (255,150,0) | (0,255,150) | (255,0,255) | (0,150,255) | (0,255,0) | (255,255,0) |

* ### 数据集CholecSeg8K
数据集包含来自17个腹腔镜胆囊切除手术视频的8080张图像以及对应的像素级标注的mask，共有17个case,13个类别，每帧图像分辨率为854×480.  
发布时间: 2020-12  
官方项目链接🔗 [kaggle](https://www.kaggle.com/datasets/newslab/cholecseg8k)  
文章地址🔗 [Arxiv](https://arxiv.org/abs/2012.12453)  
<p align="center"><img width="600" alt="image" src="img/cholec_sample.png" alt="Anatomical structures"></p>
如图所示，做分割任务时将结缔组织等杂项归为Mis.类，手术器械归为Instruments类，最终得到包括背景在内的8类标注：背景(Background), 腹壁(Abdominal Wall
), 肝脏(Liver), 消化道(Gastrointestinal Tract), 脂肪(Fat), 手术器械(Instruments), 胆囊(Gallbladder)， 杂项(Misc).

__解剖结构和标注的对应关系如下表:__ 
| structure | Background | Abdominal Wall | Liver | Gastrointestinal Tract | Fat | Misc | Instruments | Gallbladder |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| label | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
|colormap(BGR) | (0, 0, 0) | (140, 140, 210) | (114, 114, 255) | (156, 70, 231) | (75, 183, 186) | (227, 158, 45) | (0, 255, 170) | (0, 255, 255) |


使用时请以如下结构进行分级(已给出train目录和json示例)：

> train  
>> images(original image)  
>> mask(labeled ground truth)  
>> masks(ground truth split for each structure)  
>> image2label_train.json(image&label set)  

> test  
>> images(original image)  
>> mask(labeled ground truth)  
>> masks(ground truth split for each structure)  
>> label2image_test.json(image&label set)  


* ### 使用说明
* __Step 1 从0开始的数据预处理：__  
按前文结构设置数据集层级；  
用*split_mask.py*将一张图中的不同结构的mask分成几张图，并生成对应的.json文件；  
* __Step 2 下载预训练模型:__  
使用SAM-MED2d finetune后的权重作为预训练模型，该版本相比于原版SAM网络的预训练权重增加了医疗信息，更适用于医疗场景;  
下载链接🔗 [Google云端硬盘](https://drive.google.com/file/d/1ARiB5RkSsWmAB_8mqWnwDF8ZKTtFwsjl/view?usp=drive_link)  
将*sam-med2d_b.pth*文件保存在pretrain文件夹下;  
* __Step 3 模型搭建:__  
将segment anything目录下需要使用的模块去掉后缀，修改*build_sam.py*中对应参数，即可使用；  
* __Step 4 Train & Test:__  
使用Mask Decoder对应的训练/测试模块。

* ### 可视化结果
将*test.py*中的save_pred参数设置为True就能生成对应的预测可视化结果。  
部分结果如图所示：  
<p align="center"><img width="800" alt="image" src="img/result.png" alt="result of PASeg"></p>    
