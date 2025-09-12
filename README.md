
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

1 - Air condition and AT (apperant temperature)
-----------------------------------------------

首先选取气象站。最终我们只选了龙山气象站 ID = 108

![20160924](excel/air station select.png)

可以看到2023年的6.16不是典型 normal heat day，在图像中前2天下了暴雨。

然而，由于所有有效数据都不太好↓

![其他天情况](excel/其他天情况.png) 
![其他天情况](excel/weather_condition_on_pairs.png) 
Final pairs : 20160924 vs 20160807  
Final pairs : 20230616 vs 20230819

2 - Extreme heat days and normal heat days identification
--------------------------
|Extreme heat day|
|----------------|
|Extreme heat is defined as a period during which the apparent temperature exceeds 33°C for at least two consecutive days, or when rapid temperature increases or prolonged heatwaves are expected to result in significant or widespread harm, based on the Korea Meteorological Administration, KMA.|
|Criteria are based on duration, intensity, and potential damage.|
|https://www.kdca.go.kr/contents.es?mid=a20205050300|
|**Summer identification**: in Korea, the summer begins in May and end in September. Therefore, when we think about the percentile of the apparent temperature, we need use the 5-9 as the data source.|


3 - LST data and variables
--------------------------

### 01 statistics of data based on Grid 120, 240, 480 m

|2016 (G = 120,240,480)|2023 (G = 120,240,480)|
|----------------------|----------------------|
|                      |                      |
### 02 Comparison of LST data (original)

| Year | Normal heat day (AT, Percentile, Non Cloud Ratio, LOCAL_TIME) | Extreme heat day (AT, Percentile, Non Cloud Ratio, LOCAL_TIME) | Heat Resilience (Valid Area Ratio)          |
|------|--------------------------------------------------------------------|---------------------------------------------------------------------|-----------------------------------------|
| 2016 | ![](fig/20160924.jpg) 27.7, 47.51, 99.46%, 11:11:17 | ![](fig/20160807.jpg) 34.3, 97.47, 82.53%, 11:11:04               | ![](fig/2016_HR.jpg) 82.10%            |
| 2023 | ![](fig/20230616.jpg) 28.4, 53.95, 99.00%, 11:10:22  |  ![](fig/20230819.jpg) 33.7, 95.68, 72.66%, 11:10:50                | ![](fig/2023_HR.jpg)  72.25%           |


### 03 Comparison of LST data based on Grid 480m(2016, 2023)

| Year | Normal heat day (Fig, Date)         | Extreme heat day (Fig, Date)        | Heat Resilience (Fig, Date)   |
|------|-------------------------------------|-------------------------------------|-------------------------------|
| 2016 | ![](fig/G480_nor2016.jpg)<br>20160924 | ![](fig/G480_ext2016.jpg)<br>20160807 | ![](fig/G480_hr2016.jpg)<br>2016 HR |
| 2023 | ![](fig/G480_nor2023.jpg)<br>20230616 | ![](fig/G480_ext2023.jpg)<br>20230819 | ![](fig/G480_hr2023.jpg)<br>2023 HR |

### 04 Comparison of Features data (2016,2023)

| Features| 2016                            | 2023                            |
|---------|---------------------------------|---------------------------------|
| BCR     | ![](fig/G480_V_BCR.jpg)         | ![](fig/G480_V_BCR_2023.jpg)    |
| BHV     | ![](fig/G480_V_BHV.jpg)         | ![](fig/G480_V_BHV_2023.jpg)    |
| SVF     | ![](fig/G480_V_SVF.jpg)         | ![](fig/G480_V_SVF_2023.jpg)    |
| NDVI    | ![](fig/G480_V_NDVI.jpg)        | ![](fig/G480_V_NDVI_2023.jpg)   |
| EV      | ![](fig/G480_V_EV.jpg)          | ![](fig/G480_V_EV_2023.jpg)     |
| WR      | ![](fig/G480_V_WR.jpg)          | ![](fig/G480_V_WR_2023.jpg)     |
| Dist_P  | ![](fig/G480_V_DistP.jpg)       | ![](fig/G480_V_DistP_2023.jpg)  |
| Dist_M  | ![](fig/G480_V_DistM.jpg)       | ![](fig/G480_V_DistM_2023.jpg)  |
| Dist_W  | ![](fig/G480_V_DistW.jpg)       | ![](fig/G480_V_DistW_2023.jpg)  |

3 - spatial regression model result
-----------------------------------

### 00 correlation test

