# 动手学深度学习（东北石油大学-大数据分析技术）

# 基础知识

## 一、深度学习介绍

### 1、简介

+ 人工智能最热门领域
+ 核心是神经网络
+ 是一门语言

### 2、经典与最新模型

+ 线性神经网络、多层感知机
+ LeNet（卷积神经网络，用于手写数字识别模型）
+ RNN（循环神经网络）、LSTM（长短期记忆人工神经网络）
+ Attention（注意力机制）、Transformer
+ SGD（随机梯度下降算法）
+ 并行计算技术、多GPU计算技术、分布式技术
+ YoloV、OpenCV（计算机视觉-目标检测）
+ BERT（自然语言处理）

### 3、AI热门方向

+ 计算机视觉
+ 深度学习
+ 自然语言处理

#### 4、应用实例

+ 图片分类
+ 物理检测和分割
+ 样式迁移
+ 人脸识别和合成
+ 文字生成图片
+ 文字生成（语义模型）
+ 无人驾驶（计算机视觉）

#### 5、PyTorch介绍

+ 深度学习算法是高度结构化的，不需要从零创建
+ 深度学习框架：通过将深度学习算法和模型抽象成张量的一系列计算，并把计算涉及的一些算法抽象成应用程序接口供用户使用，从而可以快速实现各种各样的深度学习算法。
+ **PyTorch**是一个基于Python的深度学习框架，提供两个高级功能：
  + 具有强大的**GPU加速**的张量计算
  + 包含**自动求导**系统的深度神经网络

## 二、数据操作与预处理

### 1、张量

**N维数组是机器学习和神经网络的主要数据结构。**数组也称为**张量（tensor）**，它具有多个维度。

+ 0个维度数学意义为标量
+ 1个维度数学意义为向量
+ 2个维度数学意义为矩阵
+ 2个维度以上无特殊数学意义

深度学习框架中的数组（张量）比Numpy的ndarray多一些重要功能（CPU加速、自动微分），这些功能使得张量类更适合深度学习。

### 2、张量创建与操作

#### （1）创建一个行向量

```python
x = torch.arange(12)  
x 
```

```
tensor([ 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11])
```

+ 这个行向量包含从0开始的前12个整数，默认创建为**浮点数**
+ 张量中的每个值都称为张量的**元素**（element）
+ 除非额外指定，新的张量**默认**将存储在**内存**中，并采用基 于**CPU**的计算

#### （2）查询张量维度

```python
x.shape
```

```
torch.Size([12])
```

可以通过张量的shape属性来访问张量沿**每个维度的长度**。

#### （3）查询张量元素数量

```python
x.numel()
```

```
12
```

numel()（即number of elements的缩写）可以查看张量有多少元素。

#### （4）更改张量形状（维度）

```python
X = x.reshape(3, 4)
X
```

```
tensor([[ 0, 1, 2, 3], 
        [ 4, 5, 6, 7], 
        [ 8, 9, 10, 11]])
```

+ 要想改变一个张量的形状而**不改变元素数量和元素值**，可以调用reshape函数
+ 改变张量的形状，张量的大小（size）不会改变

**自动计算维度并更改**（只能计算剩余一维度的情况）

如果我们的目标形状是（高度,宽度）， 那么在知道宽度后，高度会被自动计算得出。

我们可以用x.reshape(-1,4)或x.reshape(3,-1)来取代 x.reshape(3,4)

#### （5）生成全0/1矩阵

```python
torch.zeros((2, 3, 4))
torch.ones((2, 3, 4))
```

#### （6）根据特点概率分布生成张量

```python
torch.randn(3, 4)
```

生成元素服从均值为0，标准差为1的**正态分布**。

#### （7）指定数据生成张量

```python
torch.tensor([[2, 1, 4, 3], [1, 2, 3, 4], [4, 3, 2, 1]])
```

```
tensor([[2, 1, 4, 3], 
        [1, 2, 3, 4], 
        [4, 3, 2, 1]])
```

还可以通过提供包含数值的**Python列表 （或嵌套列表）**，来为所需张量中的每个元素赋予确定的值。

### 3、张量基本运算

+ 按元素（element-wise）运算：按元素运算将二元运算符应用于两个数组中的**每对位置对应的元素**

+ 对于任意具有相同形状的张量， 常见的标准算术运算符（+、-、 * 、/、`**`）都可以被**升级为按元素运算**

+ 我们可以在**同一形状的任意**两个张量上调用按元素操作

```python
x = torch.tensor([1.0, 2, 4, 8])
y = torch.tensor([2, 2, 2, 2])
x + y, x - y, x * y, x / y, x ** y # 标准运算符
torch.exp(x) # 求幂运算
torch.cat((X, Y), dim=0) # 张量连接-按行（上下）
torch.cat((X, Y), dim=1) # 张量连接-按列（左右）
X == Y # 逻辑运算符（每个位置两两比较返回布尔值）
X.sum() # 对所有元素进行求和（会产生一个单元素向量）
```

### 4、广播机制

**形状不同两个张量上，我们仍然可以通过调用广播机制来执行按元素操作。**

+ 首先，通过适当复制元素来扩展一个或两个数组， 以便在转换之后，两个张量具有相同的形状
+ 其次，对生成的数组执行按元素操作。

广播机制注意事项：

+ 每个张量至少有一个维度
+ 在遍历维度大小时, 从尾部维度开始遍历
+ 并且二者对应维度的值需满足：（**相等**） 或 （**其中一个值是1或不存在**）。

```python
e = torch.arange(3).reshape(3, 1)
f = torch.arange(2).reshape(1, 2)
print(e, f, sep="\n")
# e中的第一列三个元素被复制到第二列，f中第一行两个元素被复制到第二行和第三行
print(e+f)
```

```
tensor([[0],
        [1],
        [2]])
tensor([[0, 1]])
tensor([[0, 1],
        [1, 2],
        [2, 3]])
```

### 5、索引与切片

