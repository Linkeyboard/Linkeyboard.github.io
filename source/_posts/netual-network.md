---
title: netual_network
date: 2018-07-03 20:31:41
tags: deep learning
categorites: paper reading
mathjax: true
---
## 神经网络学习
---
### 代价函数
- 二次代价函数
    * $$c=\frac{(y-a)^2}{2}$$
- 交叉熵代价函数
    * $$c=-\frac{1}{n}\sum_{x}{y\ln{a}+(1-y)\ln(1-a)}$$
    * 每一项都是非负，对数函数定义域a的范围是(0,1)
    * 目标值和真实值很近时，代价趋近于0
    * 
