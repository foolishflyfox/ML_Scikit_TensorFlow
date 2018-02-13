---
export_on_save:
 html: true
---

# 函数用法摘记

[TOC]

## Pyton 中 module 和 package 的区别

任何的Python文件都是一个module，module名是Python文件名去掉 `.py`之后留下来的部分。一个 package 是很多个 module 的集合。

**划重点：** 一个 module 是一个单独的Python文件，一个 package 是一个包含了很多Python文件的文件夹，为了区别于那些只是包含了很多Python脚本的文件夹，package 中通常含有一个 `__init__.py` 文件。package 可以进行多层嵌套，每层的 package 都有其自身的 `__init__.ph` 文件。

module 和 package 只是在系统的层面上存在区别。当你 import 一个 package 或者 module 之后，Python 通常创建一个 `module` 类型的对象。当你在 import 了一个 package，只有在 `__init__.py` 中 变量/函数/类 是对外可见的，sub-package 或 module 是不可见的。

## 为什么类中的 method 定义必需显示地传入self

1. 通过`self`可以更容易看出你是在使用实例的方法/属性还是一个局部函数/变量；在C++中，你可以通过变量的声明来识别出该变量是否是局部变量，而在 Python 中就根本没有变量声明这种说法，所以你需要通过类的定义来对变量进行确认；
1. 这意味着如果要明确地调用某个类（不是某个实例）的方法，不需要特别的语法支持。在C++中，如果你想要调用被子类所重载的父类中的方法，你需要用到操作符`::`，而在Python中，你只需写 `basename.methodname(self, <argument list>)` 就可以了。这对于 `__init__()` 方法特别有用，一般的使用情形是：子类中的方法想要扩展父类中同名方法的功能，但有需要调用父类中的这个方法；
1. 不传入变量 `self` 可以看做是C++中的静态函数(通常在定义的上一行写`@staticmethod`)，传入 `self` 可以看做是C++中的普通成员函数；

## 为什么在Python中没有 ++ 、-- 操作符

因为没有必要。在Python中，for 循环通常是 `for i in range(number)` ，而在 C/C++ 等语言中，for 循环通常是 `for(i=0; i<number; ++i)`，所以 ++、-- 操作符在Python中不常用。而且 ++ 分为前++、后++，-- 也一样，这就因为这需要增加4个操作码，这个问题不是说添加这两个操作符有没有意义，而是说我们值不值得添加（毕竟做什么事情都需要在付出和获得之间进行权衡）。另外，需要说明的是 `++a` 并不会报错，但是要注意，这里的 ++ 并不是一个运算符，而是一个正号，所以，`a = +a = ++a = +++a = ...`；

## `@staticmethod` 和 `@classmethod`

`@staticmethod` 定义静态方法，可以不传入任何参数，也不能调用其他的成员函数；`@classmethod` 至少有一个参数，且参数名通常为 `cls`，通过该参数可以调用属于类的变量和方法。有这两句声明的函数，都可以通过类直接进行调用，而不一定要创建实例。

## 命名惯例

Python中的命名规则：对用户公开的API中，应该体现的是用法而非实现的过程。

需要避免的名字：不要使用l(小写的L)、O(大写的o)、I(大写的i)；因为这3个字母在有的字体中非常容易混淆；

- package 和 module 的命名：模块名应该简短、全部小写，为了增强可读性，可以在模块名中添加下划线；包名类似，简短、小写，不太提倡使用下划线。一些用 C/C++ 编写的扩展模块，其命名时，需要在名称最前面加上下划线，如`_socket`。
- class 命名：通常使用首字母大写命名法(大驼峰命名法)。对于Python内建的名称，有单独的约定：大多数的内建名称是一个单独的单词(如果两个单词，直接并到一起)，例外是：exception名称以及内建常量名(e.g. True,False,None)使用的是首字母大写命名法。
- exception 命名：因为异常通常所以一个class，所以满足上一条命名规范。通常，如果以个异常是一个错误的话，通常应该在名称后添加`Error`后缀。
- 全局变量命名：（希望只是在模块内部使用）该命名规则与函数的命名规则相同。如果想通过 `from
 modulename import *` 导入模块内部元素时，可以通过 `__all__` 机制防止全局变量的污染，具体方法是：在module中定义`__all__`变量为一个list，其中的每一个元素都是字符串，表示变量名或函数名（相当于进行了注册），只有注册了的变量和函数才会在`from module import *`后被导出。
