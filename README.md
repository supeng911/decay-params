# 网站热度排序参数记录：

## 具体参数

衰减函数： function decay();

### 日记参数

热度值计算：（trending_num）

数值类型 | 权重值 | 是否更新数据
---|---|---
浏览数量 | 0.05 | false
回复数量 | 0.3 | true
点赞数量 | 0.1 | true
收藏数量 | 0.45 | true
分享数量 | 0.1 | true

热度值时间衰减参数：（trending_at）

数值类型 | 值 
---|--- 
offset | 12 
scale | 72.0
decay | 0.5；

>  热度值在12小时内不衰减，纯靠热度值排序， 在（72 + 12） 小时，即3.5天时衰减到0.5

文章时间衰减参数： （时间：examine_at）

数值类型 | 值 
---|--- 
offset | 24 
scale | 144.0
decay | 0.5；

>  文章在24小时内不衰减， 在（144 + 24）小时，即一周时衰减到0.5

具体计算： 

trending_score = log(trending_num + 2) * decay(trending_at) (网站中值的范围在0.33 ~ 4) 

document_value = trending_score * 0.1 + decay(examine_at) * 0.4


### 问答参数


热度值计算：（trending_num）

数值类型 | 权重值 | 是否更新数据
---|---|---
浏览数量 | 0.05 | false
回答数量 | 0.4 | true
关注数量 | 0.55 | true

热度值时间衰减参数：（trending_at）

数值类型 | 值 
---|--- 
offset | 12 
scale | 72.0
decay | 0.5；

>  热度值在12小时内不衰减，纯靠热度值排序， 在（72 + 12） 小时，即3.5天时衰减到0.5

问题时间衰减参数： （时间：examine_at）

数值类型 | 值 
---|--- 
offset | 3 
scale | 12.0
decay | 0.5；

>  文章在3小时内不衰减， 在（12+3）小时衰减到0.5

具体计算： 

trending_score = log(trending_num + 2) * decay(trending_at) (网站中值的范围在0.33 ~ 4) 

document_value = trending_score * 0.1 + decay(examine_at) * 0.4


## 函数计算的一些问题

1. 函数计算时会出现数字范围超限（Value out of Range: Underflow）， 所以修改分母最大值为500，应为500对应的结果 趋近于0 ，如果超过500，直接返回0；
2. 将分子中的log函数改为ln函数，否则无法计算出实例中的结果。
3. 应数据跟新较慢，所有的时间单位改为小时，即 timestamp 值除以 3600，时区以东八区为基准。** 切记除以3600 **


## 日记排序参数

1. 增加 trending_at 记录热度值更新的时间，初始化 trending_at = updated_at
2. 增加 trending_num
3. 增加 examine_at , 初始化 examine_at = updated_at
4. 程序将 所有更新 updated_at 的地方去掉，只保留作者更新文章时更新 updated_at。
5. examine_at 用于手工调整排序，后台增加刷新功能，更新此字段时间。衰减函数的时间参数以examine_at 为准。

## 问答排序参数

1. 增加 trending_at 记录热度值更新的时间，初始化 trending_at = updated_at
2. 增加 trending_num
3. 增加 examine_at , 初始化 examine_at = updated_at
4. 程序将 所有更新 updated_at 的地方去掉，只保留作者更新文章时更新 updated_at。
5. examine_at 用于手工调整排序，后台增加刷新功能，更新此字段时间。衰减函数的时间参数以examine_at 为准。