![pCM13nO.png](https://s1.ax1x.com/2023/06/16/pCM13nO.png)

### 6、节省内存

```python
# 运行一些操作可能会导致为新结果分配内存
d_id_before = id(d)
d = d +c
print(id(d) == d_id_before)
```

```
False
```

```python
# 执行原地操作（对元素进行改写，地址不变）
z = torch.zeros_like(d)  # 根据d的形状创建全0张量
print("id(z) : ", id(z))
z[:] = c + d
print("id(z) : ", id(z))
```

```
id(z) :  1833344835392
id(z) :  1833344835392
```

```python
# 如果在后续计算中没有重复使用c
# 我们也可以使用c[:] = c + d 或 c += d 来减少操作的内存开销
c_id_before = id(c)
c += d
print(id(c) == c_id_before)
```

```
True
```

### 7、张量类型转换

```python
# 转换为Numpy张量
c = torch.arange(12, dtype=torch.float32).reshape((3, 4))
c_np = c.numpy()
c_np_2_ts = torch.tensor(c_np)
# 将大小为1的张量转换为Python的标量
a = torch.tensor([3.5])
print(a, a.item(), float(a), int(a))
```

```
tensor([3.5000]) 3.5 3.5 3
```

### 8、数据预处理

常用函数：

+ makedirs 创建一个文件夹
+ os.path.join 合并字符串构建一个路径
+ read_csv 读取csv表格
+ fillna 对所有NA的域填一个值
+ get_dummies 使数据以one-hot（独热编码）格式编码，参数dummy_na表示是否对NA进行编码
+ torch.tensor() 可以将数值类型转换为张量类型

# 模型实战

## 一、线性回归模型

### 1、模型及相关介绍

**回归**（regression）是能为一个或多个自变量与因变量之间关系建模的一类方法。

+ 在自然科学和社会科学领域，回归经常用来表示输入和输出之间的关系。 

+ 在机器学习领域中的大多数任务通常都与预测（prediction）有关。 当我们想预测一个数值时，就会涉及到回归问题。

+ 常见的例子包括：预测价格（房屋、股票等）、预测住院时间（针对住院病人等）、 预测需求（零售销量等）。 但不是所有的预测都是回归问题。
+  在后面的章节中，我们将介绍分类问题。分类问题的目标是预测数据属于一组类别中的哪一个。

#### （1）基本元素

线性回归基于几个简单的假设： 

+ 首先，假设自变量 和因变量 之间的关系是线性的， 即 可以表示为 中元素的加权和，这里通常允许包含观测值的一些噪声；
+  其次，我们假设任何噪声都比较正常，如噪声遵循正态分布。

在机器学习的术语中该数据集称为**训练数据集**（training data set） 或训练集（training set）。 

+ 每行数据（比如一次房屋交易相对应的数据）称为样本（sample）， 也可以称为**数据点**（data point）或数据样本（data instance）。
+  我们把试图预测的目标（比如预测房屋价格）称为**标签**（label）或**目标**（target）。 
+ 预测所依据的自变量（面积和房龄）称为**特征**（feature）或**协变量**（covariate）。
+  通常，我们使用n来表示数据集中的样本数。

#### （2）线性模型

给定一个数据集，我们的目标是寻找模型的**权重w**和**偏置b** ，使得根据模型做出的预测大体符合数据里的真实价格。

+ 输出的预测值由输入特征通过线性模型的仿射变换决定
+  仿射变换由所选权重和偏置确定。

#### （3）代数表示

在机器学习领域，我们通常使用的是高维数据集，建模时采用线性代数表示法会比较方便。  

当我们的输入包含𝑑个特征时，我们将预测结果𝑦表示为：

**𝑦 ̂ = 𝑤1𝑥1+. . . +𝑤𝑑 𝑥𝑑 + 𝑏.**

+ 将所有特征放到向量𝐱 ∈ ℝ𝑑中， 
+ 并将所有权重放到向量𝐰 ∈ ℝ𝑑中，

+ 我们可以用点积形式来简洁地表达模型

**𝑦 ̂ = 𝐰⊤ 𝐱 + 𝑏.**

**噪声项**：无论我们使用什么手段来观察特征𝐗和标签𝐲， 都可能会出现少量的观测误差。因此，即使确信特征与标签的潜在关系是线性的，  我们也会加入一个**噪声项**来考虑观测误差带来的影响。

#### **（4）损失函数**

在开始寻找最好的模型参数（model parameters）𝐰和𝑏之前， 我们还需要两个东西：

+ 一种模型质量的度量方式；
+ 一种能够更新模型以提高模型预测质量的方法。

**损失函数**

在我们开始考虑如何用模型拟合（fit）数据之前，我们需要确定一个拟合程度的度量。

+ **损失函数**（loss function）能够量化目标的实际值与预测值之间的差距。
+ 通常我们会选择非负数作为损失，且数值越小表示损失越小，完美预测时的损失为0。

回归问题中最常用的损失函数是**平方误差函数**

#### （5）解析解

当模型和损失函数形式较为简单时，上面的误差最小化问题的解可以直接用公式表达出来。这类解叫作**解析解**（analytical solution） 。 **像线性回归这样的简单**问题存在解析解，但并不是所有的问题都存在解析解。

+ 首先，我们将偏置𝑏合并到参数𝐰中，合并方法是在包含所有参数的矩阵中附加一列。
+ 我们的预测问题是最小化‖𝐲 − 𝐗𝐰‖2。
+ 这在损失平面上只有一个临界点，这个临界点对应于整个区域的损失极小点。
+ 将损失关于𝐰的导数设为0，得到解析解：

**𝐰∗ = (𝐗⊤ 𝐗)−1𝐗⊤ 𝐲.**

**解析解与数值解**

+ 解析解可以进行很好的数学分析，但解析解对问题的限制很严格，导致它无法广泛应用在**深度学习**里。
+ 大多数深度学习模型只能通过优化算法有限次迭代模型参数来尽可能降低损失函数的值。这类解叫作**数值解**（numerical  solution）。

### 2、从零实现

在这一节中，我们将从零开始实现整个方法，  包括**数据流水线、模型、损失函数和小批量随机梯度下降优化器**。

#### （1）生成数据集

导入相关必要包

```python
%matplotlib inline 
import random
import torch
from d2l import torch as d2l
```

为了简单起见，我们将根据带有**噪声**的**线性模型**构造一个人造数据集。在下面的代码中：

+ 生成1000个样本数据集
+ 每个样本包含从标准正态分布中采样的2个特征
+ 数据集为一个矩阵𝐗 ∈ ℝ1000×2
+ 𝐲 = 𝐗𝐰 + 𝑏 + 𝜖 （𝐰 = [2, −3.4]⊤、𝑏 = 4.2 和噪声项𝜖）
+ 标准假设成立，即𝜖服从均值为0的正态分布

```python
def synthetic_data(w, b, num_examples):
    """生成y=Xw+b+噪声"""
    X = torch.normal(0, 1, (num_examples, len(w))) # 返回一个张量，正态分N(0,1)
    y = torch.matmul(X, w) + b # 向量的乘积，再加一个偏差b
    y += torch.normal(0, 0.01, y.shape) # 添加一个随机噪音，均值为0，方差为0.01，形状y.shape
    return X, y.reshape((-1, 1)) # reshape-1表示自动匹配，将标签数据转为所谓的列向量

# 设置真实的权重向量与偏差 
true_w = torch.tensor([2, -3.4])
true_b = 4.2
# 调用函数synthetic_data来生成数据
features, labels = synthetic_data(true_w, true_b, 1000)
```

注意， **features** 中的每一行都包含一个**二维数据样本**， **labels** 中的每一行都包含**一维标签值**（一个标量）。

#### （2）读取数据集

回想一下，训练模型时要对数据集进行遍历，每次抽取一小批量样本，并使用它们来更新我们的模型。

由于这个过程是训练机器学习算法的基础，所以有必要定义一个函数，  该函数能**打乱数据集**中的样本并以**小批量方式**获取数据。

在下面的代码中，我们定义一个 **data_iter 函数**， 该函数接收批量大小、特征矩阵和标签向量作为输入，生成大小为 batch_size 的小批量。 每个小批量包含一组特征和标签。

```python
def data_iter(batch_size, features, labels): # 出入批量大小、特征与标签
    num_examples = len(features) # 得到样本量
    indices = list(range(num_examples)) # 相当于得到样本的索引 
    # 这些样本是随机读取的，没有特定的顺序
    random.shuffle(indices) # 将得到的索引打乱顺序、进而实现对小批量的随机抽取 
    for i in range(0, num_examples, batch_size): # 在样本中小批量的大小抽取 
        # 得到小批量的索引、由于已经打乱了顺序、所以这就是对样本的随机抽取
        #  注意使用最小值函数、因为可能会在数据尽头无法满足批量的大小
        batch_indices = torch.tensor(indices[i: min(i + batch_size, num_examples)])
        #  相当于return、不过会保存这次提取的位置、下次循环从batch_size这个位置开始
        yield features[batch_indices], labels[batch_indices]
```

通常，我们利用GPU并行运算的优势，处理合理大小的“小批量”。 每个样本都可以**并行**地进行模型计算，且每个样本损失函数的梯度也可以被并行计算。 GPU可以在处理几百个样本时，所花费的时间不比处理一个样本时多太多。

我们直观感受一下小批量运算：读取第一个小批量数据样本并打印。 每个批量的特征维度显示批量大小和输入特征数。 同样的，批量的标签形状与 batch_size相等。

```python
batch_size = 10 # 将批量大小设置为10、尝试从数据迭代器中拿到一次数据

for X, y in data_iter(batch_size, features, labels): 
    print(X, '\n', y)
    break
```

#### （3）初始化模型参数

在我们开始用小批量随机梯度下降优化我们的模型参数之前， 我们需要先有一些参数。

+ 初始化模型参数、需要给出迭代的初始值、一般应尽可能地小

+ 这里的模型参数 w 和 b 是不同于上面定义的 true_w 和 true_b的
  + w 和 b 是向模型中传入并不断根据求得的 梯度 来更新的
  +  true_w 和 true_b 是用来生成数据的

+ 模型训练完成后、如果效果很好、那么 w 和 true_w 、b 和 true_b 都应该是非常接近的

在下面的代码中，我们通过从**均值为0**、**标准差为0.01**的**正态分布**中采样随机数来初始化权重，并将**偏置初始化为0**。

```python
w = torch.normal(0, 0.01, size=(2,1), requires_grad=True) # 回归系数服从均值为0、方差为0.1且形状为2行1列
b = torch.zeros(1, requires_grad=True) # 回归常数取0即可
```

#### （4）定义模型

接下来，我们必须**定义模型，将模型的输入和参数同模型的输出关联起来。** 要计算线性模型的输出， 我们只需计算**输入特征𝐗和模型权重𝐰的矩阵-向量乘法后加上偏置𝑏**。 注意，上面的𝐗𝐰是一个向量，而𝑏是一个标量。 当我们用一个向量加一个标量时，标量会被加到向量的每个分量上（广播机制）。

```py
# 定义线性回归模型
def linreg(X, w, b):
    """线性回归模型"""
    return torch.matmul(X, w) + b # 对应元素相乘再相加
```

#### （5）定义损失函数

因为需要计算损失函数的梯度，所以我们应该先定义损失函数。 这里我们使用平方损失函数。 在实现中，我们需要将真实值 y 的形状转换为和预测值 y_hat 的形状相同。

```python
# 定义损失函数
def squared_loss(y_hat, y):	# 传入拟合值与真实值
    """均方损失"""
    return (y_hat - y.reshape(y_hat.shape)) ** 2 / 2
```

#### （6）定义优化算法

+ 定义优化算法、即随机梯度下降（Stochastic Gradient Descent）

+ 求偏导是对损失函数 squared_loss 中的模型参数 w 和 b 求的偏导、实际上这里只是完成了**对参数的更新**、并没有使用反向传播在每一步中，使用从数据集中**随机**抽取的一个小批量，然后根据参数计算损失的梯度。

+ 接下来，朝着**减少损失的方向**更新我们的参数。注意更新完成后将**梯度清零**。

下面的函数实现小批量随机梯度下降更新。

+ 该函数接受模型参数集合、学习速率和批量大小作为输入。每一步更新的**大小**由学习速率 lr 决定。

+ 因为我们计算的损失是一个批量样本的总和，所以我们用批量大小（ batch_size ) 来**规范**化步长，这样步长大小就不会取决于我们对批量大小的选择。

```python
def sgd(params, lr, batch_size): # 传入参数列表、学习率以及小批量的大小
    """小批量随机梯度下降"""
    with torch.no_grad(): # 更新时解除梯度运算
        for param in params:
            # 学习率与参数梯度的乘积除以批量大小 
            param -= lr * param.grad / batch_size 
            param.grad.zero_()	# 将梯度清零
```



#### （7）训练

现在我们已经准备好了模型训练所有需要的要素，可以实现主要的**训练过程**部分了。

+ 理解这段代码至关重要，因为从事深度学习后， 你会一遍又一遍地看到几乎相同的训练过程。
+ 在每次迭代中，我们读取一小批量训练样本，并通过我们的模型来获得一组预测。

+ 计算完损失后，我们开始**反向传播**，存储每个参数的梯度。
+ 最后，我们调用优化算法 sgd 来更新模型参数。

概括为以下循环。

+ 初始化参数
+ 重复以下训练，直到完成
  + 计算梯度
  + 更新参数
+ 在每个迭代周期（epoch）中，我们使用 data_iter 函数遍历整个数据集， 并将训练数据集中所有样本都使用一次（假设样本数能够被批量大小整除）。
+ 这里的迭代周期个数 num_epochs 和学习率 lr 都是超参数，分别设为3和0.03。
+ 设置超参数很棘手，需要通过反复试验进行调整。

对线性回归模型进行训练

+ 设置迭代的轮次 num_epochs 、在每轮迭代中、不断地从数据迭代器 data_iter 中拿到小批量的数据 X 和 y 来对模型进行训练。
+ 计算模型在这些小批量上的拟合值 LinearRegression(X, w, b) 与真实值 y 之间的损失 (squared_loss) 、以此得知参数 w 和 b 该如何变化
+ 对参数 w 和 b 更新 (sgd) 后再在下一个小批量上重复、所有的数据均参与训练后即完成了一轮迭代

```python
lr = 0.03 # 学习率
num_epochs = 3 # 迭代次数
net = linreg # 选择模型 线性回归模型
loss = squared_loss # 损失函数 均方损失

# 对所有数据的迭代进行控制
for epoch in range(num_epochs): # 训练模型⼀共需要num_epochs个迭代周期 
    # 在所有数据的小批量上不断使损失最小化
    for X, y in data_iter(batch_size, features, labels): 
        l = loss(net(X, w, b), y) # X和y的小批量损失
        # 因为l形状是(batch_size,1)，而不是一个标量。l中的所有元素被加到一起， 
        # 并以此计算关于[w,b]的梯度
        l.sum().backward() # 对损失求和后计算[w, b]的梯度
        sgd([w, b], lr, batch_size) # 使用参数的梯度更新参数 # 将迭代轮次与损失打印出来
    with torch.no_grad():
        train_l = loss(net(features, w, b), labels)
        print(f'epoch {epoch + 1}, loss {float(train_l.mean()):f}')
```

输出：

```
epoch 1, loss 0.038150
epoch 2, loss 0.000139
epoch 3, loss 0.000048
```

因为我们使用的是自己合成的数据集，所以我们知道真正的参数是什么。 因此，我们可以通过**比较真实参数和通过训练学到的参数来评估训练的成功程度**。 事实上，真实参数和通过训练学到的参数确实非常接近。

```python
print(f'w的估计误差: {true_w - w.reshape(true_w.shape)}')
print(f'b的估计误差: {true_b - b}')
```

```
w的估计误差: tensor([-1.7548e-04,  1.9550e-05], grad_fn=<SubBackward0>)
b的估计误差: tensor([0.0001], grad_fn=<RsubBackward1>)
```