| Year | Pearson correlation            | Spearman correlation            | VIF score                     |
|------|--------------------------------|---------------------------------|-------------------------------|
| 2016 | ![](fig/2016_pearson_corr.png) | ![](fig/2016_spearman_corr.png) | ![](excel/2016_VIF_score.png) |
| 2023 | ![](fig/2023_pearson_corr.png) | ![](fig/2023_spearman_corr.png) | ![](excel/2023_VIF_score.png) |

### 00 error information

|      | 2016                                                      | 2023                              |
|------|-----------------------------------------------------------|-----------------------------------|
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
| 2016 | Normal heat day   | 211.3825     | 6.859e-48    | 1652.8657      | 0            | 16.2983              | 5.411e-05      | 1457.7815              | 0              |
| 2016 | Extreme heat day  | 248.7858     | 4.777e-56    | 1839.4007      | 0            | 30.7201              | 2.981e-08      | 1621.3350              | 0              |
| 2016 | Heat resilience   | 706.0370     | 1.455e-155   | 2894.3563      | 0            | 6.0687               | 0.01376        | 2194.3880              | 0              |
| 2023 | Normal heat day   | 261.6000     | 7.687e-59    | 1403.3011      | 4.028e-307   | 22.6666              | 1.927e-06      | 1164.3677              | 3.383e-255     |
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

LR test score

model performance

### 05 AIC/BIC

explanatory\_vars =  
\['BCR', 'BHV', 'SVF', 'NDVI', 'EV', 'WR', 'Dist\_W', 'Dist\_P', 'Dist\_M'\]

explanatory\_vars =  
\['BCR', 'BHV', 'SVF', 'NDVI', 'EV', 'WR'\]

\--> it's clear that SDEM (SDM) has the best performance.

### 06 final model select: partial SDEM ( partial SDM)

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

|model  |**partial SDEM**                                                                                                    |
|-------|--------------------------------------------------------------------------------------------------------------------|
|equation|$LST = \beta\_0 + \beta\_1 BCR + \beta\_2 BHV + \beta\_3 NDVI + \beta\_4 SVF + \beta\_5 EV + \beta\_6 WR + \beta\_7 DistWB + \beta\_8 DistGL +\beta\_9 DistMT + \theta\_1 WBCR + \theta\_2 WBHV + \theta\_3 WNDVI + \theta\_4 WSVF + \theta\_5 WEV + \theta\_6 WWR + u, \quad u = \lambda W u +\varepsilon, \varepsilon \sim N(0, \sigma^2 I)$|
|result\_nor\_2016| nor\_2016 = 27.5798 + 0.0203\*BCR + 0.0014\*BHV + 8.4274\*SVF + -21.4460\*NDVI + -0.0114\*EV + -0.1134\*WR + -0.0982\*Dist\_W + -0.0560\*Dist\_P + 0.0172\*Dist\_M + -0.0352\*W\_BCR + -0.0110\*W\_BHV + 3.3434\*W\_SVF + -10.5876\*W\_NDVI + 0.0039\*W\_EV + -0.0394\*W\_WR + 0.8184\*lambda|
|result\_ext\_2016| ext\_2016 = 39.3798 + 0.0326\*BCR + -0.0036\*BHV + 8.9039\*SVF + -38.8294\*NDVI + -0.0148\*EV + -0.1873\*WR + -0.2271\*Dist\_W + -0.0456\*Dist\_P + -0.1242\*Dist\_M + -0.0248\*W\_BCR + 0.0113\*W\_BHV + 4.9234\*W\_SVF + -9.7617\*W\_NDVI + 0.0050\*W\_EV + -0.0444\*W\_WR + 0.8400\*lambda|
|result\_hr\_2016| hr\_2016 = -11.9287 + -0.0118\*BCR + 0.0056\*BHV + -0.3798\*SVF + 17.4904\*NDVI + 0.0033\*EV + 0.0735\*WR + 0.0882\*Dist\_W + -0.0281\*Dist\_P + 0.1634\*Dist\_M + -0.0085\*W\_BCR + -0.0200\*W\_BHV + -1.7936\*W\_SVF + 0.7076\*W\_NDVI + -0.0013\*W\_EV + 0.0096\*W\_WR + 0.9190\*lambda|
|result\_nor\_2023| nor\_2023 = 37.4249 + 0.0267\*BCR + 0.0033\*BHV + 7.7807\*SVF + -27.5380\*NDVI + -0.0122\*EV + -0.1727\*WR + -0.2168\*Dist\_W + -0.0881\*Dist\_P + 0.0103\*Dist\_M + -0.0011\*W\_BCR + 0.0098\*W\_BHV + 0.9387\*W\_SVF + -2.9640\*W\_NDVI + -0.0021\*W\_EV + -0.0292\*W\_WR + 0.8250\*lambda|
|result\_ext\_2023| ext\_2023 = 40.0883 + 0.0256\*BCR + 0.0006\*BHV + 8.3616\*SVF + -29.4204\*NDVI + -0.0114\*EV + -0.1725\*WR + -0.2396\*Dist\_W + -0.2239\*Dist\_P + -0.1696\*Dist\_M + 0.0193\*W\_BCR + 0.0377\*W\_BHV + 2.9527\*W\_SVF + -4.2769\*W\_NDVI + 0.0026\*W\_EV + -0.0304\*W\_WR + 0.9065\*lambda|
|result\_hr\_2023| hr\_2023 = -2.5438 + 0.0004\*BCR + 0.0032\*BHV + -0.6424\*SVF + 1.6928\*NDVI + -0.0007\*EV + 0.0005\*WR + 0.0621\*Dist\_W + 0.1557\*Dist\_P + 0.1643\*Dist\_M + -0.0177\*W\_BCR + -0.0218\*W\_BHV + -1.7902\*W\_SVF + 0.7802\*W\_NDVI + -0.0038\*W\_EV + -0.0004\*W\_WR + 0.9537\*lambda|

