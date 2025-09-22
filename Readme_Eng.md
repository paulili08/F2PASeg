# F2PASeg

### Introduction
F2PASeg is a segmentation model based on **SAM2** for video stream segmentation, specifically targeting six anatomical structures in endoscopic images of skull base surgery. It can be applied to both video stream scenarios and 3D medical imaging.  
On top of the original network, the **image encoder** and **mask decoder** structures have been modified to enhance segmentation performance.  

The overall framework is shown below. Both the Image Encoder and Mask Decoder are interchangeable:  
<p align="center"><img width="900" alt="image" src="img/framework.png" alt="F2PASeg"></p> 

---

### Software Architecture
- **SAM Components**  
- **Tools**: visualization tools, mask separation tools, and `.json` file generation modules.  
- **Train & Test**: different modules are provided depending on the use case.  

---

### PAS Dataset
The dataset contains **7,845 frames** extracted from **120 pituitary tumor endoscopic surgery videos**.  
Frames were selected by experienced neurosurgeons based on clinical significance. Each frame originally has a resolution of **1920×1080** or **720×576**.  

<p align="center"><img width="1000" alt="image" src="img/data_sample.png" alt="Anatomical structures"></p>  

Six anatomical structures are chosen as segmentation targets:
- Sella floor  
- ICA prominence  
- Optic prominence  
- Optic-carotid recess (OCR)  
- Clival recess  
- Tuberculum sella  

Pixel-level annotations were performed by professional neurosurgeons. The dataset is split into **training (70%) / validation (10%) / test (20%)** sets.  

**Labeling schema:**

| Structure | Sella floor | Tuberculum sella | ICA prominence | Clival recess | Optic-carotid recess | Optic prominence |
| --- | --- | --- | --- | --- | --- | --- |
| Label | 1 | 2 | 3 | 4 | 5 | 6 |
| Colormap (BGR) | (255,150,0) | (0,255,150) | (255,0,255) | (0,150,255) | (0,255,0) | (255,255,0) |

---

### CholecSeg8K Dataset
This dataset contains **8,080 annotated frames** from **17 laparoscopic cholecystectomy surgery videos**, with pixel-level masks.  
It includes **17 cases** and **13 categories**, with frame resolution of **854×480**.  

- Release date: 2020-12  
- Official project link: [Kaggle](https://www.kaggle.com/datasets/newslab/cholecseg8k)  
- Paper: [Arxiv](https://arxiv.org/abs/2012.12453)  

<p align="center"><img width="600" alt="image" src="img/cholec_sample.png" alt="Anatomical structures"></p>  

For segmentation tasks, connective tissues and other irrelevant regions are grouped into a **Misc** class, and surgical instruments are grouped into an **Instruments** class. The final labeling includes **8 categories** (including background):  

| Structure | Background | Abdominal Wall | Liver | Gastrointestinal Tract | Fat | Misc | Instruments | Gallbladder |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Label | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
| Colormap (BGR) | (0, 0, 0) | (140, 140, 210) | (114, 114, 255) | (156, 70, 231) | (75, 183, 186) | (227, 158, 45) | (0, 255, 170) | (0, 255, 255) |

---

### Dataset Directory Structure
``` 
PASeg
├── train
│   ├── image
│   |      ├── case_0004
│   |         ├── 0.jpg
│   |         ├── 1.jpg
│   |         ├── ...
│   ├── mask
│         ├── case_0004
│            ├── 0.png
│            ├── 1.png
│            ├── ...
├── val
└── test
```

---

### Visualization Results
By setting `save_pred=True` in *test.py*, predicted segmentation visualizations will be generated.  

Examples of segmentation results:  
<p align="center"><img width="1000" alt="image" src="img/result.png" alt="result of PASeg"></p>    
<p align="center"><img width="800" alt="image" src="img/seg_result.gif" alt="result of PASeg"></p>  