注意，我们不应该想当然地认为我们能够完美地求解参数。 在机器学习中，我们通常不太关心恢复真正的参数，而更关心如何**高度准确预测参数**。 幸运的是，即使是在复杂的优化问题上，随机梯度下降通常也能找到非常好的解。 其中一个原因是，在深度网络中存在**许多参数组合**能够实现高度精确的预测。

#### （8）小结

+ 我们学习了深度网络是如何实现和优化的。在这一过程中只能使用张量和自动微分，不需要定一层或复杂的优化器
+ 这一节只触及到了表面知识。

#### （9）练习

1. 如果我们将权重初始化为零，会发生什么。算法仍然有效吗？

   + 如果只有一层线性层的时候，如果权重初始化为 0 的话，再进行初次求导时其导数不为 0 对算法的基本没有影响；

   + 如果含有多层线性层的时候，如果权重初始化为 0 的话，网络中的神经元更新机制将完全相同，由于网络具有对称性，令各个 layer 中产生相同的梯度更新，最后导致所有的权重最后的值相同，无法进行正常收敛，多个隐藏神经元的作用就如同一个神经元，影响算法效果，算法基本无效。

2. 为什么在 squared_loss 函数中需要使用 reshape 函数？

   + 确保 y 和 y_hat 是同形式的，不然可能会出现两个向量维度不同的情况导致无法运行。

3. 尝试使用不同的学习率，观察损失函数值下降的快慢。

   + 学习率越大，损失函数值下降越快，但是如果学习率设置的过大，则有可能导致算法无法收敛。

4. 如果样本个数不能被批量大小整除， data_iter 函数的行为会有什么变化？

   + 设置了 indices[i: min(i + batch_size, num_examples)] ，样本个数不能被批量大小整除不会导致data_iter变化；

   + 不设置的话可能会报错。

#### （10）代码合集

```python
%matplotlib inline 
import random 
import torch
from d2l import torch as d2l

# 1、生成数据集

def synthetic_data(w, b, num_examples):
    """生成y=Xw+b+噪声"""
    X = torch.normal(0, 1, (num_examples, len(w))) # 返回一个张量，正态分N(0,1)
    y = torch.matmul(X, w) + b # 向量的乘积，再加一个偏差b
    y += torch.normal(0, 0.01, y.shape) # 添加一个随机噪音，均值为0，方差为0.01，形状y.shape
    return X, y.reshape((-1, 1)) # reshape-1表示自动匹配，将标签数据转为所谓的列向量

# 设置真实的权重向量与偏差 
true_w = torch.tensor([2, -3.4])
true_b = 4.2
# 调用函数synthetic_data来生成数据
features, labels = synthetic_data(true_w, true_b, 1000)

# 2、读取数据集

def data_iter(batch_size, features, labels): # 出入批量大小、特征与标签
    num_examples = len(features) # 得到样本量
    indices = list(range(num_examples)) # 相当于得到样本的索引 
    # 这些样本是随机读取的，没有特定的顺序
    random.shuffle(indices) # 将得到的索引打乱顺序、进而实现对小批量的随机抽取 
    for i in range(0, num_examples, batch_size): # 在样本中小批量的大小抽取 
        # 得到小批量的索引、由于已经打乱了顺序、所以这就是对样本的随机抽取
        #  注意使用最小值函数、因为可能会在数据尽头无法满足批量的大小
        batch_indices = torch.tensor(indices[i: min(i + batch_size, num_examples)])
        #  相当于return、不过会保存这次提取的位置、下次循环从batch_size这个位置开始
        yield features[batch_indices], labels[batch_indices]
        
batch_size = 10 # 将批量大小设置为10、尝试从数据迭代器中拿到一次数据

for X, y in data_iter(batch_size, features, labels): 
    print(X, '\n', y)
    break
    
# 3、初始化模型参数

w = torch.normal(0, 0.01, size=(2,1), requires_grad=True) # 回归系数服从均值为0、方差为0.1且形状为2行1列
b = torch.zeros(1, requires_grad=True) # 回归常数取0即可

# 4、定义模型

def linreg(X, w, b):
    """线性回归模型"""
    return torch.matmul(X, w) + b # 对应元素相乘再相加

# 5、定义损失函数

def squared_loss(y_hat, y):	# 传入拟合值与真实值
    """均方损失"""
    return (y_hat - y.reshape(y_hat.shape)) ** 2 / 2

# 6、定义优化算法

def sgd(params, lr, batch_size): # 传入参数列表、学习率以及小批量的大小
    """小批量随机梯度下降"""
    with torch.no_grad(): # 更新时解除梯度运算
        for param in params:
            # 学习率与参数梯度的乘积除以批量大小 
            param -= lr * param.grad / batch_size 
            param.grad.zero_()	# 将梯度清零

# 7、训练

lr = 0.03 # 学习率
num_epochs = 3 # 迭代次数
net = linreg # 选择模型 线性回归模型
loss = squared_loss # 损失函数 均方损失

# 对所有数据的迭代进行控制
for epoch in range(num_epochs): # 训练模型⼀共需要num_epochs个迭代周期 
    # 在所有数据的小批量上不断使损失最小化
    for X, y in data_iter(batch_size, features, labels): 
        l = loss(net(X, w, b), y) # X和y的小批量损失
        # 因为l形状是(batch_size,1)，而不是一个标量。l中的所有元素被加到一起， 
        # 并以此计算关于[w,b]的梯度
        l.sum().backward() # 对损失求和后计算[w, b]的梯度
        sgd([w, b], lr, batch_size) # 使用参数的梯度更新参数 # 将迭代轮次与损失打印出来
    with torch.no_grad():
        train_l = loss(net(features, w, b), labels)
        print(f'epoch {epoch + 1}, loss {float(train_l.mean()):f}')

print(f'w的估计误差: {true_w - w.reshape(true_w.shape)}')
print(f'b的估计误差: {true_b - b}')
```



### 3、简洁实现

在本节中，我们将介绍如何**通过使用深度学习框架来简洁地实现线性回归模型**。

#### （1）生成数据集

导入相关必要包

```python
import numpy as np
import torch
from torch.utils import data # 里面有一些处理数据的模块
from d2l import torch as d2l
```

```python
# 人工数据合成的函数，生成特征和标签
def synthetic_data(w, b, num_examples): #@save
    """生成y=Xw+b+噪声"""
    X = torch.normal(0, 1, (num_examples, len(w))) # 返回一个张量，正态分布N(0,1)
    y = torch.matmul(X, w) + b # 向量的乘积，再加一个偏差b
    y += torch.normal(0, 0.01, y.shape) # 添加一个随机噪音，均值为0，方差为0.01，
    return X, y.reshape((-1, 1)) # reshape-1表示自动匹配，将标签数据转为所谓的列向量
```

```python
true_w = torch.tensor([2, -3.4]) # 使⽤线性回归模型真实权重
true_b = 4.2 # 使⽤线性回归模型真实偏差
# 调用生成数据函数synthetic_data来生成数据，features是X，一个1000行2列的张量
# labels是一个1000行一列的张量 也就是y
features, labels = d2l.synthetic_data(true_w, true_b, 1000)
```

#### （2）读取数据集

我们可以调用框架现有的API来读取数据。

+ 我们每次只选取部分数据集进行训练。我们将 features 和 labels 作为API的参数传递，并通过数据迭代器指定 batch_size 。
+ 此外，布尔值 is_train 表示是否希望数据迭代器对象在每个迭代周期内打乱数据。

```python
def load_array(data_arrays, batch_size, is_train=True): #@save
    """构造一个PyTorch数据迭代器"""
    #将传入的特征和标签作为list传到TensorDataset里面得到一个pytorch的数据集（data
    #括号中的一个星号，表示对list解开入参，即把列表元素分别当作参数传入
    dataset = data.TensorDataset(*data_arrays)
    # DataLoader它也是一个可迭代的对象，可以使用多线程并行处理，加快读取数据集的速
    # 调用Dataloader每次从dataset里面挑选batch_size个样本出来
    # shuffle表示是否需要对数据打乱顺序，后加布尔值is_train则表示需要对数据打乱顺
    return data.DataLoader(dataset, batch_size, shuffle=is_train)
```

```python
batch_size = 10
# 将特征和标签传入load_array函数
data_iter = load_array((features, labels), batch_size)
```

这里我们使用 iter 构造Python迭代器，并使用 next从迭代器中获取第一项。

+ next函数：从迭代器中检索下一个项目。 如果给定了默认值，则在迭代器耗尽返回此默认值，否则会引发StopIteration。
  + iterator：要读取行的文件对象
  + default：如果迭代器耗尽则返回此默认值

```python
next(iter(data_iter)) # 转化成python的iter，再使用next函数得到X，y
```

将会输出迭代器中的各项内容

#### （3）定义模型

对于标准深度学习模型，我们可以**使用框架的预定义好的层**。这使我们只需关注使用哪些层来构造模型，而不必关注层的实现细节。

我们首先定义一个模型变量 **net** ，它是一个 Sequential 类的实例。Sequential 一个**有序的容器**，将多个层串联在一起。

+ 当给定输入数据时， Sequential 实例将数据传入到第一层
+ 然后将第一层的输出作为第二层的输入，以此类推

在下面的例子中，**线性回归模型只包含一个层**，因此实际上不需要 Sequential 。 但是由于以后几乎所有的模型都是多层的，在这里使用 Sequential 会让你熟悉“标准的流水线”。

**回顾**：线性回归用神经网络中的单层网络架构时， 这一单层被称为**全连接层**（fully- connected layer）， 因为它的每一个输入都通过矩阵-向量乘法得到它的每个输出。

在PyTorch中，全连接层在 Linear 类中定义：

+ 参数1：输入特征形状
+ 参数2：输出特征形状

```python
# nn是神经网络的缩写
from torch import nn
#输入维度为2（w），输出维度是1（label）
# 放入sequential容器内，其可以理解为层组成的一个list。 
# 注：线性回归就一般就是只有一Linear层
net = nn.Sequential(nn.Linear(2, 1))
```

#### （4）初始化模型参数

在使用 net 之前，我们需要初始化模型参数。

深度学习框架通常有预定义的方法来初始化参数。在这里，我们指定每个权重参数应该从**均值为0、标准差为0.01**的正态分布中随机采样，偏置参数将初始化为**零**。

由于线性回归是单层网络结构，我们通过 ：

+ **net[0]** 选择网络中的第一个图层
+ 然后使用 weight.data 和 bias.data 方法访问参数。
+ 然后再设定参数，那么权重w就使用normal(0,0.01)进行初始化（normal中的两个参数：均值，标准差），
+ 偏置ｂ就使用fill_进行初始化。

```python
net[0].weight.data.normal_(0, 0.01) # 初始化权重
net[0].bias.data.fill_(0) # 初始化偏置
```

#### （5）定义损失函数

**计算均方误差使用的是** **MSELoss** **类，也称为平方**L2**范数**。 默认情况下，它返回所有样本损失的平均值。

```python
loss = nn.MSELoss()
```

#### （6）定义优化算法

**小批量随机梯度下降算法**是一种优化神经网络的标准工具

