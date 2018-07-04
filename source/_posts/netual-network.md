---
title: netual network
date: 2018-07-03 20:31:41
tags: deep learning
categories: paper reading
mathjax: true
---
## 神经网络学习
---
### 代价函数
- 二次代价函数
    * $$c=\frac{(y-a)^2}{2}$$
    * 人工神经元在犯错比较明显的时候学习速率很慢
    * 求导 $$\frac{\partial C}{\partial w}=(a-y)\sigma'(z)x$$ $$\frac{\partial C}{\partial b}=(a-y)\sigma'(z)$$
- 交叉熵代价函数
    * $$c=-\frac{1}{n}\sum_{x}{y\ln{a}+(1-y)\ln(1-a)}$$
    * 每一项都是非负，对数函数定义域a的范围是(0,1)
    * 目标值和真实值很近时，代价趋近于0
    * $$\sigma'(z)=\sigma(z)(1-\sigma(z))$$
     * 求导 $$\frac{\partial C}{\partial w_j} = \frac{1}{n}\sum_x x_j(\sigma(z)-y)$$ $$\frac{\partial C}{\partial b} = \frac{1}{n}\sum_x (\sigma(z)-y)$$
     * 从导数中可以看到学习速率加快
    
---
### 过拟合
- 四个参数可以画出来一头大象，五个参数能让他卷鼻子（Joke :D）
- 代价函数持续减小但是测试分类正确率波动稳步不前，单纯记忆训练集合，没有对数字本质进行理解，泛化到整个测试数据集上
- 检测过拟合的明显方法：测试集的准确率随训练变化不再提升。
- traing_data test_data validation_data: 在每个迭代周期之后都计算模型在validation_data的准确率，防止饱和。(hold out 方法)