explanatory\_vars = \['BCR', 'BHV', 'SVF', 'NDVI', 'EV', 'WR', 'Dist\_W', 'Dist\_P', 'Dist\_M'\]
explanatory\_vars\_clean =\['BCR', 'BHV', 'SVF', 'NDVI', 'EV', 'WR'\] 
X-> explanatory\_vars; WX -> explanatory\_vars\_clean

#### 02 impact

|SDM                |  SDEM                   |
|-------------------|-------------------------|
|                   |                         |

#### 03 model performance

|  Year    |   SDM       |   SDEM    |
|----------|-------------|-----------|
|  2016    |             |           |
|  2023    |             |           |

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

还没做完 # xxx

### 02 - hyperparameter tuning

codes

      # 这个代码干的事情其实就是用梯度提升回归树 (GradientBoostingRegressor, GBDT) 对地理网格数据建模，并做特征重要性分析 + 偏依赖图 (PDP) 提取。
    import geopandas as gpd
    import pandas as pd
    import numpy as np
    import os
    import re
    import matplotlib.pyplot as plt
    from sklearn.ensemble import GradientBoostingRegressor
    from sklearn.metrics import r2_score, mean_squared_error
    from sklearn.model_selection import RandomizedSearchCV
    from sklearn.inspection import PartialDependenceDisplay
    from sklearn.model_selection import train_test_split
    from sklearn.model_selection import RepeatedKFold
    from scipy.stats import randint, uniform, loguniform

    # 文件夹路径
    grid_folder = r'D:\seoul\grids\lst_map\final_clean\480_based'
    def run_gbdt(grid_folder, years=[2016, 2023], n=0):
        for year in years:
            target_vars = [f'nor_{year}', f'ext_{year}', f'hr_{year}']
            explanatory_vars = ['BCR', 'BHV',  'SVF', 'NDVI', 'EV', 'WR', 'Dist_W', 'Dist_P', 'Dist_M','X','Y'] # 顺序很讲究
            # 保存结果
            all_results = []
            pdp_records = []
            r2_comparison = []

            param_dist = {
                'n_estimators': [4168], #4168
                'learning_rate': loguniform(0.002, 0.355), #(0.002, 0.355)
                'subsample': uniform(0.545, 0.413), # [0.545,0.958]
                'max_depth' : randint(5, 14), # [5, 13]
                'min_samples_split':[2], #2
                'max_features': uniform(0.335, 0.581), #[0.335,0.916]
                }

            # === 主循环 ===
            for filename in os.listdir(grid_folder):
                if filename.endswith(f'city{year}_lst_ratio_grid_480m_bcr_bhv_ndvi_svf_ev_distbp_distmt_distwb_wr_xy.shp'):
                    input_path = os.path.join(grid_folder, filename)
                    match = re.search(r'(\d{3,5})m', filename)
                    grid_size = match.group(1)

                    gdf = gpd.read_file(input_path)
                    gdf_clean = gdf.replace([np.inf, -np.inf], np.nan).dropna(subset=target_vars + explanatory_vars)

                    for target in target_vars:
                        X = gdf_clean[explanatory_vars]
                        y = gdf_clean[target]

                        #####################################################################
                        # 用20个 random seeds
                        np.random.seed(0)  # 固定种子以便复现
                        random_seeds = np.random.choice(10000, size=20, replace=False)
                        n = n
                        #####################################################################
                        for r in [random_seeds[n]]:

                            # 数据划分
                            X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=r) # 切20次

                            gbdt = GradientBoostingRegressor(random_state=0)
                            
                            cv = RepeatedKFold(n_splits=5, n_repeats=20, random_state=0)

                            search = RandomizedSearchCV(
                                estimator=gbdt,
                                param_distributions=param_dist,
                                n_iter= 200,
                                scoring='r2',
                                cv=cv, # cross validation
                                verbose=3,
                                n_jobs=-1,
                                random_state=0
                            )

                            search.fit(X_train, y_train)
                            folder = os.path.join(grid_folder, r'Machine Learning')
                            os.makedirs(folder, exist_ok=True)

                            # 先保存一份 checkpoint (CSV，追加方式)
                            checkpoint_path = os.path.join(folder, f"{year}_{target}_seed{n}_checkpoint.csv")
                            df_cv = pd.DataFrame(search.cv_results_)
                            if not os.path.exists(checkpoint_path):
                                df_cv.to_csv(checkpoint_path, index=False)
                            else:
                                df_cv.to_csv(checkpoint_path, mode='a', header=False, index=False)
                            print(f"[SAVE] checkpoint -> {checkpoint_path}")

                            # 再保存原本的 Excel（完整結果）
                            cv_path = os.path.join(folder, f"{n}_{r}_GBDT_{target}_cv_results.xlsx")
                            try:
                                df_cv.to_excel(cv_path, index=False)
                                print(f"[SAVE] CV results -> {cv_path}")
                            except Exception as e:
                                print(f"[ERROR] Save CV results failed: {cv_path} | {e}")
                                raise

                            # 使用测试集评估
                            best_model = search.best_estimator_
                            y_train_pred = best_model.predict(X_train)
                            y_test_pred = best_model.predict(X_test)

                            r2_train = best_model.score(X_train, y_train)
                            r2_test = r2_score(y_test, y_test_pred)

                            rmse_train = np.sqrt(mean_squared_error(y_train, y_train_pred))
                            rmse_test = np.sqrt(mean_squared_error(y_test, y_test_pred))

                            print(f" {filename} | {target} 最佳参数: {search.best_params_} | R²_train={r2_train:.3f} | R²_test={r2_test:.3f}")

                            for var, importance in zip(explanatory_vars, best_model.feature_importances_):
                                all_results.append({
                                    'GridSize': grid_size,
                                    'Target': target,
                                    'Feature': var,
                                    'Random seed':r,
                                    'FeatureImportance_TrainModel': round(importance, 4),
                                    'Train_R2': round(r2_train, 4),
                                    'Train_RMSE': round(rmse_train, 4),
                                    'Test_R2': round(r2_test, 4),
                                    'Test_RMSE': round(rmse_test, 4),
                                    **search.best_params_
                                })
                                r2_comparison.append({
                                    'GridSize': grid_size,
                                    'Target': target,
                                    'Random seed':r,
                                    'Train_R2': round(r2_train, 4),
                                    'Test_R2': round(r2_test, 4),
                                    'Train_RMSE': round(rmse_train, 4),
                                    'Test_RMSE': round(rmse_test, 4)
                                })
                    

                    # 保存模型训练后的结果
                    df_all = pd.DataFrame(all_results)

                    df_all.to_excel(os.path.join(folder, f'{year} GBDT_Random_Search_Results_{target}_{n}_{r}.xlsx'), index=False)
                    df_r2 = pd.DataFrame(r2_comparison)
                    df_r2 = df_r2.sort_values(['Target', 'GridSize'])
                    df_r2.to_excel(os.path.join(folder, f'{year} R2_Comparison_Train_vs_Test_{target}_{n}_{r}.xlsx'), index=False)
                    # print("✅ R² train vs test comparison saved.")

    run_gbdt(grid_folder, years=[2016, 2023], n=10)

### 03 - cv result

|Year|normal heat day|extreme heat day|heat resilience|
|----|---------------|----------------|---------------|
|2016|               |                |               |
|2023|               |                |               |

### 04 - best hyperparameter

|    |GBDT random search result|
|----|-------------------------|
|2016|                         |
|2023|                         |

![](excel/GBDT R².png)

### 05 - predict result with SDEM

|predict|![](fig/predict.jpg)|
|PDP    |![](fig/PDP.jpg)    |
