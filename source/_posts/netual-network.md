---
title: netual network
date: 2018-07-03 20:31:41
tags: deep learning
categories: paper reading
mathjax: true
---
## 神经网络学习
---
<!-- more -->
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
- 代价函数持续减小但是测试分类正确率波动稳步不前，`单纯记忆训练集合，没有对数字本质进行理解，泛化到整个测试数据集上`
- 检测过拟合的明显方法：`测试集的准确率随训练变化不再提升。`
- traing_data test_data validation_data: 在每个`迭代周期`之后都计算模型在validation_data的准确率，防止饱和。(hold out 方法)
- 防止过拟合的方法
    * 增加训练数据规模
    * hold out, validation data验证每个周期后的准确率
    * 规范化
    * 弃权？

---
### 规范化
- L2规范化
	* $$C=C_0+\frac{\lambda}{2n}\sum_w w^2$$
    * 也叫权重衰减，直觉上选择较小的权重。（奥卡姆剃刀原则，选择更简单的解释）。`学习局部噪音更加困难，不会因为输入的改变发生较大的变化，对经常出现的证据进行反应，可以抵抗噪音。`
    * $$\frac{\partial C}{\partial w}=\frac{\partial C_0}{\partial w}+\frac{\lambda}{n}w$$
- L1规范化
     * $$C=C_0+\frac{\lambda}{n}\sum_w |w|$$
     * $$\frac{\partial C}{\partial w}=\frac{\partial C_0}{\partial w}+\frac{\lambda}{n}sign(w)$$
     * 当w绝对值很大时，L2规范化权重缩小多，当w绝对值很小时，L1规范化权重缩小多。最终的结果是，L1规范化倾向于`聚集网络权重在相对少量的高重要度连接上。`
     
- 弃权
 	* 弃权不改变损失函数，改变网络结构。每次训练隐藏一半的神经元，输入输出层不变，反向传播修改参数。
 	* 运行整个网络时，相当于两倍的隐藏神经元被激活，为了补偿这个，将隐藏神经元的权重减半。
 	* 相当于两（多）个网络叠加到一起。
- 人为扩展训练数据
 	*  旋转
 	* 弹性扭曲
---
### 权重初始化
&emsp;假设训练使用1000个输入神经元，权重初始化为均值为0，标准差为1的高斯分布，其中500个输入神经元的值为0，500个为1。$z=\sum_j w_j x_j + b$,剩下500个权重项和1个额外的偏置项。所以z本身是一个均值为0,方差为$\sqrt{501}$的高斯分布,因此使用均值为0，标准差为$\sqrt{n_{in}}$的高斯分布来初始化`权重`，容易证明z是均差为0，标准差为$\sqrt{\frac{3}{2}}$的高斯分布。这样z集中分布在-1~1附近。
Tips:
- 独立随机变量和的方差是每个随机独立变量的方差之和
- 方差是标准差的平方。
