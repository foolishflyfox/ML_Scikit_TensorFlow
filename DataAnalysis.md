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
- 所有的相关系数 :`df.corr()`
- 与某一项的相关系数 : `housing.corr()[ 'median_house_value' ].sort_values( ascending=False )`

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
  - 将类别类型数据转化为整数型数据：`LabelEncoder`
  - 将整数型数据转化为向量数据：`OneHotEncoder`
  - 将类别类型数据转化为整数型数据：`LabelBinarizer`

<h2 id="20180201131731">如何自己编写转换器</h2>

- 设置成继承自`TransformMixin`(提供 `fit` 和 `transform` 相结合的 `fit_transform` 函数) 和 `BaseEstimator`(提供`get_params` 和 `set_params` 这对超参数相关的函数)；这两个类都在模块`sklearn.base`中；
- 提供`fit`方法，参数有数据集，通常是 `fit(self, X, Y=None)`，写入训练的过程，将学到的参数写在一个以变量中；
- 提供`transform` 方法，返回一个`np.narray` 类型数据；

## 特征缩放

- `sklearn.preprocessing.MinMaxScaler` :用于进行归一化，可通过超参数 `feature_range` 设置最后所得到的范围；
- `sklearn.preprocessing.StandardScaler` :用于数据的标准化；

## 组装流水线

- `pipeline = sklearn.pipeline.Pipeline([('t1_name',transformer1), ('t2_name',transformer2), ...])`,其中构造函数的传入参数是一个`list`，每一个项都是一个`tuple`，其中第一个为名称，可以任意，第二个为对应的转换器实例；使用方法只需要 `pipeline.fit_transform(df)` 即可；**注意：LabelEncoder和LabelBinarizer并不适用于Pipeline中，OneHotEncoder适合，解决方法，一种是将类别数据单独预处理，另一种是按[如何自己编写转换器](#20180201131731)的方法对这两个类进行封装**

- `full_pipeline = sklearn.pipeline.FeatureUnion([('pipe_name1', pipe1), ('pipe_name2', pipe2), ...])` :其中构造函数中的传入参数使一个 `list`，每一个项都是一个`tuple`，其中第一个为名称，可以任意，第二个为对应的流水线实例；使用时只需要 `full_pipeline.fit_transform(df)` 即可，返回的值是将多个输出合并在一些numpy 二维数组；

## scikit-learn训练模型

scikit-learn 中模型的调用过程通常为：
1. 创建模型实例，在创建过程中设置超参数：`model = Model(hyper_parameters)`
2. 训练模型：`model.fit(X, y)`
3. 预测
    - 直接获得结果：`y_preds = model.predict(X)`
    - 对于分类问题，获得评分，自定义阈值进行分割：`y_scores = model.decision_function(X)`

### 线性模型

线性模型在 `sklearn.linear_model` 模块中。

#### 线性回归模型

线性回归模型为：`sklearn.linear_model.LinearRegression`。

使用方法：
- 创建模型的实例 ：`lin_reg = LinearRegression()`
- 训练实例：`lin_reg.fit(features, labels)`
- 预测：`lin_reg.predict(new_features)`，返回的是一个numpy.ndarray 类型数据；

#### 线性回归模型的评价

对scikit-learn中的模型评估的函数在模块 `sklearn.metrics` 中。

MSE 误差测量使用 `sklearn.metrics.mean_squared_error`，使用方法是：`mean_squared_error(labels, predictions)`

precision 查准率测量使用：`sklearn.metrics.precision_score`，使用方法是：`precision_score(labels, predictions)`

recall 查全率测量使用：`sklearn.metrics.recall_score`，使用方法是：`recall_score(labels, predictons)`

$F_1$值测量使用：`sklearn.metrics.f1_score`，使用方法是：`f1_score(labels, predictons)`

### 树模型

树模型在 `sklearn.tree` 模块中。

- 决策树回归：`sklearn.tree.DecisionTreeRegressor`

### 集成方法

集成方法在 `sklearn.ensemble` 模块中

- 随机森林回归 ：`sklearn.ensemble.RandomForestRegressor`

#### 决策树回归模型

决策树回归模型为：`sklearn.tree.DecisionTreeRegressor`。

使用方法：
- 创建模型的实例 ：`tree_reg = DecisionTreeRegressor()`
- 训练实例 ：`tree_reg.fit(features, labels)`
- 预测 ：`tree_reg.predict(new_features)`

## 评价一个模型的好坏

### 对数据集进行K折分层采样

`sklearn.model_selection.StratifiedKFold`用于将数据进行分层划分，使用方法如下：

```python
skfold = StratifiedKFold(n_splits=3, shuffle=True, random_state=42)
for train_index, test_index in skfold.split(X, y):
    X_train_folds = X[train_index]
    y_train_folds = y[train_index]
    X_test_fold = X[test_index]
    y_test_fold = y[test_index]
    model.fit(X_train_folds, y_train_folds)
    y_test_pred = model.predict(X_test_fold)
    meansure_performance(y_test_pred, y_test_fold)
```



### 使用交叉验证的方式

主要用到的函数是：`sklearn.model_selection.cross_val_score`

使用方法也很简单：`scores = cross_val_score(model_instance, X, y, scoring='mean_squared_error', cv=10)`，如此调用返回的是10-折交叉验证得到的负的MSE数组。


`sklearn.model_selection.cross_val_predict` 函数用于返回交叉验证的结果集。

### 网格搜索最佳的超参数组合

使用 `sklearn.model_selection` 中的 `GridSearchCV` 可以进行自动化的最佳超参数选择。

使用方法：
- 创建一个 `GridSearchCV` 实例：`grid_search = GridSearchCV(model, param_grid, cv=5, scoring='neg_mean_squared_error')`
- 执行训练：`grid_search.fit(features, labels)`

## 模型的保存

常用的有Python自带的 `pickle` 模块与 `sklearn.externals.joblib`。

### 用 pickle 进行保存

```python
import pickle
pickle.dump(obj, open(filename_pkl, "wb"))

# late ...
pickle.load(open(filename_pkl, "rb"))
```

### 用 externals

用这种方法进行存储效率要比用 `pickle` 的要高；

```python
from sklearn.externals import joblib
joblib.dump(model, filename_pkl)

# late
model_loaded = joblib.load(filename_pkl)
```

### 分类模型

计算分类模型的 ROC AUC：`sklearn.metrics.roc_auc(labels, scores)`