PyTorch在 optim 模块中实现了该算法的许多变种：

+ 当我们(**实例化一个** **SGD 实例**)时，我们要指定优化的参数，
+ 可通过 net.parameters() 从我们的模型中获得对应优化算法所需的**超参数字典**，
+ 小批量随机梯度下降只需要设置 lr 值，这里设置为0.03。

```python
# net.parameters()：net里面的所有参数，包括w，b，然后指定学习率
trainer = torch.optim.SGD(net.parameters(), lr=0.03)
```

#### （7）训练

为了更好的衡量训练效果，我们计算每个迭代周期后的损失，并打印它来监控训练过程。

```python
num_epochs = 3 # 定义轮次
for epoch in range(num_epochs): # 迭代3次
    for X, y in data_iter: # 每次拿出一个小批量
        # 这里的net现在自带了参数，不用像手动识别时写成net（X,w,b）了
        # loss是将net（X）得到的预测值与真实值y做比较
        l = loss(net(X) ,y) 
        # 梯度归零
        trainer.zero_grad()
        # 计算梯度，这里不用像之前手动一样，要去求sum了，因为pytorch已经把sum求好了
        l.backward() 
        # 模型更新 
        trainer.step()
    l = loss(net(features), labels) 
    print(f'epoch {epoch + 1}, loss {l:f}')
```

输出：

```
epoch 1, loss 2.250273
epoch 2, loss 0.442633
epoch 3, loss 0.001808
```

下面我们**比较**生成数据集的**真实参数**和通过有限数据训练获得的**模型参数**。

要访问参数，我们首先从 **net** 访问所需的层，然后**读取**该层的权重和偏置。

 正如在从零开始实现中一样，我们估计得到的参数与生成数据的真实参数非常接近。

```python
w = net[0].weight.data
print('w的估计误差：', true_w - w.reshape(true_w.shape)) 
b = net[0].bias.data
print('b的估计误差：', true_b - b)
```

输出：

```
w的估计误差： tensor([ 0.0141, -0.0443])
b的估计误差： tensor([0.0369])
```

#### （8）小结

+ 我们可以使用PyTorch的高级API更简洁地实现模型。

+ 在PyTorch中，data 模块提供了数据处理工具， nn 模块定义了大量的神经网络层和常见损失函数。

+ 我们可以通过 **_** 结尾的方法将参数替换，从而初始化参数。

#### （9）练习

1. 如果将小批量的总损失替换为小批量损失的平均值，你需要如何更改学习率？
   + 可以将学习率缩小为之前的 1/n 。
2. 查看深度学习框架文档，它们提供了哪些损失函数和初始化方法？用Huber损失代替原损失。
   + 1、通过查看官方技术文档可知部分损失函数：binary_cross_entropy、binary_cross_entropy_with_logits、poisson_all_loss、cross_entropy等;
3. 你如何访问线性回归的梯度？
   + net[0].weight.grad
   + net[0].bias.grad
   + 假如是多层网络，可以用一个self.xxx=某层，然后在外面通过net.xxx.weight.grad和net.xxx.bias.grad把梯度给拿出来。

#### （10）代码合集

```python
import numpy as np
import torch
from torch.utils import data
from d2l import torch as d2l

# 1、生成数据集

def synthetic_data(w, b, num_examples): # 人工数据合成的函数，生成特征和标签
    """生成y=Xw+b+噪声"""
    X = torch.normal(0, 1, (num_examples, len(w))) # 返回一个张量，正态分布N(0,1)
    y = torch.matmul(X, w) + b # 向量的乘积，再加一个偏差b
    y += torch.normal(0, 0.01, y.shape) # 添加一个随机噪音，均值为0，方差为0.01，
    return X, y.reshape((-1, 1)) # reshape-1表示自动匹配，将标签数据转为所谓的列向量

true_w = torch.tensor([2, -3.4]) # 使用线性回归模型真实权重
true_b = 4.2 # 使用线性回归真实偏差
# 调用生成数据函数 synthetic_data 来生成数据
# features 为 x，一个1000行2列的张量
# labels 为 y，一个1000行1列的张量
features, labels = d2l.synthetic_data(true_w, true_b, 1000)

# 2、读取数据集

def load_array(data_arrays, batch_size, is_train = True): #@save
    '''pytorch数据迭代器'''
    # 将传入的特征和标签作为list传入到TensorData里面得到一个pytorch的数据集
    # 括号中的星号标识对list解开入参，即把队列元素分别作为参数传入
    dataset = data.TensorDataset(*data_arrays)
    # DataLoader 他是一个可迭代的对象，可以使用多线程并行处理，加快读取数据集的速度
    # 调用 DataLoader 每次从 dataset 里面提下旬 batch_size 个样本出来
    # shuffle 标识是否需要对数据打乱顺序，后加布尔值 is_train 则表示需要对数据打乱顺序
    return data.DataLoader(dataset, batch_size, shuffle = is_train)

batch_size = 10
# 将特征和标签传入 load_array 函数（ 和手动实现中data_iter使用方法相同）
data_iter = load_array((features, labels), batch_size)

# 转化成python的iter，在使用next函数得到x，y
next(iter(data_iter))

# 3、定义模型

from torch import nn # nn 神经网络的缩写
# 输入维度为2（w），输出维度是1（label）
# 放入 sequential 容器内，其可以理解为层组成的一个list。
# 注意：于一个存放各层数据的list，单层时也可以只用Linear
net = nn.Sequential(nn.Linear(2, 1))  

# 4、初始化模型参数

# 使用 net[0] 选择神经网络中的第一层
# 然后使用 weight.data 和 bias.data 方法访问参数
# 然后再设定参数，那么权重 w 就是用 normal(0, 0,01) 进行初始化（normal(均值, 标准差)）
# 偏置 b 就使用 fill_ 进行初始化
net[0].weight.data.normal_(0, 0.01) # 正态分布
net[0].bias.data.fill_(0)

# 5、定义损失函数

loss = torch.nn.HuberLoss()

# 6、定义优化算法

# net.paramters(): net 里面的所有参数，包括w，b，然后指定学习率
trainer = torch.optim.SGD(net.parameters(), lr=0.03) # optim module中的SGD

# 7、训练

num_epochs = 3 # 迭代三次
for epoch in range(num_epochs): # 每次拿出一个小批量
    for X, y in data_iter:
        # 这里的 net 现在自带了参数，不用像手动识别时写成 net(X, w, b)
        # loss 是将 net(X) 得到的预测值与真实值 y 做比较
        l = loss(net(X), y)
        # 梯度归零
        trainer.zero_grad()
        # 计算梯度
        # 这里不需要手动求sum，因为 pytorch 已经把 sum 求好了
        l.backward()
        # 模型更新
        trainer.step()
    l = loss(net(features), labels)
    print(f'epoch {epoch+1}, loss {l:f}')

# 查看误差
w = net[0].weight.data
print('w的估计误差：', true_w - w.reshape(true_w.shape))
b = net[0].bias.data
print('b的估计误差：', true_b - b)
```



## 二、softmax回归模型

### 1、模型及相关介绍

**softmax回归**可以用于预测多少的问题。 比如预测房屋被售出价格，或者棒球队可能获得的胜场数，又或者患者住院的天数。

通常，机器学习实践者用分类这个词来描述两个有微妙差别的问题：

+ 我们只对样本的”硬性“类别感兴趣，即属于哪个类别
+ 我们希望得到“软性”类别。即得到属于每个类别的概率。

这两者的界限往往很模糊，其中的一个原因是：即使我们只关心硬性类别，但是我们仍然使用软性类别的模型。

#### （1）分类问题

**如果类别间有一些自然顺序，** 比如说我们试图预测{婴儿, 儿 , 青 年, 青年人, 中年人, 老年人}， 那么将这个问题转变为回归问题，并且保留这种格式是有意义的。

幸运的是，一般的分类问题并不与类别之间的自然顺序有关。统计学家很早以前就发明了一种表示分类数据的简单方法：**独热编码（one-hot encoding）**，独热编码是一个向量，它的分量和类别一样多，类别对应的分量设置为1，其他所有分量设置为0。

#### （2）网络架构

为了估计所有可能类别的条件概率，我们需要一个有多个输出的模型，每个类别对应一个输出。为了解决线性模型的分类问题，我们需要和输出一样多的仿射函数（affine function），每个输出对应于它自己的仿射函数。

我们可以用神经网络图来描述这个计算过程。 与线性回归一样，**softmax回归**也是一个**单层神经网络**，且**softmax回归的输出层也是全连接层**。

#### （3）全连接层的参数开销

正如我们将在后续章节中看到的，在深度学习中，全连接层无处不在。然而，顾名思义，全连接层是“完全”连接的，可能有很多可学习的参数。具体来说，对于任何具有𝑑个输入和𝑞个输出的全连接层， 参数开销为(dq)，这个数字在实践中可能高得令人望而却步。幸运的是，将𝑑个输入转换为𝑞个输出的成本可以减少到( dp/n )，其中超参数𝑛可以由我们灵活指定，以在实际应用中平衡参数节约和模型有效性。

#### （4）softmax运算

softmax运算**不会**改变**未规范化**的预测𝐨之间的**顺序**，只会**确定**分配给每个类别的概率。尽管softmax是一个**非线性函数**，但softmax回归的**输出**仍然由输入特征的**仿射变换决定**， 因此，softmax回归是一个**线性模型**（linear  model）。

#### （5）损失函数

我们需要一个损失函数来度量预测的效果， 我们将使用最大似然估计。

**交叉熵损失**（cross-entropy loss）

### 2、从零实现

我们将使用**Fashion-MNIST数据集**， 并设置数据迭代器的**批量大小为256**。 Fashion-MNIST数据集包含了10个类别的图像，分别是：t-shirt（T恤），trouser（牛仔裤），pullover（套衫），dress（裙子），coat（外套），sandal（凉鞋），shirt（衬衫），sneaker（运动鞋），bag（包），ankle boot（短靴）。

#### （1）导入数据集

导入相关必要包

```python
import torch
from IPython import display
from d2l import torch as d2l
```

```python
# 导入数据集
# 设置数据迭代器的批次大小
batch_size = 256
# 得到训练集和测试集的迭代器
train_iter, test_iter = d2l.load_data_fashion_mnist(batch_size)
```

#### （2）初始化模型参数

原始数据集中的每个样本都是28 × 28的图像。 在本节中，我们将**展平每个图像**，把它们看作长度为784的**向量**，现在我们暂时只把每个像素位置看作一个特征。

在softmax回归中，我们的输出与类别一样多。 因为我们的**数据集有10个类别**，所以网络**输出维度为10**。 因此，权重将构成一个784 × 10的矩阵，偏置将构成一个1 × 10的行向量。与线性回归一样，我们将使用正态分布初始化我们的权重 W ，偏置初始化为0。

