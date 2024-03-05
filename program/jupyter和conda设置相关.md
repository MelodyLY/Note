[TOC]

# pyspark配置

*   windows下可设置环境变量：
    PYSPARK\_DRIVER\_PYTHON：jupyter
    PYSPARK\_DRIVER\_PYTHON\_OPTS：notebook

*   linux下建议启动pyspark时动态设置，如：
    ```shell
    PYSPARK_PYTHON=python3 PYSPARK_DRIVER_PYTHON=jupyter PYSPARK_DRIVER_PYTHON_OPTS="notebook --ip=0.0.0.0" \
    pyspark \
    --master yarn \
    --deploy-mode client \
    --driver-memory 8g \
    --executor-memory 12g \
    --num-executors 4 \
    --executor-cores 2
    ```

# conda配置

*   源配置（[清华源](https://mirrors4.tuna.tsinghua.edu.cn/help/anaconda/)）或者（[阿里云](https://developer.aliyun.com/mirror/anaconda/?spm=a2c6h.25603864.0.0.6f1519e4eJsITd)）
    ```shell
    conda config --show-sources #查看当前使用源
    conda config --remove channels 源名称或链接 #删除指定源
    conda config --add channels 源名称或链接 #添加指定源
    #清华源
    conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
    conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
    conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/
    conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
    conda config --set show_channel_urls yes
    # 获取直接修改配置文件
    vim ~/.condarc
    # 修改之后清除索引缓存
    conda clean -i
    #pip命令
    pip install -i https://pypi.tuna.tsinghua.edu.cn/simple 包名
    pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple #永久设置
    ```
    
*   创建虚拟环境
    ```shell
    #创建环境
    conda create -n tf1 python=3.6
    #查看环境列表
    conda info -e
    source activate tf1
    # conda deactivate #退出虚拟环境
    #在jupyter notebook中创建相关环境的内核
    conda install ipykernel
    python -m ipykernel install --user --name tf1 --display-name "tf1"
    #查看内核列表
    jupyter kernelspec list
    
    #删除环境
    conda remove -n oldname --all
    #环境重命名
    conda rename [-h] [-n ENVIRONMENT | -p PATH] [--force] [-d] destination
    #删除内核
    jupyter kernelspec remove kernelname
    ```
    解决重启实例后新建虚拟环境相关依赖被重置问题
    
    ```shell
    # 切换至powerop账户
    su powerop
     
    # 设置proxy 代理让conda可以访问外网
    export http_proxy=http://proxygate2.ctripcorp.com:8080
    export https_proxy=http://proxygate2.ctripcorp.com:8080
     
    # 新建conda 虚拟环境，例如 yolov5
    conda create -p /home/powerop/work/conda/envs/yolov5
     
    # 配置 conda shell
    conda init bash
     
    # 重启shell
    bash
     
    # 激活环境
    conda activate /home/powerop/work/conda/envs/yolov5
     
    # 将python 依赖安装在该虚拟环境, 对应包都会保存在 /home/powerop/work/conda/envs/yolov5
    pip install -r requirements.txt
     
    ## developing and training
     
    # 也可以将conda虚拟环境添加到jupyter notebook中，在虚拟环境激活的情况下执行下列命令：（实例重启之后，需要重新激活虚拟环境并且执行下列命令）：
    pip install ipykernel
    python -m ipykernel install --user --name yolov5 --display-name "conda-yolov5"
     
    # 打开jupyter notebook，可以选择kernel 类型就会多一个选项 conda-yolov5，这样启动的book就是会有对应虚拟环境的所有依赖
     
     
    # 退出虚拟环境
    conda deactivate
     
    # 在需要迁移或者冻结虚拟环境是，可以保存为一个yml文件
    conda env export -n ENV_NAME > environment.yml
     
    # 更换实例或者需要恢复环境则直接从yml文件恢复
    conda env create -f environment.yml
    ```
    
    

# pip配置

*   源配置
    ```shell
    pip install -i [source_url] [package_name]
    ```

# Jupyter 设置

## 安装插件 

```shell
#扩展插件
pip install jupyter_contrib_nbextensions
jupyter contrib nbextension install --user
```

## 启动设置

```shell
# 生成配置文件
jupyter notebook --generate-config
vim ~/.jupyter/jupyter_notebook_config.py
#添加如下内容
c.NotebookApp.ip = '*'
c.NotebookApp.open_browser = False
c.NotebookApp.port = 8799
# md5密码
c.NotebookApp.password = 'argon2:$argon2id$v=19$m=10240,t=10,p=8$x81X1QyBJW+2CcokG8kdzA$JKs9Y3q5Xyi/pb/9I9skD1EQpcwcPePu9hjaemJ+8bk'
```

## 权限问题

```shell
# jupyter内置页面遇到权限问题，可考虑通过如下方式解决
sudo chown -R luoyong /usr/share/jupyter/nbconvert/templates/
```

