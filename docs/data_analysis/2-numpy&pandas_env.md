# numpy安装教程
## macOS
```
# 使用python3
pip3 install numpy

# 使用python2
pip install numpy
```
# pandas安装教程
## macOS
```
# 使用python3
pip3 install pandas

# 使用python2
pip install pandas
```
### 安装包结果报错
(machine_learning_py2-pPzatUDd) bash-3.2$ pip install scikit-learn
Collecting scikit-learn
  Could not fetch URL https://pypi.python.org/simple/scikit-learn/: There was a problem confirming the ssl certificate: [SSL: TLSV1_ALERT_PROTOCOL_VERSION] tlsv1 alert protocol version (_ssl.c:590) - skipping
  Could not find a version that satisfies the requirement scikit-learn (from versions: )
No matching distribution found for scikit-learn

### 解决方案
(machine_learning_py2-pPzatUDd) bash-3.2$ curl https://bootstrap.pypa.io/get-pip.py | python
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 1603k  100 1603k    0     0  27488      0  0:00:59  0:00:59 --:--:-- 39256
Collecting pip
  Using cached https://files.pythonhosted.org/packages/0f/74/ecd13431bcc456ed390b44c8a6e917c1820365cbebcb6a8974d1cd045ab4/pip-10.0.1-py2.py3-none-any.whl
Installing collected packages: pip
  Found existing installation: pip 9.0.1
    Uninstalling pip-9.0.1:
      Successfully uninstalled pip-9.0.1
Successfully installed pip-10.0.1

### 安装包的命令
(machine_learning_py2-pPzatUDd) bash-3.2$ pip install --trusted-host pypi.org --trusted-host files.pythonhosted.org scikit-learn