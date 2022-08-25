# Lung_Cancer_CT_Prediction
###### 學習如何預處理及應用CT圖檔案

## Dataset
<img src="https://i.imgur.com/DMpxWjj.png" width="600px">

https://wiki.cancerimagingarchive.net/pages/viewpage.action?pageId=70224216

## 圖片篩選

資料集中並非所有圖片都是適合用來訓練的 (如下圖)，

![](https://i.imgur.com/1vUbk32.png)
因此需先篩選出足以分辨腫瘤存在的圖像。

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

4-1 Window width 介於300~400的屬於 Soft Tissue Window (用於軟組織)

4-2 其餘的屬於 Lung Tissue Window  (可觀測肺的薄壁組織 Lung parenchyma)


## Model
#### Model_1 

一個簡單的CNN模型
![](https://i.imgur.com/R4VHVlI.png)
#### ResNet

使用 torchvision.models 中的 ResNet50 作為預測模型。

**Dataset Loading:**

各 Label 可用之圖片數量分別為 A: 6595張，B: 936張，C: 60張，D: 2060張。
由於 Label E之圖片數量特別少，暫時不參與訓練 (待辦: data augmentation)，
其餘 Label 各取約600張圖做為trainset，200張圖做為testset。

*備註:同為病人之圖片不可同時存在於train與testset，因此須以病人作為分群單位*

| Label | Trainset(img/病患) | Testset(img/病患) |
| ----- | -------- | ------- |
| A     | 603/71     | 208/9    |
| B     | 665/33     | 212/3      |
| G     | 614/36     | 255/4     |
| Total     | 1882/140     | 675/16     |

**Result:** (詳細數據見 report.csv)

Model_1: epoch 23, accuracy: 0.484444。

ResNet50: epoch 11, accuracy: 0.5703703703703704。

分析: 有些病人的圖片數量特別多，在testset時可能導致大量出錯，在trainset則會造成訓練不平衡。



