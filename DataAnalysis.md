---
export_on_save:
 html: true
---
# 数据分析过程

## 数据获取

- 从网络上获取数据 :`urllib.request.urlretrieve(url, save_path)`, save_path 应该具体到文件，而不是仅仅一个目录；

- 数据解压缩 :`f = tarfile.open(tarfile_path)` --> `f.list()` --> `f.extract(member, path)` or `f.extractall(path)`

## 数据探索

- 将数据载入内存 :`df = pd.read_csv('csv_filepath')` 返回 DataFrame 对象；
- 查看数据 :`df.head()`、`df.tail()`
- 查看数据的整体信息 :`df.info()`
- 分析数值数据的特性 :`df.describe()` 有数量、平均值、标准差、最小值、1/4分位点、中位数、3/4分位点、最大值；
- 绘制所有数值型数据的柱状图 :`df.hist(bins=10, figsize=(20,15))`，`bins`为柱的数目，`figsize`是一个tuple，表示每个图的大小；
- 绘制单个数据的柱状图 :`df['attrib'].hist(bins, figsize)`
- 分析分类数据的特性 :`df['cat_attrib'].value_counts()`

## 数据划分

- 打乱数据 :`df.iloc[np.random.permutation(len(df))]`--打乱DataFrame中所有数据，`df[attrib_list].iloc[np.random.permutation(len(df))]`--打乱DataFrame中指定的几列数据；`df[attrib][np.random.permutation(len(df))]`
- 随机采样(原始方法)
```python
shuffled_indices = np.random.permutation(len(df))
test_set = df.iloc[shuffled_indices[:len(df)*test_ratio]]
train_set = df.iloc[shuffled_indices[len(df)*test_ratio]:]
```
- 随机采样(scikit-learn)
```python
from sklearn.model_selection import train_test_split
train_set, test_set = train_test_split(housing, test_size=0.2,
    random_state=42)
```
- 分层采样
```python
from sklearn.model_selection import StratifiedShuffleSplit
splitor = StratifiedShuffleSplit(test_size=0.2, n_splits=5, random_state=42)
for train_index, test_index in splitor.split(df, cat_array):
    train_set, test_set = df[train_index], df[test_index]
    # deal with  train and set
```
- 将某个数值型的变量根据一定的公式转换为类型
```python
# DataFrame 直接可以加一列
housing['income_cat'] = np.ceil(housing['median_income']/1.5)
# 对特定的值进行修改 (满足条件的修改)
housing['income_cat'].where(housing['income_cat']<5, 5, inplace=True)
```
- 对于多个数据集，删除相同的属性
```python
for i in (train_set, test_set):
    i.drop("income_cat", axis=1)
```

## 数据可视化

- 以两个变量为横纵坐标值绘制散点图
```python
# s 表示半径，c表示颜色，cmap表示色板
housing.plot(x="longitude", y="latitude", kind="scatter",
            alpha=0.4, s=housing['population']/100,
            label="population", c="median_house_value",
            cmap=plt.get_cmap("jet"), colorbar=True)
```
- 绘制散点图矩阵
```python
from pandas.plotting import scatter_matrix
scatter_matrix(housing[['median_house_value', 'median_income',
 'housing_median_age']],figsize=(10,8))
```

## 寻找相关性
```python
# 所有的相关系数
housing.corr()
# 与某一项的相关系数
housing.corr()['median_house_value'].sort_values(ascending=False)
```

## 属性之间的联合

**两个属性或多个属性可以直接进行运算创建新属性。**

## 数据清理

- 直接删除一整列：`df.drop(attrib_name, axis=1)`
- 删除某几行：`df.dropna(subset=[attr1, attr2])`
- 用某个数补充：`df.fillna(value)` 或者 `series.fillna(value)`
- 用 `scikit.preprocessing.Imputer` 进行操作
```python
imputer = Imputer(strategy="median")
imputer.fit(df)
a = imputer.transform(df)
df = DataFrame(a, columns=df.columns)
```
- 将标签数据进行转换
