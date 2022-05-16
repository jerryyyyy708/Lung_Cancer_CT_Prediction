# Lung_Cancer_CT_Prediction
###### 資訊工程專題紀錄，進度報告與修正過去的錯誤

## Dataset
<img src="https://i.imgur.com/DMpxWjj.png" width="600px">

https://wiki.cancerimagingarchive.net/pages/viewpage.action?pageId=70224216

## 圖片篩選

資料集中並非所有圖片都是適合用來訓練的 (如下圖)，

![](https://i.imgur.com/1vUbk32.png)
利用資料集官網中提供之 visualization tool，可透過 annotation 資料夾中之.xml檔將圖片用於癌症判斷之位置以方框標示出來，以此了解 Label 的判斷依據，而未被標示之圖片即為無法用於訓練、判斷Label之圖片。以該 tool 為基礎修改，將有被標示的圖片檔名寫進文件中，取得有被標示之圖片列表，將其中的圖片作為使用的資料集。

![](https://i.imgur.com/jCQI5bj.png)
 *Visualize結果與有標示圖片之列表*

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



## 論文閱讀

**A fully automated deep learning-based network for detecting COVID-19 from a new and large lung CT scan dataset**

https://www.sciencedirect.com/science/article/pii/S1746809421001853

FPN: 用FPN偵測不同scale的特徵，以增強Lung CT圖的預測效果(由於infection可能出現在不同尺度)

**Deep Residual Learning for Image Recognition**

ResNet https://arxiv.org/abs/1512.03385

**U-Net: Convolutional Networks for Biomedical Image Segmentation**

U-Net https://arxiv.org/abs/1505.04597

**Grad-CAM: Visual Explanations from Deep Networks via Gradient-based Localization**

GradCAM https://arxiv.org/abs/1610.02391

## 待辦事項

~~1. Selection Algorithm 篩選圖片 (已完成)~~

2.特化Model提升預測效果

3.應用FPN提取不同scale特徵
 
4.使用Grad-cam將預測可視化

5.Data augmentation，以讓Label E之圖片可以加入訓練

## Update

2022 4/13 新增圖片預處理 step 1~3、Model_1

2022 4/14 新增圖片預處理 step 4，新增report

2022 5/16 版面重整(移除無效訓練數據)、新增由visualization tool 延伸之圖片篩選

