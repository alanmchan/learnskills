# Virtual Environment

## 通过virtualenv创建

安装

```shell
pip3 install virtualenv
```

创建虚拟环境

```shell
virtualenv myprojectenv # 创建纯净环境
virtualenv --system-site-packages myprojectenv # 创建环境，继承原安装的模块
```

激活虚拟环境

```shell
# linux
source myprojectenv/bin/activate
# windows
cd myproject/Script
activate
```

退出当前虚拟环境

```shell
(myprojectenv) $ deactivate
```

安装与卸载

```sh
pip install numpy
# 批量安装
pip install -r requirements.txt

# 卸载
pip uninstall numpy
```



## 通过conda创建

安装

```sh
dpkg -i Ananconda
```

查看版本

```sh
conda --version
```

创建环境

```shell
conda create --name mytest python=3.7 pandas
```

切换环境

```shell
# linux/macOS
source activate python3
# windows
activate python3
```

退出环境

```sh
source deactivate
```

查看环境中安装的包

```sh
conda list
```

安装

```sh
conda install pandas
conda install --yes --file requirements.txt
```

更新

```sh
conda update
```

卸载

```sh
conda remove pandas
conda remove -n mytest --all
```

查看所有环境

```sh
conda env list
conda list -e
```

导出

```sh
conda env export > environment.yaml
```

导入

```sh
conda env create -f environment.yaml
```

