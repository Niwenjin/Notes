# 动手学深度学习（PyTorch 版）

## 目录

[预备知识](#预备知识)  
[计算机视觉](#计算机视觉)

## 预备知识

### 数据操作

Pytorch 的 n 维数组称为张量（tensor），相比 Numpy 的 ndarray，张量多一些重要功能：⾸先，GPU 很好地支持加速计算，⽽ NumPy 仅支持 CPU 计算；其次，张量类支持自动微分。这些功能使得张量类更适合深度学习。

要使用张量，首先，需要导入 torch 库。

```py
import torch
```

可以通过使用全 0、全 1、其他常量，或者从特定分布中随机采样的数字来初始化张量。

```py
# 全0
torch.zeros((2, 3, 4))
# 全1
torch.ones((2, 3, 4))
# 从其他张量的形状
torch.zeros_like(x)
torch.ones_like(x)
# 从有序数列
torch.arange(12)
# 从N(0,1)中随机采样
torch.randn(3, 4)
# 从Python列表
torch.tensor([[2, 1, 4, 3], [1, 2, 3, 4], [4, 3, 2, 1]])
```

可以通过张量的`shape`属性来访问张量（沿每个轴的长度）的形状。要想改变一个张量的形状而不改变元素数量和元素值，可以调用`reshape`函数。

```
x.shape
X = x.reshape(3, 4)
```

对于任意具有相同形状的张量，常见的标准算数运算符都可以被升级成**按元素运算**。

```py
x = torch.tensor([1.0, 2, 4, 8])
y = torch.tensor([2, 2, 2, 2])
# 基本算术运算
x + y, x - y, x * y, x / y, x ** y
# 更多按元素运算
torch.exp(x)
```

还可以把多个张量**连结**（concatenate）在一起。

```py
X = torch.arange(12, dtype=torch.float32).reshape((3,4))
Y = torch.tensor([[2.0, 1, 4, 3], [1, 2, 3, 4], [4, 3, 2, 1]])
# 需要给出张量列表，并给出沿哪个轴连结
torch.cat((X, Y), dim=0), torch.cat((X, Y), dim=1)
```

可以通过逻辑运算符构建张量。对于每个位置，若逻辑语句在该位置为真，则新向量中对应位置为 1；否则为 0。

```py
>>> x
tensor([[2, 1, 4, 3],
        [1, 2, 3, 4],
        [4, 3, 2, 1]])
>>> x > 2
tensor([[False, False,  True,  True],
        [False, False,  True,  True],
        [ True,  True, False, False]])
```

对张量中所有元素进行求和，会产生一个单元素张量。

```py
x.sum()
```

即使形状不同的两个张量之间也可以执行按元素操作，此时会调用**广播机制**。这种机制的工作方式如下：

1. 通过适当复制元素来扩展一个或两个数组，以便在转换之后，两个张量具有相同的形状；
2. 对生成的数组执行按元素操作。

与 Python 数组一样，张量中的元素可以通过索引访问。第一个元素的索引是 0，最后一个元素索引是-1；可以指定包含元素索引和步长。

```py
>>> x
tensor([[2, 1, 4, 3],
        [1, 2, 3, 4],
        [4, 3, 2, 1]])
>>> x[:,1:4:2]  # 设置起始索引，终止索引（不包含）和步长
tensor([[1, 3],
        [2, 4],
        [3, 1]])
```

运行一些操作可能导致为新结果分配内存，这可能会造成内存的浪费。因此，有时需要执行**原地操作**。

```py
Y = Y + X  # Y指向新的位置
# 原地操作
X += Y
X[:] = X + Y
```

### 数据预处理

数据集通常使用 CSV 文件格式，每行表示一条记录，列之间用逗号分隔。

```py
# 创建一个数据集
import os

os.makedirs(os.path.join('..', 'data'), exist_ok=True)
data_file = os.path.join('..', 'data', 'house_tiny.csv')
with open(data_file, 'w') as f:
    f.write('NumRooms,Alley,Price\n')  # 列名
    f.write('NA,Pave,127500\n')  # 每行表示一个数据样本
    f.write('2,NA,106000\n')
    f.write('4,NA,178100\n')
    f.write('NA,NA,140000\n')
```

pandas 软件包是 Python 中常用的数据分析工具。pandas 可以与张量兼容。

```py
import pandas as pd

# 加载数据集
data_file = os.path.join('..', 'data', 'house_tiny.csv')
data = pd.read_csv(data_file)
print(data)
```

为了处理缺失值，典型的方法包括插值法和删除法。

```py
# 对于连续值，可以采取均值填充等方法
inputs, outputs = data.iloc[:, 0:2], data.iloc[:, 2]  # 切片
inputs = inputs.fillna(inputs.mean())

# 可以删除包含缺省值的行
dropna = inputs.dropna()

# 对于离散值，可以将NaN视为一种类别
inputs = pd.get_dummies(inputs, dummy_na=True)  # 将Allay列按照类别转换为两列，对应类别的数值为1，其余为0
```

现在数据都是数值类型，可以转换为张量格式。

```py
import torch

X, y = torch.tensor(inputs.values), torch.tensor(outputs.values)  # pandas数据类型的values属性用于提取底层的Numpy数组
```

### 线性代数

```py
a = torch.randn(3, 4)
b = torch.randn(3, 4)
a * b
```

张量相乘采取的是按元素乘。两个矩阵的按元素乘法称为*Hadamard积*（Hadamard product）（数学符号$\odot$）。对于矩阵$\mathbf{B} \in \mathbb{R}^{m \times n}$，其中第$i$行和第$j$列的元素是$b_{ij}$。矩阵$\mathbf{A}$（在 :eqref:`eq_matrix_def`中定义）和$\mathbf{B}$的Hadamard积为：
$$
\mathbf{A} \odot \mathbf{B} =
\begin{bmatrix}
    a_{11}  b_{11} & a_{12}  b_{12} & \dots  & a_{1n}  b_{1n} \\
    a_{21}  b_{21} & a_{22}  b_{22} & \dots  & a_{2n}  b_{2n} \\
    \vdots & \vdots & \ddots & \vdots \\
    a_{m1}  b_{m1} & a_{m2}  b_{m2} & \dots  & a_{mn}  b_{mn}
\end{bmatrix}.
$$

另一个基本操作是点积。给定两个向量$\mathbf{x},\mathbf{y}\in\mathbb{R}^d$，它们的*点积*（dot product）$\mathbf{x}^\top\mathbf{y}$（或$\langle\mathbf{x},\mathbf{y}\rangle$）是相同位置的按元素乘积的和：$\mathbf{x}^\top \mathbf{y} = \sum_{i=1}^{d} x_i y_i$。

```py
y = torch.ones(4, dtype = torch.float32)
x, y, torch.dot(x, y)
```

### 自动微分

深度学习框架通过自动计算导数，即自动微分（automatic differentiation）来加快求导。实际中，根据设计好的模型，系统会构建⼀个计算图（computational graph），来跟踪计算是哪些数据通过哪些操作组合起来产⽣输出。自动微分使系统能够随后反向传播梯度。这⾥，反向传播（backpropagate）意味着跟踪整个计算图，填充关于每个参数的偏导数。

深度学习框架可以自动计算导数：

1. 首先将梯度附加到想要对其计算偏导数的变量上；
2. 记录目标值的计算；
3. （可选）清除积累的梯度；
4. 执行目标值的反向传播函数；
5. 访问或处理变量的梯度。


```py
x.requires_grad_(True) # 等价于x=torch.arange(4.0,requires_grad=True)

x.grad.zero_()  # 清除积累的梯度
y = 2 * torch.dot(x, x)
y.backward()  # 反向传播
x.grad
```

有时，我们希望将某些计算移动到记录的计算图之外。这⾥可以分离y来返回⼀个新变量u，该变量与y具有相同的值，但丢弃计算图中如何计算y的任何信息。换句话说，梯度不会向后流经u到x。

```py
x.grad.zero_()
y = x * x
u = y.detach()  # u是值为y的一个常数
z = u * x
z.sum().backward()
x.grad == u
```

### 查阅文档

为了知道模块中可以调⽤哪些函数和类，可以调⽤dir函数。例如，我们可以查询随机数生成模块中的所有属性：

```py
>>> import torch
>>> dir(torch.distributions)
```

有关如何使⽤给定函数或类的更具体说明，可以调⽤help函数。

```py
>>> help(torch.ones)
```

## 计算机视觉

### 图像增广

大型数据集是成功应⽤深度神经⽹络的先决条件。图像增广在对训练图像进行一系列的随机变化之后，生成相似但不同的训练样本，从而扩大了训练集的规模。

torchvision.transforms库用于对图像进行变换。

```py
from PIL import Image
from torchvision import transforms
```

随机水平翻转图像通常不会改变图片的类别。

```py
transforms.RandomHorizontalFlip(p=0.5)  # 随机水平翻转图像，概率为 p
transforms.RandomVerticalFlip(p=0.5)  # 随机垂直翻转图像，概率为 p
transforms.RandomRotation(degrees)  # 随机旋转图像，degrees 范围内的任意角度。
```

可以对图像进行裁剪（可以配合填充），随机裁剪图像使物体以不同比例出现在图像的不同位置。这可以降低模型对目标位置的敏感性。

```py
transforms.RandomResizedCrop(size)  # 随机裁剪并调整图像到指定 size
transforms.Resize(size)  # 调整图像大小到 size（可以是整数或元组）
transforms.Pad(padding)  # 在图像周围填充 padding 数量的像素
```

另一种增广方法是改变颜色。们可以改变图像颜色的四个方面：亮度、对⽐度、饱和度和色调。

```py
transforms.ColorJitter(brightness, contrast, saturation, hue)  # 随机调整图像的亮度、对比度、饱和度和色调
```

多种图像增广方法通常被结合使用。transforms.Compose用于将多个变换组合成一个变换。

```py
transform = transforms.Compose([
    transforms.RandomCrop(32, padding=4),
    transforms.RandomHorizontalFlip(),
    transforms.ToTensor(),  # 将图像转换为深度学习框架所要求的格式
    transforms.Normalize((0.4914, 0.4822, 0.4465), (0.2023, 0.1994, 0.2010)),
])
```