- 函数与变量的命名：全部字母小写，为了增强可读性，可以在单词之间添加下划线。变量的命名也遵循相同的惯例。
- 函数/方法的参数命名：对于实例的方法，第一个参数通常是`self`；对于类的方法，第一个参数通常是`cls`；如果函数的参数名与保留关键字冲突，最好是在后面加个下划线，而不是使用缩写或某些简化的写法。因此，`class_` 的参数名要比 `clss` 要好。（使用同义词进行替换更好）
- 方法名称和实例名称：全小写，单词间用下划线隔开。对于非公开的成员变量、成员函数，在名称最前面加一个下划线；最前面是双下划线，是为了避免被子类覆盖；
- 常量：常量名通常是全大写，并用下划线分隔，如`MAX_OVERFLOW` 和 `TOTAL`
- 继承的设计：如果你不知道某个类或实例的属性、方法是应该公开还是非公开，那就选择非公开吧。毕竟从非公开变为公开要比从公开变为非公开要来得容易。


## 类中内置的重要函数接口

- `__getitem__` :当用户对类的实例进行方括号(square brackets)操作是，该函数将会被调用；

## Build-in Functions

### open

打开文件，返回一个相应的文件对象。如果指定的文件不能被打开，将会抛出一个 `OSError` 错误。

## str

- `s.encode('latin-1')` :将字符串实例转换成bytes类型；更通用的是下面的，有Unicode时会出错；
- `s.encode('utf-8')` :将字符串实例转换成bytes类型；

## Numpy module

Numpy 是Python用于科学计算的基础库，它包含了：强大的 N维数组对象，精巧的（能够广播的）函数，集成 C/C++、Fortran代码的工具，能够进行线性代数运算、傅里叶变换以及获得随机数。

- `np.c_[X,Y,Z]` :参数还可以增加，例子是指将函数相同的X，Y，Z合成一个array对象；

### random

- `numpy.random.permutation` :将一个序列随机重排列(序列作为参数传入)，或者是返回一个重排列的range；
- `numpy.random.seed` :为产生随机化数设置一个种子；

## Pandas module

Pandas 模块是Python进行数据处理的利器。

- `pandas.read_csv` :从csv文件读取数据，创建一个pandas.DataFrame对象用于数据存储；
- `pandas.isnull` :判断DataFrame或Series中的元素，若为整数型，判断是否为 NaN，如果是对象，判断是否为 None/NaN
- `apply` :Pandas中的 DataFrame、Series都有该方法，用迭代的方法对其中的元素做操作；
- `~` :Pandas中的DataFrame、Series可使用，当其中的数据类型为bool，则~df可以将True、False进行翻转；
- `where` :Pandas中的 DataFrame、Series都要改方法，用于修改不满足条件的数据；
- `copy` :创建一个拷贝；
- `sort_values` :按值进行重新排序；参数`ascending`指定是否递增排序；
- `sort_index` :按索引重新排序；
- `fillna` :将值为 NaN 的元素换成指定数值；
- `drop` :将指定的行/列删除(对于DataFrame),删除指定的元素(对Series)
- `dropna` :对 DataFrame 将含NaN的行删除，可以通过指定`subset`设置需要检查的列；Series删除NaN的元素
- `astype` :将 DataFrame 或 Series 中的数据都转换成特定的类型，注意如果在其中存在不能转换的元素，那将发生一个错误；

### DataFrame

- `pandas.DataFrame.to_csv` :将DataFrame对象写入一个csv文件中(默认逗号分隔)；
- `pandas.iloc` :以整数索引作为位置信息选择 DataFrame 中的数据；可以是一个整数、一个整数数组(列表)、一个整数切片、一个布尔数组、一个可调用的函数；
- `pandas.loc` :以label的方式选择DataFrame中的数据；可以是一个label，一个label数组或列表，列表的切片，布尔数组(功能就和iloc相同了)，一个可调用的函数；
- `pandas.DataFrame.info` :显示DataFrame对象的简明摘要信息；
- `pandas.DataFrame['attribute_name'].value_counts` :统计某个属性中的数据类别及其实例数；
- `pandas.DataFrame.describe` :数据中**数值型**属性的摘要信息；
- `pandas.DataFrame.hist` :绘制数据中所有数值型属性的柱状图,其中的`bins`参数用于指定柱数，`figsize`参数用于指定每个柱状图的尺寸；
- `pandas.DataFrame.reset_index` :将当前DataFrame中的index值变为数据中的一列；
- `pandas.DataFrame.drop` :删除某行或某属性，如果`axis`为0，表示删除某行，如果是1，表示参数某属性；第一个参数`labels`可以是一个值或一个数组，为数组时可同时删除多个；`inplace`表示是修改实例还是讲修改结果返回；
- `pandas.DataFrame.plot(kind="scatter", x="column1", y="column2", alpha=0.1)`: 以DataFrame中的 column1 列为横坐标，以 column2 列为纵坐标，透明度为0.1进行绘图；`s`指定半径，`label`显示点的含义，`cmap` 指定色图，`c`指定颜色取值，`colorbar`指定是否显示颜色条；
- `pandas.DataFrame.corr` :计算属性之间的相关性，其返回值还是一个 `DataFrame` 实例，其 columns 和 index 都是属性名；`corr`是correlation前4字母；

