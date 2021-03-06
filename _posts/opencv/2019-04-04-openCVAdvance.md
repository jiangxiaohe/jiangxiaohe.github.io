---
layout: post
title: 图像特征-harris、SIFT
tags:
- openCV
categories: opencv
description: openCV 进阶操作
---

[TOC]

# Harris角点检测

`cv2.cornerHarris(gray, 2, 3, 0.04)`
* 找出图像中的角点
* 参数说明：gray表示输入的灰度图，2表示进行角点移动的卷积框，3表示后续进行梯度计算的sobel算子的大小，0.04表示角点响应R值的α值

图像分类：平面、边界、角点。使用一个3*3的卷积框，在图上的每一点进行平移操作，对于当前位置，平移一个像素点后，考察平移前后两个图像像素点的差值。角点的水平和竖直变化都很明显，角点含有图像更多的信息。
* 平面:不变
* 边界:一方向不变，一方向变化迅速
* 角点:两个方向变化迅速。

对I(x+dx,y+dy)使用一阶泰勒展开，可以得到差值为一个斜椭圆方程

$$
c(x,y,dx,dy) = \sum (I_x*dx+I_y*dy)^2 = [dx,dy] * M(x,y) * [dx,dy]^T
$$

$$
M[x,y] = \sum {\left[
\begin{matrix}
{I_x(x,y)^2} & {I_x(x,y)I_y(x,y)}\\
{I_x(x,y)I_y(x,y)} & {I_y(x,y)^2}
\end{matrix}
\right]}
$$

对M[x,y]进行特征变换，获得特征值λ1和λ2，即最终的表示为 $λ_1*∆x^ 2 + λ_2*∆y ^ 2$

即对斜椭圆做了一个变化，使得其是一个正椭圆，结果不变

根据λ1和λ2的对比，可以知道xy两个方向的像素变化情况，因此可以判断是否是边界或角点。

```python
# cv2.cornerHarris()
import cv2

# 进行角点检测
# 第一步：读取图片
img = cv2.imread('test_1.jpg')
# 第二步:进行灰度化
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
# 第三步：进行角点检测
dst = cv2.cornerHarris(gray, 2, 3, 0.04)
# 第四步：进行画图秒点操作
img[dst > 0.01*dst.max()] = (0, 0, 255)
cv2.imshow('img', img)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

# Scale Invariant Feature Transform（SIFT）
尺度不变特征变换，最常用的算法。

SIFT函数在python在3.4.3及以上加入了专利保护，所以用的话要把opencv版本降低。

* `sift = cv2.xfeatures2d.SIFT_create()`
	- sift为实例化的sift函数

* `kp = sift.detect(gray, None)`
	- 检测图中的关键点
	- kp表示生成的关键点，gray表示输入的灰度图，

* `ret = cv2.drawKeypoints(gray, kp, img)`
	- 在图中画出关键点
	- gray表示输入图片, kp表示关键点，img表示输出的图片

* `kp, dst = sift.compute(img,kp)`
	- 计算关键点对应的sift特征向量
	- kp表示输入的关键点，dst表示输出的sift特征向量，通常是128维的

---

第一步：进行高斯模糊，获得不同模糊度的图片

在一定的范围内，无论物体是大还是小，人眼都可以分辨出来，然而计算机要有相同的能力却很难，所以要让机器能够对物体在不同尺度下有一个统一的认知，就需要考虑图像在不同的尺度下都存在的特点。

尺度空间的获取通常使用**高斯模糊**来实现。

---

第二步：构造多分辨率金字塔，多分辨金字塔的构造直接使用降采样不需要模糊的操作，这里可以使用平均降采样。

---

第三步：构造高斯差分金字塔，金字塔每一层的五站图片作为原始图片经过不同σ参数模糊后获得的图。将五张图进行上下的相减操作，获得右边的差分图。

---

第四步：对获得的高斯差分金字塔DoG，查找极值点，对于一个点是否是极值点，将其上面的一幅图对应的9个点+下面的一幅图对应的9个点，加上周围的8个点，判断这个点是否是极值点

---

第五步：如果是极值点，即为关键点。存在的问题是这些关键点都是局部极值点，且均为离散点。精切定位关键点的一种方法是对尺度空间DoG进行曲线拟合，计算其极值点，从而实现关键点的精确定位。

使用泰勒公式对关键点进行展开，倒数用两边两个点的斜率近似。

---

第六步：消除边界相应。使用harris角点检测的原理，求出H(x,y)即构造的梯度变化矩阵，求解λ1和λ2, 如果λ1>>λ2则表示为边界点，进行去除。

---

第七步：特征点的主方向。对于每个特征点，使用sobel算子，得到三个信息(x,y,σ,θ)，即位置、尺度、方向。具有多个方向的关键点被复制成多份，然后将方向值分别赋给复制后的特征点，一个特征点就产生了多个坐标、尺度相同，但是方向不同的特征点。

---

第八步：统计相邻部分的梯度的方向，画出直方图，把直方图中出现次数最多的作为主方向，如果此方向的次数大于主方向的0.8，那么此方向也是辅助方向。

---

第九步：为了保证特征矢量的旋转不变性，要以特征点为中心，在附近邻域内将坐标轴旋转θ角度，即将坐标轴旋转为特征点的主方向。

---

第十步：旋转之后的主方向为中心取8x8的窗口，求每个像素的梯度幅值和方向，箭头方向代表梯度方向，长度代表梯度幅值，然后利用高斯窗口对其进行加权运算，最后在每个4x4的小块上绘制8个方向的梯度直方图，计算每个梯度方向的累加值，即可形成一个种子点，即每个特征的由4个种子点组成，每个种子点有8个方向的向量信息。

论文中建议对每个关键点使用4x4共16个种子点来描述，这样一个关键点就会产生128维的SIFT特征向量。

* 注意：在anaconda中配置opencv-python3.4.1版本。直接在命令行采用conda install 命令报错，用pip install 命令也一样，根据网上的方法修改配置文件啥的也是没有用的。最后解决的办法是在navigation GUI的环境配置下面，安装opencv库即可，奇怪的是这里可选的版本号只有3.4.1.但是已经可以使用sift变换了，也算是解决了问题。

```python
import numpy as np
import cv2

