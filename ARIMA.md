# ARIMA model
时间序列(Time Series)模型，差分整合移动平均自回归模型(Autoregressive Integrated Moving Average model)  

## 知识要点
### ARIMA
AR是"自回归"，p为自回归项数；MA为"滑动平均"，q为滑动平均项数，d为使之成为平稳序列所做的差分次数（阶数）。  

### ADF检验(Augmented Dickey–Fuller Test)
测试一个自回归模型是否存在单位根(unit root)，单位根存在则说明时间序列不平稳。ADF通过纳入一阶向下差分项，排除了自相关的影响。  
ADF的检验统计量总为一个负数，越小代表越平稳。  

### 自相关函数(ACF, Autocorrelation Function)
一个信号于其自身在不同时间点的互相关。非正式地来说，就是两次观察之间的相似度对它们之间的时间差的函数。  
用于找出重复模式（如被噪声掩盖的周期信号），或识别隐含在信号谐波频率中消失的基频。  
计算所得的自相关值R的取值范围为[-1,1],1为最大正相关值，-1则为最大负相关值，0为不相关。 

## 模型运用流程 
1. 根据时间序列的散点图、自相关函数和偏自相关函数图识别其平稳性。
2. 对非平稳的时间序列数据进行平稳化处理。直到处理后的自相关函数和偏自相关函数的数值非显著非零。
3. 根据所识别出来的特征建立相应的时间序列模型。平稳化处理后，若偏自相关函数是截尾的，而自相关函数是拖尾的，则建立AR模型；若偏自相关函数是拖尾的，而自相关函数是截尾的，则建立MA模型；若偏自相关函数和自相关函数均是拖尾的，则序列适合ARMA模型。
4. 参数估计，检验是否具有统计意义。
5. 假设检验，判断（诊断）残差序列是否为白噪声序列。
6. 利用已通过检验的模型进行预测。

![以上步骤引自维基百科](https://zh.wikipedia.org/wiki/ARIMA%E6%A8%A1%E5%9E%8B)

## Python求解
### ADF检验
statsmodels.tsa.stattools.adfuller  
x (array_like, 1d)数据序列  
返回：  
adf (float)检验统计量  
pvalue (float)p值  
usedlag (int)使用的滞后量  
nobs (int)用于ADF检验和临界值计算的数据量  
critical values (dict)位于1%,5%和10%水平的检验统计量对应的临界值  

### 自相关函数计算
statsmodels.tsa.stattools.acf  
x 时间序列数据  
fft bool是否采用FFT计算方法，推荐长时间序列使用  
nlags 自相关的滞后数  
返回不同滞后数下的自相关函数值  

## 参考资料
* ![维基百科-迪基-福勒检验](https://zh.wikipedia.org/wiki/%E8%BF%AA%E5%9F%BA-%E7%A6%8F%E5%8B%92%E6%A3%80%E9%AA%8C)
* ![维基百科-ARIMA模型](https://zh.wikipedia.org/wiki/ARIMA%E6%A8%A1%E5%9E%8B)
* ![statsmodel相关模块文档](http://www.statsmodels.org/stable/py-modindex.html)
* ![A comprehensive beginner’s guide to create a Time Series Forecast (with Codes in Python)](https://www.analyticsvidhya.com/blog/2016/02/time-series-forecasting-codes-python/)