### Series

### plotting

- `pandas.plotting.scatter_matrix` :绘制属性间的散点图，`kind=scatter`表示绘制散点图，`x=attribute1`,`y=attribute2`,`alpha=0.2`；

## Scikit learn

Scikit learn的特点：简单、高效的数据挖掘和数据分析工具；免费；基于Numpy、SciPy和matplotlib；开源，使用BSD协议

- preprocessing :该模块包括 缩放、中心化、归一化、二值化和填充缺失值等方法。

- LabelEncoder :用于将文本标签编码成数值码；

- OneHotEncoder :用于将数值码编码成独热向量；

- LabelBinarizer :将文本标签编码成独热向量，类似于 `LabelEncoder` + `OneHotEncoder`；

- MinMaxScaler :用于将数据进行 最小-最大缩放，`feature_range` 超参数指定缩放范围。中间的数值不能有 NaN。

- StandardScaler :用于将数据进行标准化；

#### pipeline

- `sklearn.pipeline.Pipeline` :构建流水线，最终的转换器也有 `fit()`、`transform()`、`fit_transform()` 属性；

#### Imputer

- `sklearn.precessing.Imputer(strategy="median")` :构建用中位值填充缺失值的填充器，设创建的实例为 `imputer`，`strategy`可以取`median`/`mean`/`most_frequent`；
- `imputer.fit(df)` :适应某个DataFrame实例；
- `imputer.transform(df)` :将训练好的填充器用于df，返回一个 Numpy 数组；

#### model_selection

- `sklearn.model_selection.train_test_split` :将数组或矩阵随机分成训练集和测试集；`test_size` 指定测试集的比例；`random_state` 设置随机种子；
- `sklearn.model_selection.StratifiedShuffleSplit` :将数据进行分层采样，其中 `n_splits` 指定了交叉验证的次数，test_size确定了测试集大小，random_state设置种子；

### datasets

该模块用于获得某些数据

- `sklearn.datasets.fetch_mldata` : 从 `mldata.org` 网站上获取数据集；

## OS module

该模块主要封装了各种与操作系统相关的接口。

- 如果只是想要读写文件，查看 `open()`；
- 如果想要操作路径，查看 `os.path` 模块；
- 如果你想要读取所有文件的所有行，查看 `fileinput` 模块；
- 如果想要创建临时文件或目录，查看 `tempfile` 模块；
- 对于高级的文件和目录操作，查看 `shutil` 模块；

### 直属函数

- `os.makedirs`:创建目录，可一次实现多层目录的创建，相当于命令`mkdir -p`

### os.path

该模块实现了一些与路径名相关的有用函数。

- `os.path.abspath`: 返回某个路径的绝对路径
- `os.path.basename`: 返回最后一个 / 之后的内容
- `os.path.isdir`: 判断指定路径是否为目录
- `os.path.join`: 将多个路径连接在一起

## urllib module

urllib包收集了多个与URLs相关的多个模块：

- `urllib.request`: 用于打开和读取URLs
    - `urllib.request.urlretrieve`: 将一个通过URL指定的网络对象下载到本地，可以简单地认为是用于下载的一个函数。第一个参数是 url地址，第二个参数是要保持的路径名。**注意：路径名不要使用 ~**
- `urllib.error`: 包含了 `urllib.request`可能抛出的异常
- `urllib.parse`: 解析URLs
- `urllib.robotparse`: 用于解析robots.txt文件

## tarfile

tarfile模块用于读写tar存档文件，包括使用了 gzip、bz2、lzma进行压缩的文件。使用 zipfile 模块用于读写 .zip 文件，其他的高级函数查看 shutil 模块。

- `tarfile.open`: 读取指定的文件名，返回一个 TarFile 对象
    - `TarFile.extractall`: 将所有的文件取出放入指定目录（默认当前文件夹）
    - `TarFile.extract`: 将指定的文件取出放入指定目录（默认当前文件夹）
    - `TarFile.close`: 关闭 TarFile 实例。

## hashlib

该模块实现了很多不同的安全散列和消息摘要算法。包括了FIPS安全散列函数 SHA1，SHA224，SHA256，SHA384 和 SHA512，以及 RSA的 MD5 算法。

- `hashlib.md5` :创建一个hash对象m，可通过 `m.digest()` 获得摘要，通过 `m.hexdigest` 可获得16机制表示的摘要；
