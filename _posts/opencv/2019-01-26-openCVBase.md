---
layout: post
title: OpenCV图像基本操作
tags:
- openCV
categories: opencv
description: openCV学习过程
---

[TOC]

# 参考
[《OpenCV 3计算机视觉：Python语言实现（原书第2版）》](https://blog.csdn.net/wyx100/article/details/73006307)

[网易云课堂](https://study.163.com/course/courseMain.htm?courseId=1208943817)

感谢博主[python我的最爱](https://www.cnblogs.com/my-love-is-python/category/1308248.html)

# 安装方法
直接使用pip命令安装即可
`pip install opencv-python`

如果速度较慢，请使用-i标签指定下载源`pip install -i https://pypi.tuna.tsinghua.edu.cn/simple opencv-python`

openCV开发需要用到三个包`numpy matplotlib opencv-python opencv-contrib-python`

测试程序:

```python
#导入cv模块
import cv2 as cv
#读取图像，支持 bmp、jpg、png、tiff 等常用格式
# opencv默认的格式是RGB
img = cv.imread("D:\opencv\lena.jpg")
#创建窗口并显示图像
cv.namedWindow("Image")
cv.imshow("Image",img)
#esc退出
cv.waitKey(0)
#释放窗口
cv2.destroyAllWindows()

# 对显示图片进行封装
def cv_show(name,img):
    cv2.imshow(name,img)
    cv2.waitKey(0)
    cv2.destroyAllWindows()
```

# 图像IO

`cv2.imread(filename[,flag])`
flag有三种取值：
* cv2.IMREAD_COLOR : 彩色图
* cv2.IMREAD_GRAYSCALE : 灰度图
* cv2.IMREAD_UNCHANGED : Loads image as such including alpha channel
* imread读取的图像的类型是ndarray（w,h,c),单个元素的类型是uint8

>可以用 1, 0 or -1 来代替这三个取值.

`cv2.write(filename,img[,params])`
默认以PNG保存。例如`cv2.imwrite('mycat.png',img)`

`cv2.imshow(name,img)`



# 补充：Python 中各种imread函数的区别与联系

主要的方式有：
* PIL.Image.open
* scipy.misc.imread
* scipy.ndimage.imread
* cv2.imread
* matplotlib.image.imread
* skimge
* caffe.io.load_iamge


这些方法可以分为四大家族：PIL、matplotlib、opencv、skimage，区分如下：
* PIL.Image.open 不直接返回numpy对象，可以用numpy提供的函数进行转换，参考[Image和Ndarray互相转换](https://blog.csdn.net/gzhermit/article/details/72758641).其他模块都直接返回numpy.ndarray对象，通道顺序为RGB，通道值得默认范围为0-255。
* matplot.image.imread从名字中可以看出这个模块是具有matlab风格的，直接返回numpy.ndarray格式通道顺序是RGB，通道值默认范围0-255。
* v2.imread使用opencv读取图像，直接返回numpy.ndarray 对象，通道顺序为BGR ，注意是BGR，通道值默认范围0-255。
* skimage.io.imread: 直接返回numpy.ndarray 对象，通道顺序为RGB，通道值默认范围0-255。caffe.io.load_image: 没有调用默认的skimage.io.imread，返回值为0-1的float型数据，通道顺序为RGB

具体测试代码可参考[CSDN/walter_xh](https://blog.csdn.net/renelian1572/article/details/78761278)。

# 截取部分图像数据

```python
img=cv2.imread('cat.jpg')
cat=img[0:50,0:200]
cv_show('cat',cat)
```

# 颜色通道提取与合并

```python
(B,R,R)=cv2.split(img)
dst=cv2.merge([B,G,R])
# 只保留R
cur_img = img.copy()
cur_img[:,:,0] = 0
cur_img[:,:,1] = 0
cv_show('R',cur_img)
```

# 边界填充

```python
top_size,bottom_size,left_size,right_size = (50,50,50,50)
#该参数的定义为上下左右分别扩展的像素的个数

replicate = cv2.copyMakeBorder(img, top_size, bottom_size, left_size, right_size, borderType=cv2.BORDER_REPLICATE)
reflect = cv2.copyMakeBorder(img, top_size, bottom_size, left_size, right_size,cv2.BORDER_REFLECT)
reflect101 = cv2.copyMakeBorder(img, top_size, bottom_size, left_size, right_size, cv2.BORDER_REFLECT_101)
wrap = cv2.copyMakeBorder(img, top_size, bottom_size, left_size, right_size, cv2.BORDER_WRAP)
constant = cv2.copyMakeBorder(img, top_size, bottom_size, left_size, right_size,cv2.BORDER_CONSTANT, value=0)

import matplotlib.pyplot as plt
plt.subplot(231), plt.imshow(img, 'gray'), plt.title('ORIGINAL')
plt.subplot(232), plt.imshow(replicate, 'gray'), plt.title('REPLICATE')
plt.subplot(233), plt.imshow(reflect, 'gray'), plt.title('REFLECT')
plt.subplot(234), plt.imshow(reflect101, 'gray'), plt.title('REFLECT_101')
plt.subplot(235), plt.imshow(wrap, 'gray'), plt.title('WRAP')
plt.subplot(236), plt.imshow(constant, 'gray'), plt.title('CONSTANT')

plt.show()
```

- BORDER_REPLICATE：复制法，也就是复制最边缘像素。
- BORDER_REFLECT：反射法，对感兴趣的图像中的像素在两边进行复制例如：fedcba|abcdefgh|hgfedcb   
- BORDER_REFLECT_101：反射法，也就是以最边缘像素为轴，对称，gfedcb|abcdefgh|gfedcba
- BORDER_WRAP：外包装法cdefgh|abcdefgh|abcdefg  
- BORDER_CONSTANT：常量法，常数值填充。

# 图像融合

`res = cv2.add(img_cat,img_dog)`

可以使用numpy中的矩阵加法来实现。但是在opencv中加法是饱和操作，也就是有上限值，numpy会对结果取模。注意两幅图片的大小类型必须一致，或者第二个图象是一个标量.

`res = cv2.addWeighted(img_cat, 0.4, img_dog, 0.6, 0)`
注意，融合的两张图片的shape应该相同，采用`resize`函数而非`reshape`

* reshape:只是在逻辑上改变矩阵的行列数或者通道数，没有任何的数据的复制，也不会增减任何数据，因此这是一个O(1)的操作，它要求矩阵是连续的。
* resize:对图像进行压缩扩大处理，采用插值方法

# 视频IO

* cv2.VideoCapture可以捕获摄像头，用数字来控制不同的设备，例如0,1。
* 如果是视频文件，直接指定好路径即可。

```python
vc = cv2.VideoCapture('test.mp4')
# 检查是否打开正确
if vc.isOpened():
    open, frame = vc.read()
else:
    open = False
while open:
    ret, frame = vc.read()
    if frame is None:
        break
    if ret == True:
        gray = cv2.cvtColor(frame,  cv2.COLOR_BGR2GRAY)
        cv2.imshow('result', gray)
        if cv2.waitKey(100) & 0xFF == 27:
            break
vc.release()
cv2.destroyAllWindows()
```


# 灰度图

两种方法获取灰度图
* 在读取时选取参数cv2.IMREAD_GRAYSCALE
* 采用`cv2.cvtColor`函数转换

```python
img=cv2.imread('cat.jpg')
img_gray = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)
img_gray.shape#这里显示的是二维矩阵
```

# HSV
* H - 色调（主波长）。
* S - 饱和度（纯度/颜色的阴影）。
* V值（强度）

采用`cv2.cvtColor`函数转换，如`hsv=cv2.cvtColor(img,cv2.COLOR_BGR2HSV)`

# 图像阈值

`ret, dst = cv2.threshold(src, thresh, maxval, type)`

- src： 输入图，只能输入单通道图像，通常来说为灰度图
- dst： 输出图
- thresh： 阈值
- maxval： 当像素值超过了阈值（或者小于阈值，根据type来决定），所赋予的值
- type：二值化操作的类型，包含以下5种类型： cv2.THRESH_BINARY； cv2.THRESH_BINARY_INV； cv2.THRESH_TRUNC； cv2.THRESH_TOZERO；cv2.THRESH_TOZERO_INV

- cv2.THRESH_BINARY           超过阈值部分取maxval（最大值），否则取0
- cv2.THRESH_BINARY_INV    THRESH_BINARY的反转
- cv2.THRESH_TRUNC            大于阈值部分设为阈值，否则不变
- cv2.THRESH_TOZERO          大于阈值部分不改变，否则设为0
- cv2.THRESH_TOZERO_INV  THRESH_TOZERO的反转

# 图像平滑
去除图像中的噪声点，采用滤波的方式

```python
# 均值滤波
# 简单的平均卷积操作
blur = cv2.blur(img, (3, 3))
cv_show('blur', blur)

# 方框滤波
# 基本和均值一样，可以选择归一化
box = cv2.boxFilter(img,-1,(3,3), normalize=True)  
cv_show('box', box)

# 方框滤波
# 基本和均值一样，可以选择归一化
box = cv2.boxFilter(img,-1,(3,3), normalize=True)  
cv_show('box', box)

# 方框滤波
# 基本和均值一样，可以选择归一化,容易越界
box = cv2.boxFilter(img,-1,(3,3), normalize=False)  
cv_show('box', box)

# 高斯滤波
# 高斯模糊的卷积核里的数值是满足高斯分布，相当于更重视中间的
aussian = cv2.GaussianBlur(img, (5, 5), 1)  
cv_show('aussian', aussian)

# 中值滤波
# 相当于用中值代替
median = cv2.medianBlur(img, 5)  # 中值滤波
cv_show('median', median)

# 展示所有的
res = np.hstack((blur,aussian,median))
#print (res)
cv_show('median vs average', res)
```

# 形态学-腐蚀和膨胀
应用于消除噪声；分隔独立的图像元素，以及连接相邻的元素；寻找图像中的明显的极大值区域或极小值区域。

* 膨胀：此操作将图形A与任意形状的内核B（通常为正方形或原形）进行卷积。内核B有一个可定义的锚点，通常定义为内核中心点。
* 进行膨胀操作时，将内核B划过图像，将内核B覆盖区域的最大值像素提取，并替代锚点位置的像素。显然，这一最大化操作将会导致图像中的亮区开始“扩展”。
* 腐蚀：与膨胀操作相反，提取的是内核覆盖中的像素的最小值。

```python
img = cv2.imread('cat.jpg')
# 不指定锚点的位置时，默认在内核中心位置
kernel = np.ones((3,3),np.uint8)
# 腐蚀操作
erosion = cv2.erode(img,kernel,iterations = 1)

# 比较不同的迭代次数的差异
pie = cv2.imread('pie.png')
kernel = np.ones((30,30),np.uint8)
erosion_1 = cv2.erode(pie,kernel,iterations = 1)
erosion_2 = cv2.erode(pie,kernel,iterations = 2)
erosion_3 = cv2.erode(pie,kernel,iterations = 3)
res = np.hstack((erosion_1,erosion_2,erosion_3))
cv_show('res', res)

# 膨胀操作的参数都是相同的，不在赘述
```

# 开运算和闭运算

```python
# 开：先腐蚀，再膨胀
img = cv2.imread('dige.png')

kernel = np.ones((5,5),np.uint8)
opening = cv2.morphologyEx(img, cv2.MORPH_OPEN, kernel)

cv_show('opening', opening)

# 闭：先膨胀，再腐蚀
img = cv2.imread('dige.png')

kernel = np.ones((5,5),np.uint8)
closing = cv2.morphologyEx(img, cv2.MORPH_CLOSE, kernel)

cv_show('closing', closing)
```

# 梯度运算

```python
# 梯度=膨胀-腐蚀
pie = cv2.imread('pie.png')
kernel = np.ones((7,7),np.uint8)
dilate = cv2.dilate(pie,kernel,iterations = 5)
erosion = cv2.erode(pie,kernel,iterations = 5)

res = np.hstack((dilate,erosion))

cv2.imshow('res', res)
# 画出膨胀-腐蚀的图像和梯度函数的图像进行对比，可以看出，当腐蚀和膨胀的迭代次数为1时，相减即为梯度。
g=dilate-erosion
gradient = cv2.morphologyEx(pie, cv2.MORPH_GRADIENT, kernel)

res = np.hstack((g,gradient))
cv2_show('gradient', res)
```

# 礼帽与黑帽
* 礼帽 = 原始输入-开运算结果
* 黑帽 = 闭运算-原始输入

```python
#礼帽
img = cv2.imread('dige.png')
tophat = cv2.morphologyEx(img, cv2.MORPH_TOPHAT, kernel)
cv2_show('tophat', tophat)

#黑帽
img = cv2.imread('dige.png')
blackhat  = cv2.morphologyEx(img,cv2.MORPH_BLACKHAT, kernel)
cv_show('blackhat ', blackhat )
```

# 图像梯度-Sober算子

$$
G_x=\left[
\begin{matrix}
-1 & 0 & +1\\
-2 & 0 & +2\\
-1 & 0 & +1
\end{matrix}
\right] * A
,
G_y=\left[
\begin{matrix}
-1 & -2 & -1\\
0 & 0 & 0\\
+1 & +2 & +1
\end{matrix}
\right] * A
$$

`dst = cv2.Sobel(src, ddepth, dx, dy, ksize)`

* ddepth:图像的深度，这里cv2.CV_64F使得结果可以取负值
* dx和dy分别表示水平和竖直方向
* ksize是Sobel算子的大小，即移动方框的大小

`cv2.convertScalerAbs`(将像素点进行绝对值的计算)

```python
# 方法一：直接计算
sobelxy=cv2.Sobel(img,cv2.CV_64F,1,1,ksize=3)
sobelxy = cv2.convertScaleAbs(sobelxy)
cv_show(sobelxy,'sobelxy')

# 方法二：分解计算x和y，再求和
sobelx = cv2.Sobel(img,cv2.CV_64F,1,0,ksize=3)
sobelx = cv2.convertScaleAbs(sobelx)
cv_show(sobelx,'sobelx')

sobely = cv2.Sobel(img,cv2.CV_64F,0,1,ksize=3)
sobely = cv2.convertScaleAbs(sobely)  
cv_show(sobely,'sobely')

sobelxy = cv2.addWeighted(sobelx,0.5,sobely,0.5,0)
cv_show(sobelxy,'sobelxy')
```

不建议直接计算，因为各自计算相加的结果，轮廓更加明显。

# 图像梯度-Scharr算子

$$
G_x=\left[
\begin{matrix}
-3 & 0 & +3\\
-10 & 0 & +10\\
-3 & 0 & +3
\end{matrix}
\right] * A
,
G_y=\left[
\begin{matrix}
-3 & -10 & -3\\
0 & 0 & 0\\
+3 & +10 & +3
\end{matrix}
\right] * A
$$

`cv2.Scharr(src，ddepth, dx, dy)`， 使用Scharr算子进行计算

参数说明：src表示输入的图片，ddepth表示图片的深度，通常使用-1， 这里使用cv2.CV_64F允许结果是负值， dx表示x轴方向算子，dy表示y轴方向算子

scharr算子比sobel算子在比例上更大，因此这样的好处是scharr算子获得的结果能体现出更多的边缘梯度的细节

# 图像梯度-laplacian算子

$$
G=\left[
\begin{matrix}
0 & 1 & 0\\
1 & -4 & 1\\
0 & 1 & 0
\end{matrix}
\right]
$$

`cv2.laplacian(src， ddepth)` 使用拉普拉斯算子进行计算

参数说明: src表示输入的图片，ddepth表示图片的深度，这里使用cv2.CV_64F允许结果是负值

laplacian 算子，从图中可以看出当前点的位置与周围4个点位置之差， 即周围四个点之和 - 4*当前位置像素点，这种算法容易受到噪声点的干扰，不存在x和y轴的计算过程

# 不同算子之间的差异

```python
#不同算子的差异
img = cv2.imread('lena.jpg',cv2.IMREAD_GRAYSCALE)
sobelx = cv2.Sobel(img,cv2.CV_64F,1,0,ksize=3)
sobely = cv2.Sobel(img,cv2.CV_64F,0,1,ksize=3)
sobelx = cv2.convertScaleAbs(sobelx)   
sobely = cv2.convertScaleAbs(sobely)  
sobelxy =  cv2.addWeighted(sobelx,0.5,sobely,0.5,0)  

scharrx = cv2.Scharr(img,cv2.CV_64F,1,0)
scharry = cv2.Scharr(img,cv2.CV_64F,0,1)
scharrx = cv2.convertScaleAbs(scharrx)   
scharry = cv2.convertScaleAbs(scharry)  
scharrxy =  cv2.addWeighted(scharrx,0.5,scharry,0.5,0)

laplacian = cv2.Laplacian(img,cv2.CV_64F)
laplacian = cv2.convertScaleAbs(laplacian)   

res = np.hstack((sobelxy,scharrxy,laplacian))
cv_show(res,'res')
```

可以看出使用sobel算子的轮廓要更清晰，scharr算子的轮廓的细节更多，laplacian获得的结果边缘信息较浅

# Canny边缘检测

`cv2.Canny(src, thresh1, thresh2)` 进行canny边缘检测

参数说明: src表示输入的图片， thresh1表示最小阈值，thresh2表示最大阈值，用于进一步删选边缘信息

1. 使用高斯滤波器，以平滑图像，滤除噪声。
2. 计算图像中每个像素点的梯度强度和方向。
3. 应用非极大值（Non-Maximum Suppression）抑制，以消除边缘检测带来的杂散响应。
4. 应用双阈值（Double-Threshold）检测来确定真实的和潜在的边缘。
5. 通过抑制孤立的弱边缘最终完成边缘检测。

第一步：高斯滤波器进行滤波操作
$$
H=\left[
\begin{matrix}
0.0924 & 0.1192 & 0.0924\\
0.1192 & 0.1538 & 0.1192\\
0.0924 & 0.1192 & 0.0924
\end{matrix}
\right]
$$
注意到这是一个3*3的归一化后的高斯核

第二步：使用sobel算子，计算各个点的梯度大小和梯度方向

当前点的梯度大小和方向如下：
$$
G=\sqrt{G_x^2+G_y^2}, \theta = arctan(G_y/G_x)
$$

第三步：使用非极大值抑制，消除杂散效应

非极大值抑制的想法是比较该点和其梯度方向上的左右两个点，如果大于则保留，否则剔除。

非极大值抑制有两种方式：

方法一：线性插值法

![线性插值法](http://cdn.niyunsheng.top/canny_3.png)

如图所示，g1表示的是坐上角一点的梯度值，g2为当前点上面一点的梯度值，斜线表示的是梯度的方向，我们需要计算出斜线与g1,g2交点的近似梯度值，使用线性差值表示：即
$$
M(dtmp1) = w*M(g2) + (1-w) * M(g1),
w = distance(dtmp1, g2) / distance(g1, g2)
$$

同理计算出M(dtmp2) 即斜线与g3，g4的交点的近似梯度值

将C点的梯度值与M(dtmp1) 和 M(dtmp2)的大小做一个比较，如果比两者都大就保留，否者就去除

方法二：

![方法二](http://cdn.niyunsheng.top/canny_6.png)

如图所示，我们可以直接比较梯度方向的斜线与哪条直线比较接近，就与哪个方向上的梯度值进行比较,我们可以初步的知道角度的区间是

(30, 45, 60, 90) ， 举例说明，如果斜线靠近45度角的斜线,那么就与对角线上的两个点的梯度做比较，如果都大于则保留，否者剔除

第四步：双阈值检测

进行进一步删选，如果当前梯度值大于给定的maxVal，判断为边界， 如果当前梯度值小于minval则舍弃。如果当前梯度值在给定的最大值和最小值之间，如果其周围的点是边界点，那么当前点保留，否者舍弃

从上图我们可以看出，当minval和maxval越小时，所保留的边缘信息更多

```python
img=cv2.imread("lena.jpg",cv2.IMREAD_GRAYSCALE)

v1=cv2.Canny(img,80,150)
v2=cv2.Canny(img,50,100)

res = np.hstack((v1,v2))
cv_show(res,'res')
```

# 图像金字塔

`cv2.pyrDown(src)`  对图片做向下采样操作，通常也可以做模糊化处理

参数说明：src表示输入的图片

`cv2.pyrUp(src)` 对图片做向上采样操作

参数说明：src表示输入的图片

高斯金字塔：分为两种情况：一种是向下采样，一种是向上采样
* 高斯金字塔向下采样方法（缩小）
	- 将Gi与高斯内核卷积
	- 将所有偶数行和列去除
* 高斯金字塔向上采样方法（放大）
	- 将图像在每个方向上扩大为原来的两倍，新增的行和列以0填充
	- 使用先前同样的内核（乘以4）与放大后的图像卷积，获得近似值

拉普拉斯金字塔：

![](http://cdn.niyunsheng.top/Pyramid_4.png)

分为三步：
1. 样本的进行样本的下采样
2. 进行样本的上采样
3. 原始图片 - 经过上面处理后的图片

```python
down=cv2.pyrDown(img)
down_up=cv2.pyrUp(down)
l_1=img-down_up
cv_show(l_1,'l_1')
```

# 图像轮廓

轮廓检测：轮廓检测相较于canny边缘检测，轮廓检测的线条要更少一些，在opencv中，使用的函数是cv2.findContours进行轮廓检测

`contours, hierarchy = cv2.findContours(img,mode,method)`
* mode:轮廓检索模式
	- RETR_EXTERNAL ：只检索最外面的轮廓；
	- RETR_LIST：检索所有的轮廓，并将其保存到一条链表当中；
	- RETR_CCOMP：检索所有的轮廓，并将他们组织为两层：顶层是各部分的外部边界，第二层是空洞的边界;
	- RETR_TREE：检索所有的轮廓，并重构嵌套轮廓的整个层次;最常用
* method:轮廓逼近方法
	- CHAIN_APPROX_NONE：以Freeman链码的方式输出轮廓，所有其他方法输出多边形（顶点的序列）。最常用
	- CHAIN_APPROX_SIMPLE:压缩水平的、垂直的和斜的部分，也就是，函数只保留他们的终点部分。

`cv2.cvtcolor(img, cv2.COLOR_BGR2GRAY)`
* 将彩色图转换为灰度图
* img表示输入的图片
* cv2.COLOR_BGR2GRAY表示颜色的变换形式

`cv2.drawCountours(img, contours, -1, (0, 0, 255), 2)`
* 画出图片中的轮廓值，也可以用来画轮廓的近似值
* contours表示轮廓值
* -1表示轮廓的索引，值为-1表示全部
* (0, 0, 255)表示颜色
* 2表示线条粗细

`cv2.contourArea(cnt， True)`
* 计算轮廓的面积
* cnt为输入的单个轮廓值

`cv2.arcLength(cnt， True)`
* 计算轮廓的周长
* cnt为输入的单个轮廓值

`cv2.aprroxPolyDP(cnt, epsilon,True)`
* 用于获得轮廓的近似值，使用cv2.drawCountors进行画图操作
* cnt为输入的轮廓值
* epsilon为阈值T，一般选择为周长的百分比
* 通常使用轮廓的周长作为阈值，True表示的是轮廓是闭合的

`x, y, w, h = cv2.boudingrect(cnt)`
* 获得外接矩形
* x，y, w, h 分别表示外接矩形的x轴和y轴的坐标，以及矩形的宽和高
* cnt表示输入的轮廓值

`cv2.rectangle(img, (x, y), (x+w, y+h), (0, 255, 0), 2)`
* 根据坐标在图像上画出矩形
* img表示传入的图片， (x, y)表示左上角的位置, （x+w， y+h）表示加上右下角的位置，（0, 255, 0)表示颜色，2表示线条的粗细

`(x, y), radius = cv2.minEnclosingCircle(cnt)`
* 获得外接圆的位置信息
* (x, y)表示外接圆的圆心，radius表示外接圆的半径， cnt表示输入的轮廓

`cv2.Cricle(img, center, radius, (0, 255, 0), 2)`
* 根据坐标在图上画出圆
* img表示需要画的图片，center表示圆的中心点，radius表示圆的半径, (0, 255, 0)表示颜色， 2表示线条的粗细

```python
# 为了更高的准确率，使用二值图像
img = cv2.imread('contours.png')
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
ret, thresh = cv2.threshold(gray, 127, 255, cv2.THRESH_BINARY)
cv_show(thresh,'thresh')
# 获取轮廓
binary, contours, hierarchy = cv2.findContours(thresh, cv2.RETR_TREE, cv2.CHAIN_APPROX_NONE)
# 绘制轮廓
#传入绘制图像，轮廓，轮廓索引，颜色模式，线条厚度
# 注意需要copy,要不原图会变。。。
draw_img = img.copy()
res = cv2.drawContours(draw_img, contours, -1, (0, 0, 255), 2)
cv_show(res,'res')

# 轮廓特征
cnt = contours[0]#获取第0个轮廓
#面积
cv2.contourArea(cnt)
#周长，True表示闭合的
cv2.arcLength(cnt,True)

# 轮廓近似
img = cv2.imread('contours2.png')

gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
ret, thresh = cv2.threshold(gray, 127, 255, cv2.THRESH_BINARY)
# 这里需要注意，新版的findContours返回两个参数
contours, hierarchy = cv2.findContours(thresh, cv2.RETR_TREE, cv2.CHAIN_APPROX_NONE)
cnt = contours[0]

draw_img = img.copy()
res = cv2.drawContours(draw_img, [cnt], -1, (0, 0, 255), 2)
cv_show(res,'res')

epsilon = 0.15*cv2.arcLength(cnt,True)
approx = cv2.approxPolyDP(cnt,epsilon,True)

draw_img = img.copy()
res = cv2.drawContours(draw_img, [approx], -1, (0, 0, 255), 2)
cv_show(res,'res')

#边界矩形
x,y,w,h = cv2.boundingRect(cnt)
img = cv2.rectangle(img,(x,y),(x+w,y+h),(0,255,0),2)
cv_show(img,'img')

#外接圆
(x,y),radius = cv2.minEnclosingCircle(cnt)
center = (int(x),int(y))
radius = int(radius)
img = cv2.circle(img,center,radius,(0,255,0),2)
cv_show(img,'img')
```

# 模板匹配

模板匹配和卷积原理很像，模板在原图像上从原点开始滑动，计算模板与图像被覆盖的地方的差异程度，这个差别程度的计算方法在opencv中有6种，然后将每次计算的结果放在一个矩阵里，作为结果输出。假如原图像是A*B，模板是a*b，则输出的结果的矩阵是`(A-a+1) * (B-b+1)`

`cv2.matchTemplate(src, template, method)`  

参数说明： src目标图像， template模板，method使用什么指标做模板的匹配度指标

[method设置方法详解](https://docs.opencv.org/4.0.1/df/dfb/group__imgproc__object.html#ga3a7850640f1fe1f58fe91a2d7583695d)

* cv.TM_SQDIFF：计算平方不同，计算出来的值越小，越相关
* cv.TM_SQDIFF_NORMED：归一化平房不同，计算出来的值越接近0，越相关
* cv.TM_CCORR：计算相关性，计算出来的值越大，越相关
* cv.TM_CCORR_NORMED：归一化相关性，计算出来的值越接近1，越相关
* cv.TM_CCOEFF：计算相关系数，计算出来的值越大，越相关
* cv.TM_CCOEFF_NORMED：归一化相关系数，计算出来的值越接近1，越相关

尽量采用归一化的结果

`min_val, max_val, min_loc, max_loc = cv2.minMaxLoc(ret)`
* 找出矩阵中最大值和最小值，即其对应的(x, y)的位置
* 参数说明：min_val， max_val, min_loc, max_loc 分别表示最小值，最大值，即对应的位置， ret输入的矩阵

`cv2.rectangle(img, (x, y), (x+w. y+h), (0, 0, 255), 2)`
* 用于在图像上画出矩阵
* 参数说明：img表示图片，(x, y)表示矩阵左上角的位置，(x+w, y+h)表示矩阵右下角的位置, (0, 0, 255)表示颜色，2表示线条

```python
res = cv2.matchTemplate(img, template, cv2.TM_SQDIFF)
min_val, max_val, min_loc, max_loc = cv2.minMaxLoc(res)

# 多目标匹配
img_rgb = cv2.imread('mario.jpg')
img_gray = cv2.cvtColor(img_rgb, cv2.COLOR_BGR2GRAY)
template = cv2.imread('mario_coin.jpg', 0)
h, w = template.shape[:2]

res = cv2.matchTemplate(img_gray, template, cv2.TM_CCOEFF_NORMED)
threshold = 0.8
# 取匹配程度大于%80的坐标
loc = np.where(res >= threshold)
for pt in zip(*loc[::-1]):  # *号表示可选参数
    bottom_right = (pt[0] + w, pt[1] + h)
    cv2.rectangle(img_rgb, pt, bottom_right, (0, 0, 255), 2)
# zip函数用于将可迭代的对象作为参数，将对象中的元素打包成一个个元组，然后返回由这些元组组成的列表。如果哥哥迭代器的元素个数不一致，则返回列表长度与最短的对象相同，利用*号操作符，可以将元组解压为列表
```  

# 像素频率分布直方图

`cv2.calcHist(images,channels,mask,histSize,ranges)`
* images: 原图像图像格式为 uint8 或 ﬂoat32。当传入函数时应 用中括号 [] 括来例如[img]
* channels: 同样用中括号括来它会告函数我们统幅图 像的直方图。如果入图像是灰度图它的值就是 [0]如果是彩色图像的传入的参数可以是 [0][1][2] 它们分别对应着 BGR。
* mask: 掩模图像。统整幅图像的直方图就把它为 None。但是如果你想统图像某一分的直方图的你就制作一个掩模图像并使用它。
* histSize:BIN 的数目。也应用中括号括来
* ranges: 像素值范围常为 [0,256]

```python
img = cv2.imread('cat.jpg',0) #0表示灰度图
hist = cv2.calcHist([img],[0],None,[256],[0,256])
plt.hist(img.ravel(),256);
plt.show()

#mask操作
# 创建mast，其大小和显示图片的大小相等
mask = np.zeros(img.shape[:2], np.uint8)
print (mask.shape)
mask[100:300, 100:400] = 255
cv_show(mask,'mask')

masked_img = cv2.bitwise_and(img, img, mask=mask)#与操作

hist_full = cv2.calcHist([img], [0], None, [256], [0, 256])
hist_mask = cv2.calcHist([img], [0], mask, [256], [0, 256])

plt.subplot(221), plt.imshow(img, 'gray')
plt.subplot(222), plt.imshow(mask, 'gray')
plt.subplot(223), plt.imshow(masked_img, 'gray')
plt.subplot(224), plt.plot(hist_full), plt.plot(hist_mask)
plt.xlim([0, 256])
plt.show()
```

直方图均匀化：一般可以用来提升图片的亮度， 如果图像中在150-200之间所占的频数特别的大，频数均衡化指的是让频数的分布看起来更加均匀一些

首先对各个灰度值做频数统计，计算其概率，根据像素的灰度值计算出累积概率，最后将累积概率 * (255-0) 做为函数映射后的灰度值，

这样做的目的，可以使得灰度值之间的间隔更小，即一些频数较大的灰度值补充给了频数较小的灰度值，从而实现了灰度值的均衡化

![直方图均匀化](http://cdn.niyunsheng.top/hist_4.png)

上图中的左边的图是原始数据, 右边的图是进行函数映射后的灰度值

`cv2.equalizeHist(img)`
* 表示进行直方图均衡化
* 这种全局的均衡化也会存在一些问题，由于整体亮度的提升，也会使得局部图像的细节变得模糊，因为我们需要进行分块的局部均衡化操作

`cv2.createCLAHA(clipLimit=8.0, titleGridSize=(8, 8))`
* 用于生成自适应均衡化图像
* 参数说明：clipLimit颜色对比度的阈值， titleGridSize进行像素均衡化的网格大小，即在多少网格下进行直方图的均衡化操作，即分部分分别做均衡化
* 自适应均衡化不会让一些细节消失

```python
#图像均匀化
equ = cv2.equalizeHist(img)
plt.hist(equ.ravel(),256)
plt.show()
res = np.hstack((img,equ))
cv_show(res,'res')

#自适应图像均匀化
clahe = cv2.createCLAHE(clipLimit=2.0, tileGridSize=(8,8))
res_clahe = clahe.apply(img)
res = np.hstack((img,equ,res_clahe))
cv_show(res,'res')
```

# 傅里叶变换和滤波

* 高频：变化剧烈的灰度分量，边界区域
* 低频：变化缓慢的灰度分量，例如一片大海

* 低通滤波器：只保留低频，会使得图像模糊
* 高通滤波器：只保留高频，会使得图像细节增强

* opencv中主要就是cv2.dft()和cv2.idft()（逆变换）
* 输入图像需要先转换成np.float32 格式。
* 得到的结果中频率为0的部分会在左上角，通常要转换到中心位置，可以通过shift变换来实现。
* cv2.dft()返回的结果是双通道的（实部，虚部），通常还需要转换成图像格式才能展示，即转换成（0,255）之间的值。

`cv2.dft(img, cv2.DFT_COMPLEX_OUTPUT)`
* 进行傅里叶变化
* 参数说明: img表示输入的图片， cv2.DFT_COMPLEX_OUTPUT表示进行傅里叶变化的方法

`np.fft.fftshift(img)`
* 将图像中的低频部分移动到图像的中心
* 参数说明：img表示输入的图片

`cv2.magnitude(x, y)`
* 将sqrt(x^2 + y^2) 计算矩阵维度的平方根
* 参数说明：需要进行x和y平方的数

`np.fft.ifftshift(img)`
* 将图像的低频和高频部分移动到图像原来的位置
* 参数说明：img表示输入的图片

`cv2.idft(img)`
* 进行傅里叶的逆变化
* 参数说明：img表示经过傅里叶变化后的图片

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

# 第一步读取图片
img = cv2.imread('lena.jpg', 0)

# 第二步：进行float32形式转换
float32_img = np.float32(img)

# 第三步: 使用cv2.dft进行傅里叶变化
dft_img = cv2.dft(float32_img, flags=cv2.DFT_COMPLEX_OUTPUT)

# 第四步：使用np.fft.shiftfft()将变化后的图像的低频转移到中心位置
dft_img_ce = np.fft.fftshift(dft_img)

# 第五步：使用cv2.magnitude将实部和虚部转换为实部，乘以20是为了使得结果更大
img_dft = 20 * np.log(cv2.magnitude(dft_img_ce[:, :, 0], dft_img_ce[:, :, 1]))

# 第六步：进行画图操作
plt.subplot(121)
plt.imshow(img, cmap='gray')
plt.subplot(122)
plt.imshow(img_dft, cmap='gray')
plt.show()
```

低通滤波：因为高频表示是一些细节，即图像的轮廓和边缘，失去了高频部分，图像就容易变得模糊

```python
# 使用掩模只保留低通

# 第一步读入图片
img = cv2.imread('lena.jpg', 0)
# 第二步：进行数据类型转换
img_float = np.float32(img)
# 第三步：使用cv2.dft进行傅里叶变化
dft = cv2.dft(img_float, flags=cv2.DFT_COMPLEX_OUTPUT)
# 第四步：使用np.fft.fftshift将低频转移到图像中心
dft_center = np.fft.fftshift(dft)
# 第五步：定义掩模：生成的掩模中间为1周围为0
crow, ccol = int(img.shape[0] / 2), int(img.shape[1] / 2) # 求得图像的中心点位置
mask = np.zeros((img.shape[0], img.shape[1], 2), np.uint8)
mask[crow-30:crow+30, ccol-30:ccol+30] = 1

# 第六步：将掩模与傅里叶变化后图像相乘，保留中间部分
mask_img = dft_center * mask

# 第七步：使用np.fft.ifftshift(将低频移动到原来的位置
img_idf = np.fft.ifftshift(mask_img)

# 第八步：使用cv2.idft进行傅里叶的反变化
img_idf = cv2.idft(img_idf)

# 第九步：使用cv2.magnitude转化为空间域内
img_idf = cv2.magnitude(img_idf[:, :, 0], img_idf[:, :, 1])

# 第十步：进行绘图操作
plt.subplot(121)
plt.imshow(img, cmap='gray')
plt.subplot(122)
plt.imshow(img_idf, cmap='gray')
plt.show()
```

高通滤波：流程与上面一样，只是构造的掩模是中间为0，边缘为1，然后与傅里叶变化后的图像结合， 保留高频部分，去除低频部分

```python
# 只保留高频部分
# 使用掩模只保留低通

# 第一步读入图片
img = cv2.imread('lena.jpg', 0)
# 第二步：进行数据类型转换
img_float = np.float32(img)
# 第三步：使用cv2.dft进行傅里叶变化
dft = cv2.dft(img_float, flags=cv2.DFT_COMPLEX_OUTPUT)
# 第四步：使用np.fft.fftshift将低频转移到图像中心
dft_center = np.fft.fftshift(dft)
# 第五步：定义掩模：生成的掩模中间为0周围为1
crow, ccol = int(img.shape[0] / 2), int(img.shape[1] / 2) # 求得图像的中心点位置
mask = np.ones((img.shape[0], img.shape[1], 2), np.uint8)
mask[crow-30:crow+30, ccol-30:ccol+30] = 0

# 第六步：将掩模与傅里叶变化后图像相乘，保留中间部分
mask_img = dft_center * mask

# 第七步：使用np.fft.ifftshift(将低频移动到原来的位置
img_idf = np.fft.ifftshift(mask_img)

# 第八步：使用cv2.idft进行傅里叶的反变化
img_idf = cv2.idft(img_idf)

# 第九步：使用cv2.magnitude转化为空间域内
img_idf = cv2.magnitude(img_idf[:, :, 0], img_idf[:, :, 1])

# 第十步：进行绘图操作
plt.subplot(121)
plt.imshow(img, cmap='gray')
plt.subplot(122)
plt.imshow(img_idf, cmap='gray')
plt.show()
```

# 图像旋转

`M = cv2.getRotationMatrix2D(center,angle,scale)`
angle以逆时针为正，scale为缩放因子，生成旋转矩阵

M.shape为2*3，是一个2*2的放射矩阵

`img2 = cv2.warpAffine(img, M, (w, h))`

仿射变换是指在向量空间中进行一次线性变换(乘以一个矩阵)并加上一个平移(加上一个向量)，变换为另一个向量空间的过程。在有限维的情况下，每个仿射变换可以由一个矩阵A和一个向量b给出，它可以写作A和一个附加的列b。


# cv.resize函数五种插值算法


cv::resize函数有[五种插值算法interpolation](https://blog.csdn.net/fengbingchun/article/details/17335477)：
* INTER_NEAREST - 最近邻插值法
* INTER_LINEAR - 双线性插值法（默认）
* INTER_AREA - 基于局部像素的重采样（resampling using pixel area relation）。对于图像抽取（image decimation）来说，这可能是一个更好的方法。但如果是放大图像时，它和最近邻法的效果类似。[INTER_AREA方法详解](https://medium.com/@wenrudong/what-is-opencvs-inter-area-actually-doing-282a626a09b3)。简单来说，此方法用于图片缩小时就是分块取像素平均值。
* INTER_CUBIC - 基于4x4像素邻域的3次插值法
* INTER_LANCZOS4 - 基于8x8像素邻域的Lanczos插值

## [图像处理之双线性插值法](https://blog.csdn.net/qq_37577735/article/details/80041586)

在数学上，双线性插值是有两个变量的插值函数的线性插值扩展，其核心思想是在两个方向分别进行一次线性插值。

双线性插值法的坐标对应计算公式：

int x=i*m/a ;int y=j*n/b

但是，这样计算图像偏移，右边和下边的像素不会参与运算。最好的办法是改成两个图像的几何中心重合，并且目标图像的每个像素之间都是等间隔的，并且都和两边有一定的边距。即

int x=(i+0.5) * m/a-0.5 ;int y=(j+0.5) * n/b-0.5
