# Mac python3 matplotlib中文乱码解决

## 1.下载字体
下载 arial unicode ms 字体

## 2.终端运行python后执行如下命令获取matplotlibrc配置文件路径
```
import matplotlib
matplotlib.matplotlib_fname()
/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/matplotlib/mpl-data/matplotlibrc
```

## 3.复制字体到matplotlib字体目录下
```
cp arial\ unicode\ ms.ttf /Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/matplotlib/mpl-data/fonts/ttf
```

## 4.1成功解决方法：
### 修改Matplotlib默认设置，加入中文字体支持
```
import matplotlib.pyplot as plt

plt.rcParams['font.family'] = ['Arial Unicode MS', 'sans-serif'] #  全局设置支持中文字体，默认 sans-serif
```

### 删除matplotlib字体缓存：
```
rm -rf ~/.cache/matplotlib/fontList.json
```
重启 Restart notebook Kernel，重新执行Matplotlib代码，会重新生成缓存

## 4.2失败方法：
### 记下地址，终端切换到该地址后修改matplotlibrc文件，添加如下内容：
```
vim matplotlibrc
#font.family         : sans-serif
#font.sans-serif     : Arial Unicode MS, DejaVu Sans, Bitstream Vera Sans, Lucida Grande, Verdana, Geneva, Lucid, Arial, Helvetica, Avant Garde, sans-serif
```

### 清除matplotlib字体缓存
```
rm -rf ~/.matplotlib/*.cache
rm fontList.json
```
### 字体解决
    通过命令cat ~/.matplotlib/fontList.cache|grep Vera找到fontList.cache所读取的字体的路径
/Users/liupan/.virtualenvs/matplotlib_py3-kxmrx5Ts/lib/python3.6/site-packages/matplotlib/mpl-data/fonts/ttf/STIXNonUniIta.ttfq
#### 参考网址
https://www.jianshu.com/p/ac00d44214d8

                 Vera.ttf		msyh.ttf		yehei.ttf
arial unicode ms.ttf	simhei.ttf                 