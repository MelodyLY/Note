[TOC]

# pandas 优化

## 压缩数据

```python
import numpy as np
import pandas as pd

def reduce_mem_usage(df,verbose=True):
    numeric = ['int16','int32','int64','float16','float32','float64']
    start_men = df.memory_usage().sum() / (1024**2)
    for col in df.columns:
        col_type=df[col].dtypes
        if col_type in numeric:
            c_min = df[col].min()
            c_max = df[col].max()
            if str(col_type)[:3]=='int':
                if c_min > np.iinfo(np.int8).min and c_max<np.iinfo(np.int8).max:
                    df[col] = df[col].astype(np.int8)
                elif c_min>np.iinfo(np.int16).min and c_max<np.iinfo(np.int16).max:
                    df[col] = df[col].astype(np.int16)
                elif c_min>np.iinfo(np.int32).min and c_max<np.iinfo(np.int32).max:
                    df[col] = df[col].astype(np.int32)
                elif c_min>np.iinfo(np.int64).min and c_max<np.iinfo(np.int64).max:
                    df[col] = df[col].astype(np.int64)   
            else:
                if c_min > np.finfo(np.float16).min and c_max<np.finfo(np.float16).max:
                    df[col] = df[col].astype(np.float16)
                elif c_min>np.finfo(np.float32).min and c_max<np.finfo(np.float32).max:
                    df[col] = df[col].astype(np.float32)
                elif c_min>np.finfo(np.float64).min and c_max<np.finfo(np.float64).max:
                    df[col] = df[col].astype(np.float64)
    end_mem = df.memory_usage().sum() / 1024**2
    if verbose:
        print('start_men：{:5.2f} Mb,end_men：{:5.2f} Mb'.format(start_men,end_mem))
```

## 内存检测

```python
#dataframe占用内存检测
df_sample_train.info(memory_usage='deep')
```

[节省内存相关链接](https://www.jb51.net/article/160670.htm)

# python代码优化

## sigmoid溢出问题

```python
#普通写法
def sigmoid(z):
    return 1/(1+np.exp(-z))
#防止溢出写法
def sigmoid(z):
    return .5 * (1 + np.tanh(.5 * z))
```

# 日志打印设置

```python
import coloredlogs
import logging
import sys

logger = logging.getLogger()

# 日志格式
LOG_FORMAT = "%(asctime)s - %(filename)s[line:%(lineno)d] - %(levelname)s: %(message)s"

# 配色方案
DEFAULT_FIELD_STYLES = dict(
    asctime=dict(color='green'),
    hostname=dict(color='magenta'),
    levelname=dict(color='blue', bold=True),
    name=dict(color='blue'),
    programname=dict(color='cyan'),
    username=dict(color='yellow'),
)
DEFAULT_LEVEL_STYLES = dict(
    spam=dict(color='green', faint=True),
    debug=dict(color='green'),
    verbose=dict(color='blue'),
    info=dict(),
    notice=dict(color='magenta'),
    warning=dict(color='yellow'),
    success=dict(color='green', bold=True),
    error=dict(color='red'),
    critical=dict(color='red', bold=True),
  
coloredlogs.install(level=logging.DEBUG, logger=logger, isatty=True,
                    fmt="%(asctime)s - %(filename)s[line:%(lineno)d] - %(levelname)s: %(message)s",
                    stream=sys.stdout,
                    field_styles=DEFAULT_FIELD_STYLES,
                    datefmt='%Y-%m-%d %H:%M:%S')

logger.debug("this is a debugging message")
logger.info("this is an informational message")
logger.warning("this is a warning message")
logger.error("this is an error message")
logger.critical("this is a critical message")
```

