
📂 Assessing Urban Heat Resilience: Nonlinear Impacts of Urban Form Factors Across Scales Using Machine Learning
================================================================================================================

Lin Lili
--------

用来记录所有相关的文件信息

1\. 点击这里访问 Google Drive 文件：

[Google Drive 链接](https://drive.google.com/drive/folders/1eZbPwuhGdsPJmuMucljCuAX7itkSjtiz?usp=drive_link)

* * *

0 - grid information - based on 480m
------------------------------------
| grid_original                           | grid_original_satellite                                     |
|-----------------------------------------|-------------------------------------------------------------|
| ![grid_original](fig/grid_original.jpg) | ![grid_original_satellite](fig/grid_original_satellite.jpg) |


#### grid valid information

这里展示了我的数据选择过程，随着有效值需求的拔高，以及120和240m grid需要和480m完全对齐，我们最终得到的有效值一览表

![grid_valid_num](fig/grid_valid_num.png)

|2016 (N = 1861)                |2023 (N= 1572)                 |
|-------------------------------|-------------------------------|
|![G480_2016](fig/G480_2016.png)|![G480_2023](fig/G480_2023.png)|


1 - Extreme heat days and normal heat days identification
--------------------------
|Extreme heat day                                                                  |
|----------------------------------------------------------------------------------|
|Extreme heat is defined as a period during which the apparent temperature exceeds 33°C for at least two consecutive days, or when rapid temperature increases or prolonged heatwaves are expected to result in significant or widespread harm, based on the Korea Meteorological Administration, KMA.|
|Criteria are based on duration, intensity, and potential damage.                  |
|definition links: https://www.kdca.go.kr/contents.es?mid=a20205050300 <br> https://data.kma.go.kr/climate/windChill/selectWindChillChart.do?pgmNo=111           |
|**Summer identification**: in Korea, the summer begins in May and end in September. Therefore, when we think about the percentile of the apparent temperature, we need use the 5-9 as the data source.                                                    |
|data source: https://data.kma.go.kr/climate/RankState/selectRankStatisticsDivisionList.do?pgmNo=179 |
|Extreme heat day definition: Apparent Temperature (AT) at percentile over than 95%                  |
|Regular heat day definition: Apparent Temperature (AT) at percentile 40 - 60 %                      |

2 - Air condition and AT (apperant temperature)
-----------------------------------------------
首先选取气象站。最终我们只选了龙山气象站 ID = 108
![20160924](excel/air_station_select.png)

|air temperature data 2015_2024                |
|----------------------------------------------|
|[AT_2015_2024](original_data/AT_2015_2024.csv)|

可以看到2023年的6.16不是典型 normal heat day，在图像中前2天下了暴雨。
然而，由于所有有效数据都不太好↓<br>
![其他天情况](excel/其他天情况.png) 
![其他天情况](excel/weather_condition_on_pairs.png) 
[weather source](orignal_data/weather.xlsx)

**Final pairs : 20160924 vs 20160807**
**Final pairs : 20230616 vs 20230819**

3 - LST data and variables (original)
-----------------------------------------------

|After I get the apparent temperature>33℃ and percentile>95%, <br>
I download [data](original_data/landsat_ot_c2_l2.csv) from Landsat Collection 2 Level-2 8/9 |
|links:https://www.usgs.gov/landsat-missions/landsat-collection-2-surface-temperature       |
|To properly interpret Landsat Collection 2 Surface Temperature (ST) data, <br>
users must apply a scaling factor to convert digital numbers (DN) to temperature values in Kelvin.  <br>
For Landsat Collection 2 Level-2 Surface Temperature products, the following equation is used: <br>
ST = (DN * 0.00341802) + 149.0                                                              |

### 01 Original source of LST data
| Year | Normal heat day (Fig, Date)                           | Extreme heat day (Fig, Date)                 | Heat Resilience (Fig, Date)      |
|------|-------------------------------------------------------|----------------------------------------------|----------------------------------|
| 2016 |[clipped_20160924_B10_cleaned_LST.tif](original_data/LST/clipped_20160924_B10_cleaned_LST.tif), 99.46%|[clipped_20160807_B10_cleaned_LST.tif](original_data/LST/clipped_20160807_B10_cleaned_LST.tif), 82.53% |[2016_heat_resilience.tif](original_data/LST/2016_heat_resilience.tif), 82.10% |
| 2023 |[clipped_20230616_B10_cleaned_LST.tif](original_data/LST/clipped_20230616_B10_cleaned_LST.tif), 99.00%|[clipped_20230819_B10_cleaned_LST.tif](original_data/LST/clipped_20230819_B10_cleaned_LST.tif), 72.66% |[2023_heat_resilience.tif](original_data/LST/2023_heat_resilience.tif), 72.25% |


### 02 Original source of Features data

#### 01_building
| building  |
|-----------|
|*01* links: https://map.ngii.go.kr/ms/map/NlipMap.do
![original_building](original_data/features/building/building_01.png)|
|*02* The original shapefile contained visible seams, which I have now merged into a single unified file. ↓
![merge_building](original_data/features/building/building_02.png)<br>
[fin_building_height.shp](original_data/features/building/Fin_building_height) <-- This data is probably the best one you can use.<br>|
|*03* data cleaning ↓ <br>
a -> filtered_data = data[data['area'] >= 5]<br>
b -> [**'무벽건물'** '주택외건물' '일반주택' '연립주택' '공사중건물' '아파트' **'가건물'** '온실']  -->[          '주택외건물' '일반주택' '연립주택' '공사중건물' '아파트'         '온실']<br>
|[fin.shp](original_data/features/building/final)<-- This data is the one I finally used    |

|final data with  area >= 5 m²|
|-----------------------------|
|['연립주택' '주택외건물' '아파트' '공사중건물' '온실' '일반주택']|
|종류|
|일반주택     271724|
|주택외건물    238633|
|연립주택      98280|
|아파트       24716|
|공사중건물      1848|
|온실         1306|
|Name: count, dtype: int64|


#### 02_NDVI 
| NDVI      |
|-----------|
|*01* links: https://landsat.gsfc.nasa.gov/satellites/landsat-8/landsat-8-bands/|
|NDVI = (NIR - Red) / (NIR + Red) band 4 = Red, band 5 = NIR, Landsat 8/9 Surface Reflectance Band 5 & Band 4 from Collection 2 Level-2|


#### 03_parks
| parks   |
|---------|
|Area >=100000㎡|
|final file I used: [parks.shp](original_data/features/parks/e_park/seoul_large_park.shp)|

|park_1|

#### 04_elevation
|elevation|
|---------|
|file : [elevation.tif](/original_data/features/elevation/seoul_dem.tif)



|name     | description                |photo                             |
|---------|----------------------------|----------------------------------|
| BCR     |building cover ratio        |![](variables/Seoul_2building.png)|
| BHV     |building shpfile with height|![](variables/Seoul_2bh.png)      |
| SVF     |sky view factors            |![](variables/Seoul_3svf.png)     |
| NDVI    |landsat 8 Band 4 & Band 5   |![](variables/Seoul_7ndvi.png)    |
| EV      |elevation                   |![](variables/Seoul_8ev.png)      |
| WR      |waterbodies file            |![](variables/Seoul_4wb.png)      |
| Dist_P  |parks file                  |![](variables/Seoul_5bp.png)      |
| Dist_M  |mountain file               |![](variables/Seoul_6mt.png)      |
| Dist_W  |waterbodies file            |![](variables/Seoul_4wb.png)      |


|No. |Indicator|Unit| Formula                                                 | Data by grids     |
|----|---------|----|---------------------------------------------------------|-------------------|
| 01 | BCR     |%   |BCR = Built-up Area / Total Land Area                    |국토지리정보원 (NGII)|
| 01 | BHV     |m   |BHV = SD(Building Heights within unit)                   |국토지리정보원 (NGII)|
| 01 | SVF     |N/A |SVF = Visible Sky Hemisphere Area / Total Hemisphere Area|국가정보포털 (NSDI)  |
| 02 | NDVI    |N/A |NDVI = (NIR - Red) / (NIR + Red) |USGS - Landsat 8 Operational Land Imager (OLI) surface reflectance from Bands 4 and 5|
| 02 | EV      |m   |Elevation(x,y)=DEM(x,y)                                  |국토지리정보원 (NGII)|
| 02 | WR      |%   |WR = Water Surface Area / Total Unit Area                |국가정보포털 (NSDI)  |
| 03 | Dist_P  |km  |Euclidean_Distance(*Centroid*, Nearest Park)             |Seoul Open Data    |
| 03 | Dist_M  |km  |Euclidean_Distance(*Centroid*, Nearest Mountain)         |국가정보포털 (NSDI)  |
| 03 | Dist_W  |km  |Euclidean_Distance(*Centroid*, Nearest Waterbody)        |국가정보포털 (NSDI)  |


4 - LST data and variables (based on grid)
--------------------------

### 01 statistics of data based on Grid 120, 240, 480 m

|2016 (G = 120,240,480)                                            |2023 (G = 120,240,480)                                            |
|------------------------------------------------------------------|------------------------------------------------------------------|
|[2016_Descriptive_statistics](csv/2016_Descriptive_statistics.csv)|[2023_Descriptive_statistics](csv/2023_Descriptive_statistics.csv)|

### 02 Comparison of LST data (original)

|Year|Normal heat day(AT,Percentile,LOCAL_TIME)       |Extreme heat day(AT,Percentile,LOCAL_TIME)       |Heat Resilience            |
|----|------------------------------------------------|-------------------------------------------------|---------------------------|
|2016|![](fig/20160924.jpg) 27.7℃, 47.51, 11:11:17   |![](fig/20160807.jpg) 34.3℃, 97.47, 11:11:04    |![](fig/2016_HR.jpg)       |
|2023|![](fig/20230616.jpg) 28.4℃, 53.95, 11:10:22   |![](fig/20230819.jpg) 33.7℃, 95.68, 11:10:50    |![](fig/2023_HR.jpg)       |


### 03 Comparison of LST data based on Grid 480m(2016, 2023)

| Year | Normal heat day (Fig, Date)           | Extreme heat day (Fig, Date)          | Heat Resilience (Fig, Date)         |
|------|---------------------------------------|---------------------------------------|-------------------------------------|
| 2016 | ![](fig/G480_nor2016.jpg)<br>20160924 | ![](fig/G480_ext2016.jpg)<br>20160807 | ![](fig/G480_hr2016.jpg)<br>2016 HR |
| 2023 | ![](fig/G480_nor2023.jpg)<br>20230616 | ![](fig/G480_ext2023.jpg)<br>20230819 | ![](fig/G480_hr2023.jpg)<br>2023 HR |


### 04 Features correlation test
| Year | Pearson correlation            | Spearman correlation            | VIF score                     |
|------|--------------------------------|---------------------------------|-------------------------------|
| 2016 | ![](fig/2016_pearson_corr.png) | ![](fig/2016_spearman_corr.png) | ![](excel/2016_VIF_score.png) |
| 2023 | ![](fig/2023_pearson_corr.png) | ![](fig/2023_spearman_corr.png) | ![](excel/2023_VIF_score.png) |

->> Based on Spearman correlation, we clean the BHA.

### 05 Comparison of Features data (2016,2023)

| Features| 2016                       | 2023                            |
|---------|----------------------------|---------------------------------|
| BCR     | ![](fig/G480_V_BCR.jpg)    | ![](fig/G480_V_BCR_2023.jpg)    |
| BHV     | ![](fig/G480_V_BHV.jpg)    | ![](fig/G480_V_BHV_2023.jpg)    |
| SVF     | ![](fig/G480_V_SVF.jpg)    | ![](fig/G480_V_SVF_2023.jpg)    |
| NDVI    | ![](fig/G480_V_NDVI.jpg)   | ![](fig/G480_V_NDVI_2023.jpg)   |
| EV      | ![](fig/G480_V_EV.jpg)     | ![](fig/G480_V_EV_2023.jpg)     |
| WR      | ![](fig/G480_V_WR.jpg)     | ![](fig/G480_V_WR_2023.jpg)     |
| Dist_P  | ![](fig/G480_V_DistP.jpg)  | ![](fig/G480_V_DistP_2023.jpg)  |
| Dist_M  | ![](fig/G480_V_DistM.jpg)  | ![](fig/G480_V_DistM_2023.jpg)  |
| Dist_W  | ![](fig/G480_V_DistW.jpg)  | ![](fig/G480_V_DistW_2023.jpg)  |

5 - spatial regression model result
-----------------------------------

### 00 error information

|      | 2016                                                                   | 2023                                           |
|------|------------------------------------------------------------------------|------------------------------------------------|
| unit | ![](fig/2016_isolated_grid.png) <br>1308, 1377, 1411, 1471, 1525, 1690 | ![](fig/2023_isolated_grid.png) <br>1392, 1571 |

### 01 OLS

y = β₁X₁ + β₂X₂ + ...+ ε explanatory\_vars = \['BCR', 'BHV', 'SVF', 'NDVI', 'EV', 'WR', 'Dist\_W', 'Dist\_P', 'Dist\_M'\]

| Year | Type              | R²     | RMSE    |
|------|-------------------|--------|---------|
| 2016 | Normal heat day   | 0.8539 | 0.9577  |
| 2016 | Extreme heat day  | 0.9055 | 1.2423  |
| 2016 | Heat resilience   | 0.8102 | 0.7770  |
| 2023 | Normal heat day   | 0.9206 | 1.1580  |
| 2023 | Extreme heat day  | 0.8622 | 1.6241  |
| 2023 | Heat resilience   | 0.1298 | 1.3000  |

### 02 Moran's I

| Year | Type              | Moran's I | p-value | Expected I | Variance  |
|------|-------------------|-----------|---------|------------|-----------|
| 2016 | Normal heat day   | 0.6881    | 0.0010  | -0.0006    | 0.0002    |
| 2016 | Extreme heat day  | 0.6760    | 0.0010  | -0.0006    | 0.0002    |
| 2016 | Heat resilience   | 0.6699    | 0.0010  | -0.0006    | 0.0002    |
| 2023 | Normal heat day   | 0.6775    | 0.0010  | -0.0007    | 0.0002    |
| 2023 | Extreme heat day  | 0.7007    | 0.0010  | -0.0007    | 0.0002    |
| 2023 | Heat resilience   | 0.8606    | 0.0010  | -0.0007    | 0.0002    |

### 03 LM test

| Year | Type              | LM-Lag (lml) | p-value (lml) | LM-Error (lme) | p-value (lme) | Robust LM-Lag (rlml) | p-value (rlml) | Robust LM-Error (rlme) | p-value (rlme) |
|------|-------------------|--------------|--------------|----------------|--------------|----------------------|----------------|------------------------|----------------|
| 2016 | Normal heat day  | 211.3825     | 6.859e-48    | 1652.8657      | 0            | 16.2983              | 5.411e-05      | 1457.7815              | 0              |
| 2016 | Extreme heat day | 248.7858     | 4.777e-56    | 1839.4007      | 0            | 30.7201              | 2.981e-08      | 1621.3350              | 0              |
| 2016 | Heat resilience  | 706.0370     | 1.455e-155   | 2894.3563      | 0            | 6.0687               | 0.01376        | 2194.3880              | 0              |
| 2023 | Normal heat day  | 261.6000     | 7.687e-59    | 1403.3011      | 4.028e-307   | 22.6666              | 1.927e-06      | 1164.3677              | 3.383e-255     |
| 2023 | Extreme heat day  | 641.0536     | 1.971e-141   | 2286.3265      | 0            | 52.8418              | 3.615e-13      | 1698.1147              | 0              |
| 2023 | Heat resilience   | 3168.4077    | 0            | 3154.6372      | 0            | 80.0721              | 3.61e-19       | 66.3016                | 3.869e-16      |

### 04 LR test

explanatory\_vars = \['BCR', 'BHV', 'SVF', 'NDVI', 'EV', 'WR', 'Dist\_W', 'Dist\_P', 'Dist\_M'\]

\# 1. OLS models\['OLS'\] = OLS(yi, x, w=w, spat\_diag=True, moran=True, name\_w=w\_name, name\_ds=ds\_name)  
\# 2. SLX models\['SLX'\] = OLS(yi, x, w=w, slx\_lags=1, spat\_diag=True, moran=True, name\_w=w\_name,name\_ds=ds\_name)  
\# 3. SLM models\['SLM'\] = ML\_Lag(yi, x, w=w, method="full", name\_w=w\_name, name\_ds=ds\_name, spat\_impacts=\['full'\])  
\# 4. SDM models\['SDM'\] = ML\_Lag(yi, x, w=w, slx\_lags=1, name\_w=w\_name, name\_ds=ds\_name, spat\_impacts=\['full'\], spat\_diag=True)  
\# 5. SEM models\['SEM'\] = ML\_Error(yi, x, w=w, method="full", name\_w=w\_name,name\_ds=ds\_name)  
\# 6. SDEM models\['SDEM'\] = ML\_Error(yi, x, w=w, slx\_lags=1, method="full", name\_w=w\_name, name\_ds=ds\_name)


|  LR test score                         |  model performance                                    |
|----------------------------------------|-------------------------------------------------------|
|[LR test score](csv/LR_test_results.csv)|[model performance](csv/LR_test_results_Model_info.csv)|

### 05 AIC/BIC

Comparison between 9 variables and 6 variables
|  formula       |explanatory\_vars = \['BCR', 'BHV', 'SVF', 'NDVI', 'EV', 'WR', 'Dist\_W', 'Dist\_P', 'Dist\_M'\]| 
|----------------|------------------------------------------------------------------------------------------------|
| AIC/BIC score  |[AIC score](csv/SLX_SEM_SLM_SDM_SDEM_AIC_BIC.csv)                                               |

|  formula       |explanatory\_vars = \['BCR', 'BHV', 'SVF', 'NDVI', 'EV', 'WR'\]                                 |
|----------------|------------------------------------------------------------------------------------------------|
| AIC/BIC score  |[part AIC score](csv/part_SLX_SEM_SLM_SDM_SDEM_AIC_BIC.csv)                                     |

\--> it's clear that SDEM (SDM) has the best performance.

### 06 final model select: partial SDEM (partial SDM)
explanatory\_vars = \['BCR', 'BHV', 'SVF', 'NDVI', 'EV', 'WR', 'Dist\_W', 'Dist\_P', 'Dist\_M'\]
explanatory\_vars\_clean =\['BCR', 'BHV', 'SVF', 'NDVI', 'EV', 'WR'\] 
X-> explanatory\_vars; WX -> explanatory\_vars\_clean

#### 01 Equation
<!-- LST = ρWln(LST) + β0+ β1BCR + β2BHV + β3NDVI + β4SVF + β5EV + β6WR + β7DistWB + β8DistGL + β9DistMT +θ1​WBCR+θ2​WBHV+θ3​WNDVI+θ4​WSVF+θ5​WEV+θ6​WWR + ε, ε∼N(0, σ²I) -->

|model   |**partial SDM**                                                                                                      |
|--------|---------------------------------------------------------------------------------------------------------------------|
|equation|$LST = \beta_0 + \rho W\ln(LST) + \beta_1 BCR + \beta_2 BHV + \beta_3 NDVI + \beta_4 SVF + \beta_5 EV + \beta_6 WR + \beta_7 DistWB + \beta_8 DistGL + \beta_9 DistMT + \theta_1 WBCR + \theta_2 WBHV + \theta_3 WNDVI + \theta_4 WSVF + \theta_5 WEV + \theta_6 WWR + \varepsilon,\ \varepsilon \sim N(0, \sigma^2 I)$|
|result\_nor\_2016 | nor\_2016 = 23.2855 + 0.5826\*Wnor\_2016 + 0.0338\*BCR + 0.0128\*BHV + 10.9005\*SVF + -20.0875\*NDVI + -0.0123\*EV + -0.1102\*WR + -0.0086\*Dist\_W + -0.0056\*Dist\_P + -0.0228\*Dist\_M + -0.1100\*W\_BCR + -0.1021\*W\_BHV + -16.4776\*W\_SVF + 2.6954\*W\_NDVI + 0.0050\*W\_EV + 0.0332\*W\_WR + 0.5826\*W\_nor\_2016|
|result\_ext\_2016 | ext\_2016 = 33.0126 + 0.5725\*Wext\_2016 + 0.0520\*BCR + 0.0118\*BHV + 12.5920\*SVF + -37.3964\*NDVI + -0.0154\*EV + -0.1850\*WR + -0.0849\*Dist\_W + -0.0030\*Dist\_P + -0.0828\*Dist\_M + -0.1396\*W\_BCR + -0.1200\*W\_BHV + -21.7296\*W\_SVF + 14.0294\*W\_NDVI + 0.0047\*W\_EV + 0.0758\*W\_WR + 0.5725\*W\_ext\_2016|
|result\_hr\_2016  | hr\_2016 = -9.1532 + 0.8417\*Whr\_2016 + -0.0197\*BCR + 0.0003\*BHV + -1.9617\*SVF + 17.0564\*NDVI + 0.0032\*EV + 0.0734\*WR + 0.0407\*Dist\_W + -0.0050\*Dist\_P + 0.0409\*Dist\_M + 0.0547\*W\_BCR + 0.0456\*W\_BHV + 8.5564\*W\_SVF + -13.9486\*W\_NDVI + 0.0001\*W\_EV + -0.0581\*W\_WR + 0.8417\*W\_hr\_2016|
|result\_nor\_2023 | nor\_2023 = 30.1599 + 0.5751\*Wnor\_2023 + 0.0448\*BCR + 0.0154\*BHV + 10.7982\*SVF + -26.2233\*NDVI + -0.0125\*EV + -0.1693\*WR + -0.1100\*Dist\_W + -0.0776\*Dist\_P + -0.0106\*Dist\_M + -0.1046\*W\_BCR + -0.1183\*W\_BHV + -20.6142\*W\_SVF + 12.8149\*W\_NDVI + -0.0005\*W\_EV + 0.0747\*W\_WR + 0.5751\*W\_nor\_2023|
|result\_ext\_2023 | ext\_2023 = 29.3256 + 0.7471\*Wext\_2023 + 0.0476\*BCR + 0.0144\*BHV + 12.4025\*SVF + -27.3378\*NDVI + -0.0127\*EV + -0.1678\*WR + -0.0464\*Dist\_W + -0.0227\*Dist\_P + -0.0636\*Dist\_M + -0.1205\*W\_BCR + -0.1437\*W\_BHV + -28.2722\*W\_SVF + 18.7731\*W\_NDVI + 0.0013\*W\_EV + 0.1100\*W\_WR + 0.7471\*W\_ext\_2023|
|result\_hr\_2023 | hr\_2023 = -1.1990 + 0.9467\*Whr\_2023 + 0.0010\*BCR + 0.0047\*BHV + -0.7294\*SVF + 1.4199\*NDVI + -0.0001\*EV + 0.0003\*WR + -0.0084\*Dist\_W + -0.0002\*Dist\_P + 0.0124\*Dist\_M + 0.0002\*W\_BCR + 0.0001\*W\_BHV + 1.7678\*W\_SVF + -1.4593\*W\_NDVI + 0.0003\*W\_EV + -0.0013\*W\_WR + 0.9467\*W\_hr\_2023|

|  model          |**partial SDEM**|
|-----------------|-----------------------------------------------------------------------------------------------|
|equation         |$LST = \beta\_0 + \beta\_1 BCR + \beta\_2 BHV + \beta\_3 NDVI + \beta\_4 SVF + \beta\_5 EV + \beta\_6 WR + \beta\_7 DistWB + \beta\_8 DistGL +\beta\_9 DistMT + \theta\_1 WBCR + \theta\_2 WBHV + \theta\_3 WNDVI + \theta\_4 WSVF + \theta\_5 WEV + \theta\_6 WWR + u, \quad u = \lambda W u +\varepsilon, \varepsilon \sim N(0, \sigma^2 I)$|
|result\_nor\_2016| nor\_2016 = 27.5798 + 0.0203\*BCR + 0.0014\*BHV + 8.4274\*SVF + -21.4460\*NDVI + -0.0114\*EV + -0.1134\*WR + -0.0982\*Dist\_W + -0.0560\*Dist\_P + 0.0172\*Dist\_M + -0.0352\*W\_BCR + -0.0110\*W\_BHV + 3.3434\*W\_SVF + -10.5876\*W\_NDVI + 0.0039\*W\_EV + -0.0394\*W\_WR + 0.8184\*lambda|
|result\_ext\_2016| ext\_2016 = 39.3798 + 0.0326\*BCR + -0.0036\*BHV + 8.9039\*SVF + -38.8294\*NDVI + -0.0148\*EV + -0.1873\*WR + -0.2271\*Dist\_W + -0.0456\*Dist\_P + -0.1242\*Dist\_M + -0.0248\*W\_BCR + 0.0113\*W\_BHV + 4.9234\*W\_SVF + -9.7617\*W\_NDVI + 0.0050\*W\_EV + -0.0444\*W\_WR + 0.8400\*lambda|
|result\_hr\_2016 | hr\_2016 = -11.9287 + -0.0118\*BCR + 0.0056\*BHV + -0.3798\*SVF + 17.4904\*NDVI + 0.0033\*EV + 0.0735\*WR + 0.0882\*Dist\_W + -0.0281\*Dist\_P + 0.1634\*Dist\_M + -0.0085\*W\_BCR + -0.0200\*W\_BHV + -1.7936\*W\_SVF + 0.7076\*W\_NDVI + -0.0013\*W\_EV + 0.0096\*W\_WR + 0.9190\*lambda|
|result\_nor\_2023| nor\_2023 = 37.4249 + 0.0267\*BCR + 0.0033\*BHV + 7.7807\*SVF + -27.5380\*NDVI + -0.0122\*EV + -0.1727\*WR + -0.2168\*Dist\_W + -0.0881\*Dist\_P + 0.0103\*Dist\_M + -0.0011\*W\_BCR + 0.0098\*W\_BHV + 0.9387\*W\_SVF + -2.9640\*W\_NDVI + -0.0021\*W\_EV + -0.0292\*W\_WR + 0.8250\*lambda|
|result\_ext\_2023| ext\_2023 = 40.0883 + 0.0256\*BCR + 0.0006\*BHV + 8.3616\*SVF + -29.4204\*NDVI + -0.0114\*EV + -0.1725\*WR + -0.2396\*Dist\_W + -0.2239\*Dist\_P + -0.1696\*Dist\_M + 0.0193\*W\_BCR + 0.0377\*W\_BHV + 2.9527\*W\_SVF + -4.2769\*W\_NDVI + 0.0026\*W\_EV + -0.0304\*W\_WR + 0.9065\*lambda|
|result\_hr\_2023 | hr\_2023 = -2.5438 + 0.0004\*BCR + 0.0032\*BHV + -0.6424\*SVF + 1.6928\*NDVI + -0.0007\*EV + 0.0005\*WR + 0.0621\*Dist\_W + 0.1557\*Dist\_P + 0.1643\*Dist\_M + -0.0177\*W\_BCR + -0.0218\*W\_BHV + -1.7902\*W\_SVF + 0.7802\*W\_NDVI + -0.0038\*W\_EV + -0.0004\*W\_WR + 0.9537\*lambda|

#### 02 impact

|SDM                               |  SDEM                              |
|----------------------------------|------------------------------------|
|[SDM_effects](csv/SDM_effects.csv)|[SDEM_effects](csv/SDEM_effects.csv)|

#### 03 model performance

|  Year    |   SDM                      |   SDEM    |
|----------|----------------------------|-----------|
|  2016    |[2016 SDM](csv/2016_SDM.csv)|[2016 SDEM](csv/2016_SDEM.csv)|
|  2023    |[2023 SDM](csv/2023_SDM.csv)|[2023 SDEM](csv/2023_SDEM.csv)|

#### 04 model prediction

        y\_trend = (X\_base @ betas).ravel()     # (n\_points,)
        # 7) 样本拟合值（含 λ）：直接用库的 predy（已考虑 λ）
        y\_fitted = model.predy.flatten()       # (N,)
        \*\*\* predy: nx1 array of predicted y values
        

### 07 Residuals Test

|SDM                         |SDEM                         |
|----------------------------|-----------------------------|
|![](excel/SDM_residuals.png)|![](excel/SDEM_residuals.png)|


4 - GBDT model result
---------------------

### 01 - model comparison
|Model        |	Normal_LST	|Extreme_LST	|Resilience_T|
|-------------|-------------|---------------|------------|
|Random Forest|1.190	    |1.192	        |7.046       |
|AdaBoost     |	1.747	    |1.740	        |7.434       |
|GBDT	      |1.173	    |1.170	        |7.066       |
|RNN	      |1.102	    |1.088	        |7.366       |

### 02 - hyperparameter tuning
#### fixed hyperparameter
|Hyperparameter 	|Description	                              |Data range     |Type   |
|-------------------|---------------------------------------------|---------------|-------|
|n_estimators       |number of trees.                             |4168           |Integer|
|min_samples_split  |Minimum number of samples for node splitting.|2              |Integer|

#### hyoeroarameter tuning
|Hyperparameter 	|Description	                            |Data range  	|Type   |
|-------------------|-------------------------------------------|---------------|-------|
|learning_rate	    |Step size of each boosting step.        	|(0.002, 0.355) |Real   |
|subsample	        |Subsample ratio of training instance.   	|[0.545,0.958]	|Real   |
|max_depth	        |Maximum tree depth.	                    |[5, 13]	    |Integer|
|max_features	    |Subsample ratio of features for training.	|[0.335,0.916]	|Real   |

### 03 - cv result
|Workflow                                              |
|------------------------------------------------------|
| ![](fig/workflow_1.jpg                        )      |
| ![](fig/workflow_2.jpg                        )      |
|First: we use 20 random seeds to calculate the result.|
|Second: for each seeds, split the data into 80-20 train-test sets|
|using K-folder cross validation. split into 5 set, and 20 repeated, randsom seed = 0|
|using the GBDT with the spercific hyperparameters. and for n_iterration = 200, using random search to find out the best hyperparameters.|

result 
|type   |learning_ rate	|subsample	|max_ depth|	max features|	Seed count|	Mean CV score|Final seed|Final seed CV score|
|-------|---------------|-----------|----------|----------------|-------------|--------------|----------|-------------------|
|Nor_LST|0.0137	        |0.5634	    |5	       |0.6022 	        |20	          |0.9200        |3184      |0.9200             |
|Ext_LST|0.0137	        |0.5634	    |5	       |0.6022	        |20		      |0.9486        |9394      |0.9486             |
|HR     |0.0110	        |0.5651	    |5	       |0.8360	        |11		      |0.9161        |3497      |0.9164             |
|HR     |0.0110	        |0.5651	    |5	       |0.8360	        |11		      |0.9161        |3497      |0.9164             |
|HR     |0.0196	        |0.5570	    |5	       |0.7118	        |1 		      |0.9161        |3497      |0.9164             |

### 04 - best hyperparameter

|    |GBDT random search result                   |
|----|--------------------------------------------|
|2016| [](excel/final_GBDT_summary_results.xlsx)  |
|2023|    None                                    |

|type   |learning_ rate	|subsample	|max_ depth	|max features	|Final seed	|Final seed CV score	|Final Test score|
|-------|---------------|-----------|-----------|---------------|-----------|-----------------------|----------------|
|Nor_LST|0.0137	        |0.5634 	|5	        |0.6022	        |3184	    |0.9200	                |0.9237          |
|Ext_LST|0.0137	        |0.5634 	|5	        |0.6022	        |9394	    |0.9486	                |0.9538          |
|HR     |0.0110         |0.5651     |5          |0.8360	        |3497	    |0.9161	                |0.9253          |

### 05 - PDP result compared with GBDT and SDEM

![](fig/111.png)
↑ This is the final PDP I decide. I finally decide the final method.

|predict|![](fig/GBDT_SDEM%20effect.jpg)|
