# POI场景收益干预

实验号：200402\_TRN\_yjpx

开始时间：暂定

1.  ~~TF前置干预模型文件见tf\_model\_poi\_profit\_20200408.pb（上传于模型平台C版）~~

    模型构建代码见tfmodel\_poi\_profit\_v2.ipynb

    \*\*备注（20200422）：\*\*TF前置干预版暂时下线，由于测试时发现较多badcase，收益模型需用到精排的topk信息，因此从TF独立出来，采用TF后置干预版。
2.  TF后置干预模型文件（上传于模型平台D版，“我的位置”也保持一致）见:

    * tf\_model\_poi\_profit\_20200423.pb，上传于模型平台D版
    * tf\_model\_poi\_profit\_20200522.pb，上传于模型平台D版
    * tf\_model\_poi\_profit\_20200601.pb，上传于模型平台D版

    模型构建代码见tfmodel\_poi\_profit\_v3.ipynb

## 上线日志：

实验号：200402\_TRN\_yjpx（poi和我的位置共享该TF）

1. 20200430：实验开启【干预模型为tf\_model\_poi\_profit\_20200423.pb】
2. 20200512：实验切50%流量；
3. 20200522：切换模型【干预模型为tf\_model\_poi\_profit\_20200522.pb】（20200528调整了实验，可剔除）
4. 20200601：切换模型【干预模型为tf\_model\_poi\_profit\_20200601.pb】
5. 20200617：切换模型【干预模型为tf\_model\_poi\_profit\_20200522.pb】
6. 20200703：poi切全量，我的位置关掉。
7. 20200807：poi收益干预关掉。

## 降分规则

### TF前置干预版

**暂时下线**

涉及列名：

```python
['model_score', 'dist','order_rate','hotel_is_vip','isfamilystay'
 ,'fh_price','star', 'hotel_score', 'hotel_novoters','hotel_merit_ctrip'
 ,'user_minprice_1year_zx','user_minstar_1year_zx','user_minprice_1year'
 ,'consuming_ability','profit_score']
```

#### 收益干预

干预公式：

$$
final\_score=(1-\alpha)*model\_score+\alpha*profit\_score
$$

其中model\_score为精排模型分数，profit\_socre为收益分数

**本次实验：$\alpha=0.1$**

#### 距离分层

dist

* <=4000，-0
* <=7000,-0.65
* <=10000,-0.7
* <=15000,-1
* <=20000,-1.5
* <=25000,-2
* <=30000,-2.5
* <=35000,-3.0
* <=40000,-3.5
* <=45000,-4
* <=50000,-4.5
* \>50000,-5

#### 可订率降分

order\_rate

* <=0.1,-0.5
* <=0.3,-0.2

### TF后置干预版

涉及列名：

```python
['final_score','profit_score','rank']
```

根据精排+TF干预后的分数排名后再进行收益干预：

设排名为rank，给定参数$\alpha$,topk\_1,$\theta\_1$,topk\_2,$\theta\_2$,topk\_3,$\theta\_3$,和：

*   rank<=topk\_1:

    $$
    final\_score=(1-\alpha)*final\_score+(\alpha-\theta_1)*profit\_score
    $$
*   rank<=topk\_2:

    $$
    final\_score=(1-\alpha)*final\_score+(\alpha-\theta_2)*profit\_score
    $$
*   rank<=topk\_3:

    $$
    final\_score=(1-\alpha)*final\_score+(\alpha-\theta_3)*profit\_score
    $$
*   rank>topk\_3:

    $$
    final\_score=(1-\alpha)*final\_score
    $$
*   第一次实验（20200423，模型文件见tf\_model\_poi\_profit\_20200423.pb）:

    $\alpha=0.2$

    topk\_1=5，$\theta\_1=0$

    topk\_2=10，$\theta\_2=0.05$

    topk\_3=15，$\theta\_3=0.1$
*   第二次实验（20200522，模型文件见tf\_model\_poi\_profit\_20200522.pb）

    $\alpha=0.15$

    topk\_1=20，$\theta\_1=0$
*   第三次实验（20200601，模型文件见tf\_model\_poi\_profit\_20200601.pb）

    $\alpha=0.2$

    topk\_1=20，$\theta\_1=0$

## 缺失值填充

0
