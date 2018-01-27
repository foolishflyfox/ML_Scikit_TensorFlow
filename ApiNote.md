---
export_on_save:
 html: true
---

# 函数用法摘记

[TOC]

## Build-in Functions

### open

打开文件，返回一个相应的文件对象。如果指定的文件不能被打开，将会抛出一个 `OSError` 错误。

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

- `tarfile.open`: 读取指定的文件名，返回一个 TarFile 对象，设创建的实例名为tarfile
    - `tarfile.extractall`: 将所有的文件取出放入指定目录（默认当前文件夹）
    - `tarfile.extract`: 将指定的文件取出放入指定目录（默认当前文件夹）
    - `tarfile.close`: 关闭 TarFile 实例。
