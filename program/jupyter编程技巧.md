[TOC]

# 消除警告

```python
import warnings
warnings.simplefilter(action='ignore', category=FutureWarning)
warnings.filterwarnings('ignore')
```

# 重新加载包

```python
%load_ext autoreload
%autoreload 2
```

# 添加路径

```python
import sys;
sys.path.append("../../../zx_hotel/")
```

# 垃圾回收

```python
import gc
#垃圾回收
gc.collect()
```