img = cv2.imread('test_1.jpg')
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

sift = cv2.xfeatures2d.SIFT_create()
# 找出关键点
kp = sift.detect(gray, None)

# 对关键点进行绘图
ret = cv2.drawKeypoints(gray, kp, img)
cv2.imshow('ret', ret)
cv2.waitKey(0)
cv2.destroyAllWindows()

# 使用关键点找出sift特征向量
kp, des = sift.compute(gray, kp)

print(np.shape(kp))
print(np.shape(des))

print(des[0])
```

# SIFT特征匹配

Brute-Force蛮力匹配

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

img1 = cv2.imread('box.png', 0)
img2 = cv2.imread('box_in_scene.png', 0)

def cv_show(name,img):
    cv2.imshow(name, img)
    cv2.waitKey(0)
    cv2.destroyAllWindows()

# cv_show('img1',img1)
# cv_show('img2',img2)

# 第一步：构造SIFT，并求出特征点和特征向量
sift = cv2.xfeatures2d.SIFT_create()
kp1, des1 = sift.detectAndCompute(img1, None)
kp2, des2 = sift.detectAndCompute(img2, None)

# 第二步：构造BFMatcher()蛮力匹配，匹配sift特征向量距离最近对应部分
bf = cv2.BFMatcher(crossCheck=True)
# crossCheck表示两个特征点要互相匹，例如A中的第i个特征点与B中的第j个特征点最近的，并且B中的第j个特征点到A中的第i个特征点也是
#NORM_L2: 归一化数组的(欧几里德距离)，如果其他特征计算方法需要考虑不同的匹配计算方式

# 获得匹配的结果
matches = bf.match(des1, des2)

# 第三步：对匹配的结果按照距离进行排序操作
matches = sorted(matches, key=lambda x: x.distance)

# 第四步：使用drawMatches进行画图操作
img3 = cv2.drawMatches(img1, kp1, img2, kp2, matches[:10], None,flags=2)
cv_show('img3',img3)
```

上面的算法是单点匹配，还有每个点匹配K个点的方法：K对最佳匹配

```python
bf = cv2.BFMatcher()
matches = bf.knnMatch(des1, des2, k=2)

good = []
for m, n in matches:
    if m.distance < 0.75 * n.distance:
        good.append([m])

img3 = cv2.drawMatchesKnn(img1,kp1,img2,kp2,good,None,flags=2)
cv_show('img3',img3)
```

蛮力匹配速度较慢，如果需要更快速完成操作，可以尝试使用cv2.FlannBasedMatcher

# 随即抽样一致算法（Random sample consensus，RANSAC）

* 选择初始样本点进行拟合，给定一个容忍范围，不断进行迭代
* 每一次拟合后，容差范围内都有对应的数据点数，找出数据点个数最多的情况，就是最终的拟合结果
