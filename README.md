## darknet_truck
##### 1. 用darknet识别卡车 汽车 巴士和人案例
```
* 我使用的是GPU训练的数据在Makefile中调整 GPU=1 CUDNN=1
* export PATH=/usr/local/cuda-8.0/bin/:$PATH
* export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:$LD_LIBRARY_PATH
如果是CPU请忽略此操作。
```
##### 2.准备数据/打标签
```
* 使用 [labelImg](https://github.com/tzutalin/labelImg) 对数据进行标记。
```

##### 3.建立训练和验证数据文件夹/提取图片名称
```
在darknet文件夹下建立4个文件夹trainImage、validateImage、trainImageXML、validateImageXML，分别用来存放训练图片、验证图片、训练图片的xml标签、验证图片的xml标签。
在darknet文件夹下建立2个txt文件trainImageId.txt和validateImageId.txt，分别用来存放训练图片和测试图片的名称（含后缀不包含目录)。这一步可以在上边4个文件夹手动建立完成的基础上调用python脚本自动完成，运行darkent目录下createdID.py就可以分别生成trainImageId.txt和validateImageId.txt
```
##### 4.xml标签转换成txt图片路径提取
```
训练需要把图片的xml格式标签转换成txt文件格式，并且需要把所有的训练和验证数据图片的路径提取到一个单独的txt文件里，供训练的时候读取，可以使用python脚本自动完成，修改darknet目录下trans.py 中classes分类的之后运行即可。

运行之后生成一系列文件，分别是：
XXX.txt——存放训练/验证图片的xml标签转换成的txt文件,自动生成的所有训练和验证的txt文件保存在对应的trainImage和validateImage文件夹下;
trainImagePath.txt/validateImagePath.txt存放训练/验证图片的名称（包含后缀）和完整路径;
```
##### 5.修改配置文件

###### 5.1 修改cfg/voc.data文件
```
voc.data是整个训练流程的引入文件，记录了训练的 classes（分类）、train（训练数据）、valid（验证数据）、names（训练模型）、backup（训练结果保存路径）。根据自己的情况分别做以下更改：

* classes = N （N为自己的分类数量，有10类不同的对象，N = 10）
* train = /home/XXX/darknet_truck/trainImagePath.txt
* valid = /home/XXX/darknet_truck/validateImagePath.txt
* names = data/voc.names (需要修改原voc.names文件中参数)
* backup = backup （训练结果保存在darknet_truck/backup/目录下）
```

###### 5.2 修改data/voc.names文件
```
voc.names文件存放的是所有的分类，原始文件一共有20类，每类单独占一行，一共20行，根据自己的分类情况做更改，例如有3种分类，分别是class1、class2、class3，则更改voc.names文件为：

* class1
* class2
* class3
```

###### 5.3 修改cfg/yolo-voc.2.0.cfg文件
```
yolo-voc.2.0.cfg文件定义yolo训练使用的网络，有两处需要修改：

classes = N （N为自己的分类数）
最后一个convolutional层中 filters = (classes+ coords+ 1)* (NUM)，classes是分类数，coords和NUM在 voc.2.0.cfg中分别设定为4和5,所以如果有3个分类，则修改filters的值为40（（3+4+1）×5 =40 ）
```
##### 6.下载预训练卷积权重文件darknet19_448.conv.23
```
在该 [卷积权重](http://pjreddie.com/media/files/darknet19_448.conv.23) 文件的基础上做进一步的参数调整, 下载完成之后保存在cfg目录下。
```

##### 7.train image

yolo默认训练会迭代45000次，训练之前可以根据自己的情况修改迭代次数，进入cfg/yolo_voc.2.0.cfg修改max_batches的值。在darknet目录下执行训练命令：
```
 ./darknet detector train cfg/voc.data cfg/yolo-voc.2.0.cfg cfg/darknet19_448.conv.23
```

##### 8.test image
运行以下命令执行测试
```
 ./darknet detector test cfg/voc.data cfg/yolo-voc.2.0.cfg backup/yolo-voc_900.weights validateImage/043.jpg
```
