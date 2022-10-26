\[toc]

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

*   源配置
    ```shell
    conda config --show-sources #查看当前使用源
    conda config --remove channels 源名称或链接 #删除指定源
    conda config --add channels 源名称或链接 #添加指定源
    #清华源
    conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
    conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
    conda config --set show_channel_urls yes

    ```

*   创建虚拟环境
    ```shell
    #创建环境
    conda create -n tf1 python=3.6
    source activate tf1
    # conda deactivate #退出虚拟环境
    #在jupyter notebook中创建相关环境的内核
    conda install ipykernel
    python -m ipykernel install --user --name tf1 --display-name "tf1"
    ```

# pip配置

*   源配置
    ```shell
    pip install -i [source_url] [package_name]

    ```

