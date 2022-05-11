# Lung_Cancer_CT_Prediction
###### 資訊工程專題紀錄，進度報告與修正過去的錯誤
## Dataset
<img src="https://i.imgur.com/DMpxWjj.png" width="600px">

https://wiki.cancerimagingarchive.net/pages/viewpage.action?pageId=70224216
## 圖片預處理

#### 1.將 pixel array 經過 windowing 轉化為正常單位。
<img src="https://i.imgur.com/YSP0SHg.png" width="400px">

**Windowing function:** 

Pixel_Windowed = pixel x slope + intercept

Max_Pixel = window_center + window_width/2

Min_Pixel = window_center - window_width/2

#### 2.Rescale windowed pixel array to range 0~255
#### 3.切割圖片，只留下中間362x362的pixel
原因:去除圖片外框padding顏色不一造成的干擾

<img src="https://i.imgur.com/PNE9qCL.png" width="400px">


#### 4. 將圖片依 Soft Tissue Window 與 Lung Tissue Window 分開

<img src="https://i.imgur.com/nItlDxX.png" width="400px">

4-1 Window width 為350的屬於 Soft Tissue Window (用於軟組織)

4-2 其餘的屬於 Lung Tissue Window  (可觀測肺的薄壁組織 Lung parenchyma)


## Model
Model_1 

<img src="https://i.imgur.com/yqnPSgX.png" width="500px">

ResNet

使用 torchvision.model中之ResNet50來進行預測

**Dataset Loading:**

將圖片依照病徵分為四個Label，由於Label 為E之患者僅有5位，且匯入完整Dataset電腦無法負荷，因此每個病徵各取5名病患，3名病患的CT圖做為Train set，另外兩名作為Test set。


**預測方式 (詳細數據見report.csv, 4/14新增):**
| Label | Trainset | Testset |
| ----- | -------- | ------- |
| A     | 258     | 117    |
| B     | 640     | 308      |
| E     | 511     | 297     |
| G     | 374     | 234     |
| Total     | 1783     | 956     |

*表格1 步驟4之前之Data分布*

Model_1: ~~對於CT圖之pixel array直接行訓練與預測，epoch:11，accuracy:0.6778~~ (此數據不準確，直接使用圖片是不可行的，詳見[圖片預處理](https://github.com/jerryyyyy708/Lung_Cancer_CT_Prediction#%E5%9C%96%E7%89%87%E9%A0%90%E8%99%95%E7%90%86))

Model_1: ~~經過圖片預處理步驟1~3處理後進行訓練與預測，epoch:50，accuracy:0.4717~~ (此數據不準確，圖片需經過步驟4分流，詳見[圖片預處理](https://github.com/jerryyyyy708/Lung_Cancer_CT_Prediction#%E5%9C%96%E7%89%87%E9%A0%90%E8%99%95%E7%90%86))

| Label | Trainset | Testset |
| ----- | -------- | ------- |
| A     | 134     | 59    |
| B     | 160     | 105      |
| E     | 161     | 107     |
| G     | 147     | 69     |
| Total     | 602     | 340     |

*表格2 步驟4之後之Data分布*

Model_1: 經過[圖片預處理](https://github.com/jerryyyyy708/Lung_Cancer_CT_Prediction#%E5%9C%96%E7%89%87%E9%A0%90%E8%99%95%E7%90%86)步驟1~4處理後進行訓練與預測，epoch:97，accuracy:0.55

ResNet: 經過[圖片預處理](https://github.com/jerryyyyy708/Lung_Cancer_CT_Prediction#%E5%9C%96%E7%89%87%E9%A0%90%E8%99%95%E7%90%86)步驟1~4處理後進行訓練與預測，epoch:79，accuracy:0.6176，label A、B準確率高，C大約50%，G無法正確預測。(目標:用Gradcam觀察原因)
## 論文閱讀

**A fully automated deep learning-based network for detecting COVID-19 from a new and large lung CT scan dataset**

https://www.sciencedirect.com/science/article/pii/S1746809421001853

Selection Algorithm:透過肺部中央範圍的Pixel值選擇適合用以訓練Model之圖片

FPN: 用FPN偵測不同scale的特徵，以增強Lung CT圖的預測效果(由於infection可能出現在不同尺度)

**Deep Residual Learning for Image Recognition**

ResNet https://arxiv.org/abs/1512.03385

**U-Net: Convolutional Networks for Biomedical Image Segmentation**

U-Net https://arxiv.org/abs/1505.04597

**Grad-CAM: Visual Explanations from Deep Networks via Gradient-based Localization**

GradCAM https://arxiv.org/abs/1610.02391

## 代辦事項

1. Selection Algorithm 篩選圖片
2. 加深Model提升預測效果
3. 應用FPN提取不同scale特徵
4. 使用Grad-cam將預測可視化

