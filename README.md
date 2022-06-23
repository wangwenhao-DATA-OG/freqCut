# pycutter
一个等频切割数列的包，通过最小化每组的实际频率与每组期望频率的残差平方和来寻找一个最优的离散组合切割方法
# 安装
pip install pycutter

# 功能
1.支持左闭右开  
2.对比现有包，该包对首尾临界值做了合理的处理，如最后一组为右闭  
3.通过最小化每组的频率与期望频率的残差平方和来寻找一个最优的离散组合切割方法  
4.因为是最优的离散组合切割，所以对偏斜和集中的数据也给出了合理的切割方法  
5.计算出的切割方法用于另一组数列时，可以按照新的数列自动扩展最大和最小值  
6.支持带有权重的数列

# 示例
```
from pycutter.cutter import freq_cut,cut_by_bins
import numpy as np
if __name__=='__main__':
    data = [4,2,6,np.nan,2,2,None,3,4,10,3,7,1,1,10,5,4,7,7,8,10,8,8,9,10,10]
    weight = None
    threshold_distr=0.2          
    min_distr = 0.1
    bins = freq_cut(data,threshold_distr,min_distr,weight)
    print(bins)
    #最小值由1变为-1，最大值由10变为11
    data = [4,2,11,np.nan,-1,2,None,3,4,10,3,7,1,1,11,5,4,7,7,8,10,8,8,9,10,10]
    #返回的分组方法自动扩展了最大和最小值，第一组变为[-1.0,3)，最后一组变为[10,11.0]
    data_bin = cut_by_bins(data,bins)
    print(data_bin)
```

# API
## value_counts_weight(data,weight=None)  
类似pandas中的value_counts，但是支持权重  
data:需要被等频分组的数列.array_like  
weight:data中每个数据的权重，如果权重相等则为None，默认为None。array_like  
return  
data每个取值的占比。pd.Series  

## freq_cut(data,threshold_distr,min_distr,weight=None)  
等频分组  
data:需要被等频分组的数列.array_like  
threshold_distr:当小于1时，每个组期望的占比。当大于1时，分成多少组  
min_distr:由于数据可能偏斜或集中，所以无法严格遵守threshold_distr，此时你所能接受的最小的分组占比  
weight:与data对应的权重。array_like  
return  
bins:分组信息。例如 '[1,3), [3,5), [5,8), [8,10), [10,10]'。str  

## cut_by_bins(data,bins)  
按照指定的bins来对data进行分组  
data:需要被分组的数列.array_like  
bins:分组信息。例如 '[1,3), [3,5), [5,8), [8,10), [10,10]'。注意：两个组之间的分割为逗号+空格，一个组内的两个界值仅用逗号分割，不要加空格，必须严格遵守格式。str  
return  
data的每个值所在的组。pd.Series  
