# Lung_Cancer_CT_Prediction
###### 資訊工程專題紀錄，雙數周更新進度報告與修正過去的錯誤
## Dataset
<img src="https://i.imgur.com/DMpxWjj.png" width="600px">

https://wiki.cancerimagingarchive.net/pages/viewpage.action?pageId=70224216
## 圖片預處理
(4/12)
#### 1.將 pixel array 經過 windowing 轉化為正常單位。 
<img src="https://i.imgur.com/YSP0SHg.png" width="300px">

**Windowing function:** 

Pixel_Windowed = pixel x slope + intercept

Max_Pixel = window_center + window_width/2

Min_Pixel = window_center - window_width/2

#### 2.Rescale windowed pixel array to range 0~255
#### 3.切割圖片，只留下中間362x362的pixel
原因:去除圖片外框padding顏色不一造成的干擾

<img src="https://i.imgur.com/PNE9qCL.png" width="400px">


## Model
Model_1 (3/29)

<img src="https://i.imgur.com/yqnPSgX.png" width="500px">


Dataset Loading:
將圖片依照病徵分為四個Label，由於Label 為E之患者僅有5位，且匯入完整Dataset電腦無法負荷，因此每個病徵各取5名病患，3名病患的CT圖做為Train set，另外兩名作為Test set。(3/29)


| Label | Trainset | Testset |
| ----- | -------- | ------- |
| A     | 258     | 117    |
| B     | 640     | 308      |
| E     | 511     | 297     |
| G     | 374     | 234     |
| Total     | 1783     | 956     |


預測方式 (詳細數據見report.csv):

3/29: ~~對於CT圖之pixel array直接行訓練與預測，epoch:11，accuracy:0.6778~~ (此數據不準確，直接使用圖片是不可行的，詳見4/12[圖片預處理](https://github.com/jerryyyyy708/Lung_Cancer_CT_Prediction#%E5%9C%96%E7%89%87%E9%A0%90%E8%99%95%E7%90%86))

4/12: 經過[圖片預處理](https://github.com/jerryyyyy708/Lung_Cancer_CT_Prediction#%E5%9C%96%E7%89%87%E9%A0%90%E8%99%95%E7%90%86)步驟1~3處理後進行訓練與預測，epoch:50，accuracy:0.4717


## 論文閱讀

**A fully automated deep learning-based network for detecting COVID-19 from a new and large lung CT scan dataset** (3/15新增)
https://www.sciencedirect.com/science/article/pii/S1746809421001853

Selection Algorithm:透過肺部中央範圍的Pixel值選擇適合用以訓練Model之圖片

FPN: 用FPN偵測不同scale的特徵，以增強Lung CT圖的預測效果(由於infection可能出現在不同尺度)