```python
# 初始化模型参数
# 把每个28x28的样本图像展平为长度784的向量
# 因为有10个类别，所以网络输出维度为10
num_inputs = 784
num_outputs = 10
# 权重将构成一个784×10的矩阵，偏置将构成一个1×10的行向量。
# 与线性回归一样，我们将使用正态分布初始化我们的权重W，偏置初始化为0
W = torch.normal(0, 0.01, size=(num_inputs, num_outputs), requires_grad=True)
b = torch.zeros(num_outputs, requires_grad=True)
```

#### （3）定义softmax操作

**实现softmax**由三个步骤组成：

+ 对每个项求幂（使用 exp ）；
+ 对每一行求和（小批量中每个样本是一行），得到每个样本的规范化常数；
+ 将每一行除以其规范化常数，确保结果的和为1。

```python
# 定义softmax操作
def softmax(X):
    X_exp = torch.exp(X)
    partition = X_exp.sum(1, keepdim=True)
    return X_exp / partition # 这里应用了广播机制
```

注意，虽然这在数学上看起来是正确的，但我们在代码实现中有点草率。 矩阵中的非常大或非常小的元素可能造成数值上溢或下溢，但我们没有采取措施来防止这点。

#### （4）定义模型

定义softmax操作后，我们可以**实现softmax回归模型**。 下面的代码定义了输入如何通过网络映射到输出。 注意，将数据传递到模型之前，我们使用 reshape 函数将每张原始图像展平为向量。

```python
# 定义模型
def net(X):
    return softmax(torch.matmul(X.reshape((-1, W.shape[0])), W) + b)
```

#### （5）定义损失函数

接下来我们实现交叉熵损失函数。

```python
# 定义损失函数
def cross_entropy(y_hat, y): # 实现交叉熵损失函数
    # 对真实类别y预测的y_hat求对数
    return - torch.log(y_hat[range(len(y_hat)), y]) 
```

#### （6）分类精度

给定预测概率分布 y_hat ，当我们必须输出硬预测（hard prediction）时， 我们通常选择预**测概率最高**的类。

 这里定义一个实用程序类 Accumulator ，用于对多个变量进行累加。 在下面的 evaluate_accuracy 函数中， 我们在 Accumulator 实例中创建了**2个变量**， 分别用于存储**正确预测的数量**和**预测的总数量**。 当我们遍历数据集时，两者都将随着时间的推移而累加。

```python
# 分类精度
# Accumulator实例中，创建了两个变量，
# 用于分别存储正确预测的数量和预测的总数量
class Accumulator:
    """在n个变量上累加""" 
    def __init__ (self, n):
        self.data = [0.0] * n

    def add(self, *args):
        self.data = [a + float(b) for a, b in zip(self.data, args)]

    def reset(self):
        self.data = [0.0] * len(self.data)

    def __getitem__ (self, idx):
        return self.data[idx]

def accuracy(y_hat, y):
    """计算预测正确的数量"""
     # 如果y_hat行列数均大于1，是一个二维矩阵 
    if len(y_hat.shape) > 1 and y_hat.shape[1] > 1:
        # 求每一行中的最大值
        y_hat = y_hat.argmax(axis=1) 
    # 将y_hat类型转化为y的类型，并和y进行比较，
    # 相等则说明类别正确为1，否则错误为0。
    cmp = y_hat.type(y.dtype) == y 
    # 最后将布尔类型转化为y的类型(int)，并进行求和，
    # 得到预测正确的数量，再转成float是为了之后算比值能保留小数点后
    return float(cmp.type(y.dtype).sum()) 

#同样，对于任意数据迭代器data_iter可访问的数据集， 
#我们可以评估在任意模型net的准确率。
def evaluate_accuracy(net, data_iter):
    """计算在指定数据集上模型的精度"""
    if isinstance(net, torch.nn.Module):
        # 将模型设置为评估模式，
        # 即不计算梯度了，只做一个forward-path
        net.eval() 
    metric = Accumulator(2) # Accumulator实例，正确预测数、预测总数
    with torch.no_grad():
        for X, y in data_iter:
            # (正确数，样本总数)，然后累加
            metric.add(accuracy(net(X), y), y.numel())
    return metric[0] / metric[1]
```

#### （7）训练

在这里，我们重构**训练过程的实现**以使其可重复使用。 首先，我们定义一个函数来训练一个迭代周期。 请注意， updater 是更新模型参数的常用函数，它接受批量大小作为参数。 它可以是 d2l.sgd 函数，也可以是框架的内置优化函数。

```python
# 训练
# 首先定义一个函数来训练一个迭代周期
def train_epoch_ch3(net, train_iter, loss, updater):
    """训练模型一个迭代周期（定义见第3章）"""
    # 将模型设置为训练模式
    if isinstance(net, torch.nn.Module): 
        net.train()
    # 训练损失总和、训练准确度总和、样本数
    metric = Accumulator(3)
    for X, y in train_iter: 
        # 计算梯度并更新参数
        y_hat = net(X)
        l = loss(y_hat, y)
        if isinstance(updater, torch.optim.Optimizer): 
            # 使用PyTorch内置的优化器和损失函数 updater.zero_grad()
            l.sum().backward()
            updater.step()
        else:
            # 使用定制的优化器和损失函数 
            l.sum().backward()
            updater(X.shape[0])
        # 训练损失总和、训练准确度总和、样本数
        metric.add(float(l.sum()), accuracy(y_hat, y), y.numel())
    # 返回训练损失和训练精度
    return metric[0] / metric[2], metric[1] / metric[2]

```

在展示训练函数的实现之前，我们定义一个在**动画中绘制数据**的**实用程序类** Animator 。

```python
# 训练过程可视化
class Animator:
    """在动画中绘制数据"""
    def __init__ (self, xlabel=None, ylabel=None, 
                  legend=None, xlim=None, ylim=None, 
                  xscale='linear', yscale='linear',
                  fmts=('-', 'm--', 'g-.', 'r:'), 
                  nrows=1, ncols=1, figsize=(3.5, 2.5)):
        # 增量地绘制多条线 
        if legend is None: 
            legend = []
        d2l.use_svg_display()
        self.fig, self.axes = d2l.plt.subplots(nrows, ncols, figsize=figsize)
        if nrows * ncols == 1: 
            self.axes = [self.axes, ]
        # 使用lambda函数捕获参数
        self.config_axes = lambda: d2l.set_axes(
            self.axes[0], xlabel, ylabel, xlim, ylim, xscale, yscale, legend)
        self.X, self.Y, self.fmts = None, None, fmts
    def add(self, x, y):
        # 向图表中添加多个数据点
        if not hasattr(y, "__len__"): 
            y = [y]
        n = len(y)
        if not hasattr(x, "__len__"): 
            x = [x] * n
        if not self.X:
            self.X = [[] for _ in range(n)]
        if not self.Y:
            self.Y = [[] for _ in range(n)]
        for i, (a, b) in enumerate(zip(x, y)):
            if a is not None and b is not None: 
                self.X[i].append(a)
                self.Y[i].append(b)
        self.axes[0].cla()
        for x, y, fmt in zip(self.X, self.Y, self.fmts):
            self.axes[0].plot(x, y, fmt)
        self.config_axes()
        display.display(self.fig)
        display.clear_output(wait=True)
```

接下来我们实现一个**训练函数**， 它会在 train_iter 访问到的训练数据集上训练一个模型 net 。该训练函数将会运行多个迭代周期（由 num_epochs 指定）。 在每个迭代周期结束时，利用 test_iter 访问到的测试数据集对模型进行评估。 我们将利用 Animator 类来可视化训练进度。

```python
def train_ch3(net, train_iter, test_iter, loss, num_epochs, updater):
    """训练模型（定义见第3章）"""
    animator = Animator(xlabel='epoch', xlim=[1, num_epochs], ylim=[0.3, 0.9],
                        legend=['train loss', 'train acc', 'test acc'])
    for epoch in range(num_epochs):
        train_metrics = train_epoch_ch3(net, train_iter, loss, updater)
        test_acc = evaluate_accuracy(net, test_iter)
        animator.add(epoch + 1, train_metrics + (test_acc,))
    train_loss, train_acc = train_metrics
    assert train_loss < 0.5, train_loss
    assert train_acc <= 1 and train_acc > 0.7, train_acc
    assert test_acc <= 1 and test_acc > 0.7, test_acc
```

作为一个从零开始的实现，我们使用中定义的 **小批量随机梯度下降来优化模型的损失函数**，设置学习率为0.1。

```python
lr = 0.1

def updater(batch_size): # 小批量随机梯度下降来优化模型的损失函数
    return d2l.sgd([W, b], lr, batch_size)
```

现在，我们**训练模型10个迭代周期**。 请注意，迭代周期（ num_epochs ）和学习率（ lr ）都是可调节的超参数。 通过更改它们的值，我们可以提高模型的分类精度。

```python
num_epochs = 10
train_ch3(net, train_iter, test_iter, cross_entropy, num_epochs, updater)
```

#### （8）预测

现在训练已经完成，我们的模型已经准备好**对图像进行分类预测**。 给定一系列图像，我们将比较它们的实际标签（文本输出的第一行）和模型预测（文本输出的第二行）。

```python
# 预测
def predict_ch3(net, test_iter, n=20):
    for X, y in test_iter:
        break
    trues = d2l.get_fashion_mnist_labels(y)
    preds = d2l.get_fashion_mnist_labels(net(X).argmax(axis=1))
    titles = [true + '\n' + pred for true, pred in zip(trues, preds)]
    d2l.show_images(X[0:n].reshape((n, 28, 28)), 1, n, titles=titles[0:n])

predict_ch3(net, test_iter)
```

#### （9）小结

+ 借助softmax回归，我们可以训练多分类的模型
+ 训练softmax回归循环模型与训练线性回归模型非常相似：先读取数据、再定义模型和损失函数、然后用优化算法训练模型。

#### （10）练习

+ 在本节中，我们直接实现了基于数学定义softmax运算的 softmax 函数。这可能会导致什么问题？提示：尝试计算exp(50)的大小。
  + 如果网络参数初始化不恰当，如：输入数值较大的噪音，基于数学定义的softmax运算可能造成溢出（结果超过long类型的范围）。
+ 本节中的函数 cross_entropy 是根据交叉熵损失函数的定义实现的。它可能有什么问题？提示：考虑对数的定义域。
+ 你可以想到什么解决方案来解决上述两个问题？
+ 返回概率最大的分类标签总是最优解吗？例如，医疗诊断场景下你会这样做吗？
+ 假设我们使用softmax回归来预测下一个单词，可选取的单词数目过多可能会带来哪些问题?

#### （11）代码合集

