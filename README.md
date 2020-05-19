![DICBetter Logo](logo.jpg)

## 简介

该仓库的脚本是我个人使用的DICBetter fork，最初由What.CD的zacharydenton创建并由Mechazawa以及redacted的iw00t更新。


---
DICBetter 作为一个全自动化的脚本能够帮助你搜索你所有正在做种的FLAC格式的音乐，它能在没有MP3格式转码被上传的时候自动进行有损转码并上传种子至DICMusic

## 依赖环境

* Linux or macOS
* Python 2.7 or newer (python3 in not supported)
* `mktorrent`
* `mechanize`, `mutagen`, `requests` and `Unidecode` Python 模块
* `lame`, `sox` and `flac`


## 安装指南

#### 1. 安装python

Python 官方网站在 [这里](https://www.python.org/downloads/)



#### 2. 安装 `mktorrent`

必须使用源码安装`mktorrent` 而不能从程序管理器中安装。
对于Linux系统，可以在一个临时目录采用以下命令安装

~~~~
$> git clone git@github.com:Rudde/mktorrent.git
$> cd mktorrent
$> make && sudo make install
~~~~

如果你正在使用Seedbox 并且缺少安装软件包的权限。建议你联系你的Seedbox 提供商并让他帮助你安装这些软件

#### 3. 安装 `mechanize`, `mutagen`, `requests` , `Unidecode` Python 模块

~~~~
pip install -r requirements.txt
~~~~
如果你无法安装的话可能是你的用户权限不够，尝试使用sudo安装
~~~~
sudo -H pip install -r requirements.txt
~~~~


#### 4. 安装 `lame`, `sox` , `flac`

根据你系统的程序管理器不同，你可以尝试以下命令进行安装:
  * Debian: `sudo apt-get install lame sox flac`
  * Ubuntu: `sudo apt install lame sox flac`
  * macOS: `brew install lame sox flac`



## 配置
在DICBetter目录通过以下命令运行DICBetter

    $> ./dicmusicbetter

当你第一次运行时，你应该会收到类似如下的消息

    ~Please edit the configuration file: /home/<user>/.dicmusicbetter/config

用你的文本编辑器打开上述文件，并根据你的需要修改它
* `username`: 你的dicmusic用户名
* `password`: 你的dicmusic密码
* `session_cookie`: 你在dicmusic的cookie里面的session字段
* `data_dir`: 你需要进行转码的音乐的目录
* `output_dir`: 转码后的音乐存放目录，如果你没有填写它，默认值为 `data_dir`
* `torrent_dir`: 生成的种子文件的存放目录
* `formats`: 用英文逗号 (`, `) 来分割你需要转码的目标音乐格式. 它默认包括 `flac, v0, 320`三种格式。 `flac` 被包括在内是因为dicmusic允许将24-bit FLAC转换为16-bit FLAC. 需要注意的是 `v2` 没有包含在其中因为根据dicmusic的规则 - v0 种子在任意情况下都能够trump v2 种子.
* `media`: 用英文逗号 (`, `) 来分割你想要转换的音乐的媒介. 默认值是所有的无损格式媒介。如果你只想转换特定的媒介格式，比如CD和vinyl，你可以设置为`cd, vinyl`.
* `24bit_behaviour`: 该值用来定义当程序认为当前FLAC属于24-bit却被错误分类为16-bit的时候的行为. 如果他被设定为 `2`, 每一个24bit FLAC都会被重新分类并且不会有任何提示. 如果被设置为 `1`, 则将会出现提示. 默认值是 `0` ：程序会忽略掉这种情况.

## 用法
~~~~
usage: dicmusicbetter [-h] [-s] [-j THREADS] [--config CONFIG] [--cache CACHE]
                      [-U] [-E] [--version]
                      [release_urls [release_urls ...]]

positional arguments:
  release_urls          指定发布的音乐的url (default: None)

optional arguments:
  -h, --help            显示帮助信息
  -s, --single          每个种子只添加一个版本的格式 (用来刷"独特分组"很有用) (default: False)
  -j THREADS, --threads THREADS
                        转码时使用的线程数 (default: 3)
  --config CONFIG       配置文件所在目录 (default:
                        /home/<user>/.dicmusicbetter/config)
  --cache CACHE         缓存文件所在目录 (default:
                        /home/<user>/.dicmusicbetter/cache)
  -U, --no-upload       不要自动上传种子 (如果你想手动上传的话) (default: False)
  -E, --no-24bit-edit   不尝试编辑被错误分类为16-bit的24-bit音乐 (default: False)
  --version             查看程序的版本信息
~~~~

### 使用例子

如果你想要转码所有`data_dir`目录的FLAC文件 (这会耗费一些时间):

    $> ./dicmusicbetter

如果你只想转换某一个特定的FLAC音乐 (请确保该文件位于 `data_dir`所对应的目录下):

    $> ./dicmusicbetter http://dicmusic.club/torrents.php?id=1000\&torrentid=1000000

请注意如果你明确只转换某一特定音乐，dicmusicbetter会忽视掉你配置文件中的media选项并开始转换你输入的音乐(只要它是FLAC格式的)。

DICBetter 会缓存你已经成功转换的文件, 并且会转换跳过这些文件。这会让他以后的运行会比第一次运行更快, 尤其对于一些很大的音乐目录来说. 有时候缓存会出现一些错误，比如当你上传时程序崩溃了，而缓存却认为你已经成功上传了该文件并且跳过了此次上传，解决方法是如上文所言手动指定该文件。如果多次发生这种情况的话，你可以尝试删除缓存:

    $> ./dicmusicbetter ~/.dicmusicbetter/cache

请当心，这个操作会让程序重新检查所有的音乐文件，就像他第一次运行时那样。

## Bugs 报告和新功能请求

如果在使用中你发现了bug，或者想给程序新添加一些功能，请在[issues](https://github.com/scatking/DICBetter/issues)界面上报, *不过请确保你已经在Issues界面查找了没有相似错误存在*.
