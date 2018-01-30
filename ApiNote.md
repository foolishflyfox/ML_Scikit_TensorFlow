---
export_on_save:
 html: true
---

# 函数用法摘记

[TOC]

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