```python
import torch
from IPython import display
from d2l import torch as d2l

# 1、导入数据集

# 设置数据迭代器的批次大小
batch_size = 256
# 得到训练集和测试集的迭代器
train_iter, test_iter = d2l.load_data_fashion_mnist(batch_size)

# 2、初始化模型参数

# 把每个28x28的样本图像展平为长度784的向量
# 因为有10个类别，所以网络输出维度为10
num_inputs = 784
num_outputs = 10
# 权重将构成一个784×10的矩阵，偏置将构成一个1×10的行向量。
# 与线性回归一样，我们将使用正态分布初始化我们的权重W，偏置初始化为0
W = torch.normal(0, 0.01, size=(num_inputs, num_outputs), requires_grad=True)
b = torch.zeros(num_outputs, requires_grad=True)

# 3、定义softmax操作

def softmax(X):
    X_exp = torch.exp(X)
    partition = X_exp.sum(1, keepdim=True)
    return X_exp / partition # 这里应用了广播机制

# 4、定义模型
def net(X):
    return softmax(torch.matmul(X.reshape((-1, W.shape[0])), W) + b)

# 5、定义损失函数

def cross_entropy(y_hat, y): # 实现交叉熵损失函数
    # 对真实类别y预测的y_hat求对数
    return - torch.log(y_hat[range(len(y_hat)), y]) 

# 6、分类精度

# Accumulator实例中，创建了两个变量，
# 用于分别存储正确预测的数量和预测的总数量
class Accumulator:
    """在n个变量上累加""" 
    def __init__ (self, n):
        self.data = [0.0] * n

    def add(self, *args):
        self.data = [a + float(b) for a, b in zip(self.data, args)]

    def reset(self):
        self.data = [0.0] * len(self.data)

    def __getitem__ (self, idx):
        return self.data[idx]

def accuracy(y_hat, y):
    """计算预测正确的数量"""
     # 如果y_hat行列数均大于1，是一个二维矩阵 
    if len(y_hat.shape) > 1 and y_hat.shape[1] > 1:
        # 求每一行中的最大值
        y_hat = y_hat.argmax(axis=1) 
    # 将y_hat类型转化为y的类型，并和y进行比较，
    # 相等则说明类别正确为1，否则错误为0。
    cmp = y_hat.type(y.dtype) == y 
    # 最后将布尔类型转化为y的类型(int)，并进行求和，
    # 得到预测正确的数量，再转成float是为了之后算比值能保留小数点后
    return float(cmp.type(y.dtype).sum()) 

#同样，对于任意数据迭代器data_iter可访问的数据集， 
#我们可以评估在任意模型net的准确率。
def evaluate_accuracy(net, data_iter):
    """计算在指定数据集上模型的精度"""
    if isinstance(net, torch.nn.Module):
        # 将模型设置为评估模式，
        # 即不计算梯度了，只做一个forward-path
        net.eval() 
    metric = Accumulator(2) # Accumulator实例，正确预测数、预测总数
    with torch.no_grad():
        for X, y in data_iter:
            # (正确数，样本总数)，然后累加
            metric.add(accuracy(net(X), y), y.numel())
    return metric[0] / metric[1]

# 7、训练

# 首先定义一个函数来训练一个迭代周期
def train_epoch_ch3(net, train_iter, loss, updater):
    """训练模型一个迭代周期（定义见第3章）"""
    # 将模型设置为训练模式
    if isinstance(net, torch.nn.Module): 
        net.train()
    # 训练损失总和、训练准确度总和、样本数
    metric = Accumulator(3)
    for X, y in train_iter: 
        # 计算梯度并更新参数
        y_hat = net(X)
        l = loss(y_hat, y)
        if isinstance(updater, torch.optim.Optimizer): 
            # 使用PyTorch内置的优化器和损失函数 updater.zero_grad()
            l.sum().backward()
            updater.step()
        else:
            # 使用定制的优化器和损失函数 
            l.sum().backward()
            updater(X.shape[0])
        # 训练损失总和、训练准确度总和、样本数
        metric.add(float(l.sum()), accuracy(y_hat, y), y.numel())
    # 返回训练损失和训练精度
    return metric[0] / metric[2], metric[1] / metric[2]

# 训练过程可视化
class Animator:
    """在动画中绘制数据"""
    def __init__ (self, xlabel=None, ylabel=None, 
                  legend=None, xlim=None, ylim=None, 
                  xscale='linear', yscale='linear',
                  fmts=('-', 'm--', 'g-.', 'r:'), 
                  nrows=1, ncols=1, figsize=(3.5, 2.5)):
        # 增量地绘制多条线 
        if legend is None: 
            legend = []
        d2l.use_svg_display()
        self.fig, self.axes = d2l.plt.subplots(nrows, ncols, figsize=figsize)
        if nrows * ncols == 1: 
            self.axes = [self.axes, ]
        # 使用lambda函数捕获参数
        self.config_axes = lambda: d2l.set_axes(
            self.axes[0], xlabel, ylabel, xlim, ylim, xscale, yscale, legend)
        self.X, self.Y, self.fmts = None, None, fmts
    def add(self, x, y):
        # 向图表中添加多个数据点
        if not hasattr(y, "__len__"): 
            y = [y]
        n = len(y)
        if not hasattr(x, "__len__"): 
            x = [x] * n
        if not self.X:
            self.X = [[] for _ in range(n)]
        if not self.Y:
            self.Y = [[] for _ in range(n)]
        for i, (a, b) in enumerate(zip(x, y)):
            if a is not None and b is not None: 
                self.X[i].append(a)
                self.Y[i].append(b)
        self.axes[0].cla()
        for x, y, fmt in zip(self.X, self.Y, self.fmts):
            self.axes[0].plot(x, y, fmt)
        self.config_axes()
        display.display(self.fig)
        display.clear_output(wait=True)
        
def train_ch3(net, train_iter, test_iter, loss, num_epochs, updater):
    """训练模型（定义见第3章）"""
    animator = Animator(xlabel='epoch', xlim=[1, num_epochs], ylim=[0.3, 0.9],
                        legend=['train loss', 'train acc', 'test acc'])
    for epoch in range(num_epochs):
        train_metrics = train_epoch_ch3(net, train_iter, loss, updater)
        test_acc = evaluate_accuracy(net, test_iter)
        animator.add(epoch + 1, train_metrics + (test_acc,))
    train_loss, train_acc = train_metrics
    assert train_loss < 0.5, train_loss
    assert train_acc <= 1 and train_acc > 0.7, train_acc
    assert test_acc <= 1 and test_acc > 0.7, test_acc

lr = 0.1

def updater(batch_size): # 小批量随机梯度下降来优化模型的损失函数
    return d2l.sgd([W, b], lr, batch_size)

num_epochs = 10
train_ch3(net, train_iter, test_iter, cross_entropy, num_epochs, updater)

# 8、预测

def predict_ch3(net, test_iter, n=20):
    for X, y in test_iter:
        break
    trues = d2l.get_fashion_mnist_labels(y)
    preds = d2l.get_fashion_mnist_labels(net(X).argmax(axis=1))
    titles = [true + '\n' + pred for true, pred in zip(trues, preds)]
    d2l.show_images(X[0:n].reshape((n, 28, 28)), 1, n, titles=titles[0:n])

predict_ch3(net, test_iter)
```



### 3、简洁实现

 我们发现通过深度学习框架的**高级API**能够使实现 softmax线性回归变得更加容易。 同样，通过深度学习框架的高级API也能**更方便**地实现softmax回归模型。 本节继续使用Fashion-MNIST数据集，并保持批量大小为256。

#### （1）导入数据集

导入相关必要包

```python
import torch
from torch import nn #神经网络模块相关包
from d2l import torch as d2l
```

```python
#  导入数据集
# 设置数据迭代器的批次大小
batch_size = 256
# 得到训练集和测试集的迭代器
train_iter, test_iter = d2l.load_data_fashion_mnist(batch_size)
```

#### （2）初始化模型参数

**softmax回归的输出层是一个全连接层**。 因此，为了实现我们的模型， 我们只需在 Sequential 中添加一个带有10个输出的全连接层。 同样，在这里 Sequential 并不是必要的， 但它是实现深度模型的基础。 我们仍然以均值0和标准差0.01随机初始化权重。

```python
# 初始化参数模型
# PyTorch不会隐式地调整输入的形状
# 因此我们在线性层前定义了展平层（flatten）
# 来调整网络输入的形状 
# 注意定义的nn.Sequential nn.Flatten()的作用就是转为2维
# 输入 256*28*28 转换为 256*784

net = nn.Sequential(nn.Flatten(), nn.Linear(784, 10))
def init_weights(m): # m 是一个module
    if type(m) == nn.Linear: # 如果是全连接层
        nn.init.normal_(m.weight, std=0.01) # w初始化为正态分布

net.apply(init_weights); # apply对net中所有的linear遍历
```

**函数说明**

+ nn.Sequential() 是一个有序的容器。神经网络模块将按照传入顺序依次添加到相关计算中  
+ nn.Flatten() 把任意维度的Tensor变为一个2D的Tensor。把第0维度保留，剩下的维度全部展成一个向量。  
+ nn.LInear() 定义一个神经网络的线性全连接层。(输入神经元个数，输出神经元个数，偏置)

#### （3）定义损失函数

```python
# 交叉熵损失函数，并同时计算softmax后的置信度 
# softmax的过程运用了y_hat=softmax(w*x+b) 
loss = nn.CrossEntropyLoss()
# loss = nn.CrossEntropyLoss(reduction='none') # 新版本建议加参数
```

**函数说明**

+ nn.CrossEntropyLoss() 参数说明
  + elementwise_mean：默认情况，表明对N个样本的loss进行求平均值并返回
  + sum：表明对N个样本的loss求和
  + none：表明直接返回N个样本的loss

#### （4）定义优化算法

在这里，我们使用学习率为**0.1**的**小批量随机梯度下降**作为优化算法。 这与我们在线性回归例子中的相同，这说明了优化器的普适性。

```python
# net.parameters() 在SGD里传入所有的参数 学习率lr=0.1
# torch.optim.SGD() 梯度随机下降优化算法
trainer = torch.optim.SGD(net.parameters(), lr=0.1)
```

**SGD**：***Stochastic Gradient Descent***，随机梯度下降。是通过不断沿着反梯度方向更新参数求解，可以随机采样b个样本近似损失，每次随机选择一个mini-batch去计算梯度，在minibatch-loss上的梯度显然是original-loss上的梯度的无偏估计，因此利用minibatch-loss上的梯度可以近似original-loss上的梯度，并且每走一步只需要遍历一个 minibatch（一～几百）的数据。

#### （5）训练

```python
#使用已经分装好的API
# 设置训练轮次
num_epochs = 10
# 训练数据并进行可视化展示
d2l.train_ch3(net, train_iter, test_iter, loss, num_epochs, trainer)
```

#### （6）预测

```python
def predict_ch3(net, test_iter, n=20):
    for X, y in test_iter:
        break
    trues = d2l.get_fashion_mnist_labels(y)
    preds = d2l.get_fashion_mnist_labels(net(X).argmax(axis=1))
    titles = [true + '\n' + pred for true, pred in zip(trues, preds)]
    d2l.show_images(X[0:n].reshape((n, 28, 28)), 1, n, titles=titles[0:n])

predict_ch3(net, test_iter)
# 或直接调用API
# d2l.predict_ch3(net, test_iter)
```

#### （7）小结

+ 使用深度学习框架的高级API，我们可以更简洁地实现softmax回归。

+ 从计算的角度来看，实现softmax回归比较复杂。
+ 在许多情况下，深度学习框架在这些著名的技巧之外采取了额外的预防措施，来确保数值的稳定性。
+ 这使我们避免了在实践中从零开始编写模型时可能遇到的陷阱。

#### （8）练习

+ 尝试调整超参数，例如批量大小、迭代周期数和学习率，并查看结果。
+ 增加迭代周期的数量。为什么测试精度会在一段时间后降低？我们怎么解决这个问题？
  + 不改变训练集的情况下增加迭代周期的数量，将会过度拟合，测试精度会随之降低。
  + 可通过添加训练集或进行模型L2规范化处理解决

#### （9）代码合集

```python
import torch
from torch import nn #神经网络模块相关包
from d2l import torch as d2l

# 1、导入数据集

#  导入数据集
# 设置数据迭代器的批次大小
batch_size = 256
# 得到训练集和测试集的迭代器
train_iter, test_iter = d2l.load_data_fashion_mnist(batch_size)

# 2、初始化参数模型

# PyTorch不会隐式地调整输入的形状
# 因此我们在线性层前定义了展平层（flatten）
# 来调整网络输入的形状 
# 注意定义的nn.Sequential nn.Flatten()的作用就是转为2维
# 输入 256*28*28 转换为 256*784

net = nn.Sequential(nn.Flatten(), nn.Linear(784, 10))
def init_weights(m): # m 是一个module
    if type(m) == nn.Linear: # 如果是全连接层
        nn.init.normal_(m.weight, std=0.01) # w初始化为正态分布

net.apply(init_weights); # apply对net中所有的linear遍历

# 3、定义损失函数

# 交叉熵损失函数，并同时计算softmax后的置信度 
# softmax的过程运用了y_hat=softmax(w*x+b) 
loss = nn.CrossEntropyLoss()
# loss = nn.CrossEntropyLoss(reduction='none') # 新版本建议加参数

# 4、定义优化算法

# net.parameters() 在SGD里传入所有的参数 学习率lr=0.1
# torch.optim.SGD() 梯度随机下降优化算法
trainer = torch.optim.SGD(net.parameters(), lr=0.1)

# 5、训练

#使用已经分装好的API
# 设置训练轮次
num_epochs = 10
# 训练数据并进行可视化展示
d2l.train_ch3(net, train_iter, test_iter, loss, num_epochs, trainer)

# 6、预测

def predict_ch3(net, test_iter, n=20):
    for X, y in test_iter:
        break
    trues = d2l.get_fashion_mnist_labels(y)
    preds = d2l.get_fashion_mnist_labels(net(X).argmax(axis=1))
    titles = [true + '\n' + pred for true, pred in zip(trues, preds)]
    d2l.show_images(X[0:n].reshape((n, 28, 28)), 1, n, titles=titles[0:n])

predict_ch3(net, test_iter)
# 或直接调用API
# d2l.predict_ch3(net, test_iter)
```



## 三、多层感知机

### 1、模型及相关介绍

多层感知机(MLP)是深度神经网络(DNN)的**基础算法**，有时候提起DNN就是指MLP，感知机跟SVM优化的目标一致，损失函数不同(前者分母限制为1，后者分子限制为1)，**神经网络由输入层(第一层)、隐藏层(中间层)和输出层(最后一层)构成**。

在MLP中,层与层之间是**全连接**的，前向传播算法用于求解相邻两层间输出的关系，某层的输入就是上一层的输出，反向传播算法(BP)用于求解各层的系数关系矩阵W和偏倚向量b，求解MLP采取梯度下降法。

#### （1）隐藏层

我们在线性回归模型中描述了**仿射变换**，

+ 它是一个带有偏置项的线性变换。 softmax回归的模型架构

+ 通过单个仿射变换将我们的输入直接映射到输出，然后进行softmax操作。

如果我们的标签通过仿射变换后确实与我们的输入数据相关，那么这种方法就足够了。 但是，仿射变换中的**线性**是一个很强的假设。

#### （2）激活函数

**激活函数**（activation function）通过计算加权和并加上偏置来确定神经元是否应该被激活，它们将输入信号转换为输出的可微运算。

+ 大多数激活函数都是非线性的。

+ 由于激活函数是深度学习的基础

下面简要介绍一些常见的**激活函数**。

**ReLU函数**

最受欢迎的激活函数是修正线性单元（Rectified linear unit，*ReLU*）， 因为它实现简单，同时在各种预测任务中表现良好。 **ReLU**提供了一种非常简单的**非线性变换**。

通俗地说，ReLU函数通过将相应的活性值设为0，仅保留正元素并丢弃所有负元素。

**sigmoid函数**

对于一个定义域在ℝ中的输入， *sigmoid*函数将输入变换为区间*(0, 1)*上的输出。 因此，*sigmoid*通常称为**挤压函数**（squashing function）： 它将范围（-inf, inf）中的任意输入压缩到区间（0, 1）中的某个值。

当人们的注意力逐渐转移到基于梯度的学习时，由于sigmoid函数是一个自然的选择，因为它是一个平滑的、可微的阈值单元近似，当我们想要将输出视作**二元分类问题**的概率时， sigmoid仍然被广泛用作输出单元上的激活函数。然而，sigmoid在隐藏层中已经较少使用，它在大部分时候被更简单、更容易训练的**ReLU所取代**。

**tanh函数**

与sigmoid函数类似， **tanh**（双曲正切函数）也能将其输入压缩转换到区间(-1, 1)上。

#### （3）小结

+ 多层感知机在输出层和输入层之间增加一个或多个全连接隐藏层，并通过激活函数转换隐藏层的输出。
+ 常用的激活函数包括ReLU函数、sigmoid函数和tanh函数。

#### （4）练习

以下答案来自[知乎]([李沐深度学习-感知机-jupyter 课后作业及报错 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/617688054))

+ 计算pReLU激活函数的导数。

![](https://pic2.zhimg.com/80/v2-47e47e5fbe0fe4d11f8fba4021322549_720w.webp)

当x>0时：

![](https://pic2.zhimg.com/80/v2-666ee98035d72075dc76e01637fe3d19_720w.webp)

当x<=0时：

![](https://pic3.zhimg.com/80/v2-fdb7a31bfb442d0a6948dd59b7f15f96_720w.webp)

+ 证明一个仅使用ReLU（或pReLU）的多层感知机构造了一个连续的分段线性函数。
+ 证明tanh(𝑥) + 1 = 2 sigmoid(2𝑥)。

![](https://pic1.zhimg.com/80/v2-6305119f161ccfec843956df0c9eb764_720w.webp)

+ 假设我们有一个非线性单元，将它一次应用于一个小批量的数据。你认为这会导致什么样的问题？

### 2、从零实现

现在让我们尝试自己实现一个多层感知机。 为了与之前softmax回归获得的结果进行比较， 我们将继续使用Fashion-MNIST图像分类数据集。

#### （1）导入数据集

导入相关必要包

```python
import torch
from torch import nn # 神经网络模块相关的包
from d2l import torch as d2l # 后续加载其中的图形绘制函数
```

导入数据集

```python
#  导入数据集
# 设置数据迭代器的批次大小
batch_size = 256
# 得到训练集和测试集的迭代器
train_iter, test_iter = d2l.load_data_fashion_mnist(batch_size)
```

#### （2）初始化模型参数

Fashion-MNIST中的每个图像由 28 × 28 = 784个灰度像素值组成。 所有图像共分为10个类别。 忽略像素之间的空间结构， 我们可以将每个图像视为具有784个输入特征 和10个类的简单分类数据集。 首先，我们将实现一个具有**单隐藏层的多层感知机**， 它包含**256个隐藏单元**。 

注意，我们可以将这两个变量都视为超参数。 通常，我们选择2的若干次幂作为层的宽度。 因为内存在硬件中的分配和寻址方式，这么做往往可以在计算上更高效。

我们用几个**张量**来表示我们的参数。 注意，对于每一层我们都要**记录**一个权重矩阵和一个偏置向量。 跟以前一样，我们要为这些参数的损失的梯度分配内存。

```python
num_inputs, num_outputs, num_hiddens = 784, 10, 256 # hiddens即为隐藏单元个数
# randn：以给定的形状创建一个随机数组，数组元素符合标准正态分布 N(0,1) 
# nn.Parameter：可加可不加
W1 = nn.Parameter(torch.randn(num_inputs, num_hiddens, requires_grad=True) * 0.01)
# 偏置的行数等于此层权重的列数 
b1 = nn.Parameter(torch.zeros(num_hiddens, requires_grad=True))
# 上一层的列数为下一层的行数
W2 = nn.Parameter(torch.randn(num_hiddens, num_outputs, requires_grad=True) * 0.01)
# 偏置的行数等于此层的列数
b2 = nn.Parameter(torch.zeros(num_outputs, requires_grad=True))
params = [W1, b1, W2, b2]
```

对于矩阵参数的分析：

+ 权重W = tensor（矩阵行，矩阵列），行数为输入数，列数为输出数，也就是说，上一层的列数，做下一层的行数
+ 偏置的行数，即偏置的个数应该等于输出的个数，即等于此层的输出数 b=（此层列数）
+ num_inputs 和 num_outputs 都是数据决定的，num_hiddens 是隐藏层的个数
+ torch.nn.Parameter 继承 torch.Tensor，其作用将一个不可训练的类型为Tensor的参数转化为可训练的类型为 parameter 的参数，并将这个参数绑定到 module 里面，成为  module 中可训练的参数
+ w1初始为随机的，行数为784，列数为256，需要更新梯度，偏差b1的大小和隐藏层相同为256，设置为0
+ w2的输入为隐藏层和输出，256和10，偏差b2是最终输出的10
+ 其中 [w1,b1,w2,b2] 里面 w1、b1是第一层，w2、b2是第二层

#### （3）激活函数

```python
# 激活函数
def relu(X):
    # a是和X相同形状的零矩阵 不加like会报错说zero的参数必须是一个整数的元组，而不能是张量
    a = torch.zeros_like(X)
    # 矩阵X与0作比较，返回大的值，即只会保留正数部分
    return torch.max(X, a)
```

#### （4）模型

因为我们忽略了空间结构， 所以我们使用 reshape 将每个二维图像转换为一个长度为 num_inputs 的向量。

```python
def net(X):
    # 和隐藏层那里一样，行数作为输入，列数作为输出，这里是整个网络的输入层，输入设为-1，即根据数据集的输入自己定
    # num_inputs作为输入层的输出，然后进到隐藏层的输入层
    X = X.reshape((-1, num_inputs))
    # 矩阵乘法用@符号，当然也可以用matmul（X,W1）来实现
    H = relu(X@W1 + b1) # 这里“@”代表矩阵乘法
    return (H@W2 + b2) # 返回第一层的输出和第二层的权重作乘法，再加上偏置

```

#### （5）定义损失函数

由于我们已经从零实现过softmax函数（上一节softmax模型）， 因此在这里我们直接使用高级API中的内置函数来计算softmax和交叉熵损失。

```python
loss = nn.CrossEntropyLoss() # 用交叉熵做损失函数
```

#### （6）训练

幸运的是，**多层感知机的训练过程与softmax回归的训练过程完全相同**。 可以直接调用 d2l 包的 train_ch3 函数， 将迭代周期数设置为10，并将学习率设置为0.1。

```python
# 训练
# 设置迭代周期数、学习率
num_epochs, lr = 10, 0.1
# 在SGD里传入所有的参数，学习率0.1
updater = torch.optim.SGD(params, lr=lr)
# 训练数据并进行可视化展示
d2l.train_ch3(net, train_iter, test_iter, loss, num_epochs, updater)
```

#### （7）预测

为了对学习到的模型进行评估，我们将在一些测试数据上应用这个模型。

```python
d2l.predict_ch3(net, test_iter)
```

#### （8）小结

+ 手动实现一个简单的多层感知机是很容易的。然而如果有大量的层，从零开始实现多层感知机会变得很麻烦（例如，要命名和记录模型的参数）。

#### （9）练习

+ 在所有其他参数保持不变的情况下，更改超参数 num_hiddens 的值，并查看此超参数的变化对结果有何影响。确定此超参数的最佳值。
+ 尝试添加更多的隐藏层，并查看它对结果有何影响。
+ 改变学习速率会如何影响结果？保持模型架构和其他超参数（包括轮数）不变，学习率设置为多少会带来最好的结果？
+ 通过对所有超参数（学习率、轮数、隐藏层数、每层的隐藏单元数）进行联合优化，可以得到的最佳结果是什么？
+ 描述为什么涉及多个超参数更具挑战性。
+ 如果要构建多个超参数的搜索方法，你能想到的最聪明的策略是什么？
  + 逐步优化法（Baby Siting），大范围下手动寻找一个较优解，后使用上下迭代的方式对超参数进行逐个改进，半自动求最优解
  + 构建网格搜索法（Grid Search），将所有超参数规定好范围映射到网格中，使用遍历操作遍历网格每种超参数搭配方案求得最优解
  + 有限随机参数法（Random Search），定义随机参数的有限范围，使用不重复随机的参数生成获取最优解

#### （10）代码合集

```python
import torch
from torch import nn # 神经网络模块相关的包
from d2l import torch as d2l # 后续加载其中的图形绘制函数

#  1、导入数据集

# 设置数据迭代器的批次大小
batch_size = 256
# 得到训练集和测试集的迭代器
train_iter, test_iter = d2l.load_data_fashion_mnist(batch_size)

# 2、初始化参数

num_inputs, num_outputs, num_hiddens = 784, 10, 256 # hiddens即为隐藏单元个数
# randn：以给定的形状创建一个随机数组，数组元素符合标准正态分布 N(0,1) 
# nn.Parameter：可加可不加
W1 = nn.Parameter(torch.randn(num_inputs, num_hiddens, requires_grad=True) * 0.01)
# 偏置的行数等于此层权重的列数 
b1 = nn.Parameter(torch.zeros(num_hiddens, requires_grad=True))
# 上一层的列数为下一层的行数
W2 = nn.Parameter(torch.randn(num_hiddens, num_outputs, requires_grad=True) * 0.01)
# 偏置的行数等于此层的列数
b2 = nn.Parameter(torch.zeros(num_outputs, requires_grad=True))
params = [W1, b1, W2, b2]

# 3、激活函数

def relu(X):
    # a是和X相同形状的零矩阵 不加like会报错说zero的参数必须是一个整数的元组，而不能是张量
    a = torch.zeros_like(X)
    # 矩阵X与0作比较，返回大的值，即只会保留正数部分
    return torch.max(X, a)

# 4、模型

def net(X):
    # 和隐藏层那里一样，行数作为输入，列数作为输出，这里是整个网络的输入层，输入设为-1，即根据数据集的输入自己定
    # num_inputs作为输入层的输出，然后进到隐藏层的输入层
    X = X.reshape((-1, num_inputs))
    # 矩阵乘法用@符号，当然也可以用matmul（X,W1）来实现
    H = relu(X@W1 + b1) # 这里“@”代表矩阵乘法
    return (H@W2 + b2) # 返回第一层的输出和第二层的权重作乘法，再加上偏置

# 5、损失函数

loss = nn.CrossEntropyLoss() # 用交叉熵做损失函数

# 6、训练

# 设置迭代周期数、学习率
num_epochs, lr = 10, 0.1
# 在SGD里传入所有的参数，学习率0.1
updater = torch.optim.SGD(params, lr=lr)
# 训练数据并进行可视化展示
d2l.train_ch3(net, train_iter, test_iter, loss, num_epochs, updater)

# 7、预测

d2l.predict_ch3(net, test_iter)
```



### 3、简洁实现

正如你所期待的，我们可以通过**高级API**更简洁地实现多层感知机。

#### （1）导入数据集

导入相关必要包

```python
import torch
from torch import nn # 神经网络模块相关的包
from d2l import torch as d2l # 后续加载其中的图形绘制函数
```

导入数据集

```python
#  导入数据集
# 设置数据迭代器的批次大小
batch_size = 256
# 得到训练集和测试集的迭代器
train_iter, test_iter = d2l.load_data_fashion_mnist(batch_size)
```

#### （2）初始化模型参数

与softmax回归的简洁实现相比， **唯一的区别**是我们添加了**2个全连接层**（之前我们只添加了1个全连接层）。 

+ 第一层是**隐藏层**，它包含256个隐藏单元，并使用了ReLU激活函数。 
+ 第二层是**输出层**。

```python
'''
pyTorch不会隐式地调整输入的形状。
因此我们在定义线性层前定义了展平层（flatten），来调整网络输入的形状。
注意：定义的nn.Sequential()、nn.Flatten() 的作用就是转为2维；
      （输入256*28*28 转换为 256*784）
'''

net = nn.Sequential(nn.Flatten(),
                    nn.Linear(784, 256), # 全连接层，相当于 Y=WX+b。（入784出256）
                    nn.ReLU(),           # 激活函数 ReLU
                    nn.Linear(256, 10))  # 全连接层，相当于Y=WX+b。（入256出10）

# 初始化（每个全连接层的权重 weight）
def init_weights(m):  # m 是一个 module
    if type(m) == nn.Linear:               # 如果是全连接层
        nn.init.normal_(m.weight, std=0.01)  # 从正态分布N~(0,0.01)中随机抽取填充m.weight

net.apply(init_weights)  # apply 对 net 中所有的 Linear 遍历，初始化网络权重
```

**函数说明**

+ nn.Sequential() 是一个有序的容器。神经网络模块将按照传入顺序依次添加到相关计算中  
+ nn.Flatten() 把任意维度的Tensor变为一个2D的Tensor。把第0维度保留，剩下的维度全部展成一个向量。  
+ nn.LInear() 定义一个神经网络的线性全连接层。(输入神经元个数，输出神经元个数，偏置)  

#### （3）定义损失函数

```python
# 定义损失函数
loss = nn.CrossEntropyLoss()
```

**函数说明**

nn.CrossEntropyLoss() 参数说明
+ elementwise_mean：默认情况，表明对N个样本的loss进行求平均值并返回
+ sum：表明对N个样本的loss求和
+ none：表明直接返回N个样本的loss

#### （4）定义优化算法

```python
# 定义优化算法
batch_size, lr, num_epochs = 256, 0.1, 10 # 设置批次大小，学习率，迭代次数
trainer = torch.optim.SGD(net.parameters(), lr=lr) # 在SGD李传入所有的参数，学习率0.1
```

#### （5）训练

```python
d2l.train_ch3(net, train_iter, test_iter, loss, num_epochs, trainer) # 训练数据并进行可视化展示
```

#### （6）预测

```python
d2l.predict_ch3(net, test_iter) # 验证测试集并进行可视化展示
```

#### （7）小结

+ 我们可以使用高级API更简洁地实现多层感知机。

+ 对于相同的分类问题，多层感知机的实现与softmax回归的实现相同，只是多层感知机的实现里增加了带有激活函数的隐藏层。

#### （8）练习

+ 尝试添加不同数量的隐藏层（也可以修改学习率），怎么样设置效果最好？

```python
# 添加隐藏层
net = nn.Sequential(nn.Flatten(),
                    nn.Linear(784, 256),  # 全连接层，相当于 Y=WX+b。（入784出256）
                    nn.ReLU(),            # 激活函数 ReLU
                    nn.Linear(256, 64),   # 全连接层，相当于 Y=WX+b。（入256出64）
                    nn.ReLU(),            # 激活函数 ReLU
                    nn.Linear(64, 10))    # 全连接层，相当于Y=WX+b。（入64出10）
```

+ 尝试不同的激活函数，哪个效果最好？

```python
# 更换激活函数
net = nn.Sequential(nn.Flatten(),
                    nn.Linear(784, 256),  # 全连接层，相当于 Y=WX+b。（入784出256）
                    nn.Sigmoid(),            # 激活函数 Sigmoid
                    nn.Linear(256, 10))   # 全连接层，相当于Y=WX+b。（入64出10）
```

+ 尝试不同的方案来初始化权重，什么方法效果最好？

#### （9）代码合集

```python
import torch
from torch import nn # 神经网络模块相关的包
from d2l import torch as d2l # 后续加载其中的图形绘制函数

#  1、导入数据集

# 设置数据迭代器的批次大小
batch_size = 256
# 得到训练集和测试集的迭代器
train_iter, test_iter = d2l.load_data_fashion_mnist(batch_size)

# 2、初始化模型参数

'''
pyTorch不会隐式地调整输入的形状。
因此我们在定义线性层前定义了展平层（flatten），来调整网络输入的形状。
注意：定义的nn.Sequential()、nn.Flatten() 的作用就是转为2维；
      （输入256*28*28 转换为 256*784）
'''

net = nn.Sequential(nn.Flatten(),
                    nn.Linear(784, 256), # 全连接层，相当于 Y=WX+b。（入784出256）
                    nn.ReLU(),           # 激活函数 ReLU
                    nn.Linear(256, 10))  # 全连接层，相当于Y=WX+b。（入256出10）

# 初始化（每个全连接层的权重 weight）
def init_weights(m):  # m 是一个 module
    if type(m) == nn.Linear:               # 如果是全连接层
        nn.init.normal_(m.weight, std=0.01)  # 从正态分布N~(0,0.01)中随机抽取填充m.weight

net.apply(init_weights)  # apply 对 net 中所有的 Linear 遍历，初始化网络权重

# 3、定义损失函数

loss = nn.CrossEntropyLoss()

# 4、定义优化算法

batch_size, lr, num_epochs = 256, 0.1, 10 # 设置批次大小，学习率，迭代次数
trainer = torch.optim.SGD(net.parameters(), lr=lr) # 在SGD李传入所有的参数，学习率0.1

# 5、训练

d2l.train_ch3(net, train_iter, test_iter, loss, num_epochs, trainer) # 训练数据并进行可视化展示

# 6、预测

d2l.predict_ch3(net, test_iter) # 验证测试集并进行可视化展示
```

