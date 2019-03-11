# SmoothNLP
[![GitHub release](https://img.shields.io/badge/Version-0.1-green.svg)](https://github.com/zhangruinan/SmoothNLP/releases)
****	
|Author|VC|
|---|---
|E-mail|ruinan.zhang@icloud.com|
****

<!-- TOC -->

<!-- - [SmoothNLP](#smoothnlp)
    - [Installation](#installation)
        - [Python](#python)
        - [Java Server](#java-server)
    - [情感模型 Sentiment Analysis](#情感模型-sentiment-analysis)
        - [情感标注数据生成](#情感标注数据生成)
            - [半监督打标数据生成](#半监督打标数据生成)
            - [BinarizedTree 训练数据数据处理](#binarizedtree-训练数据数据处理)
        - [RNTN Model 模型训练](#rntn-model-模型训练)
    - [NER Normalization 数字与金额的识别](#ner-normalization-数字与金额的识别)
        - [Money 金额识别](#money-金额识别)
        - [Numerical 数字识别](#numerical-数字识别)
    - [NER 命名实体识别 - 训练过程与效果评估](https://github.com/zhangruinan/SmoothNLP/tree/master/ner) -->

<!-- /TOC -->

<!-- ----------- -->

## Installation
### Python 
```shell
pip3 install git+https://github.com/zhangruinan/SmoothNLP.git
```
Please notice, the python package is only a convenient wrapper on CoreNLP's server in jre. 
Therefore, for many functionalities, you may start the Java Server first. 

### Java Server
You may download the latest jar file from [here](https://github.com/zhangruinan/SmoothNLP/releases)
. After download the archive file, unzip it and run it in jvm.

Jar文件[下载地址](https://github.com/zhangruinan/SmoothNLP/releases),下载解压后可运行Server
```shell
unzip corenlp-chinese-smoothnlp-*.zip
java -jar corenlp-chinese-smoothnlp-*-with-dependencies.jar 
```
Please notice, the corenlp server runs on port 9000 on default, you may change settings based on 
[official document](https://stanfordnlp.github.io/CoreNLP/corenlp-server.html) by parsing in additional arguments.
For instance:

如需添加额外参数，可参考[官方文档](https://stanfordnlp.github.io/CoreNLP/corenlp-server.html),下面是一个额外参数调用的例子：
```shell
java -cp corenlp-chinese-smoothnlp-0.1-with-dependencies.jar com.smoothnlp.nlp.RESTServer -port 9001
``` 
------------


## NLP Pipeline相关功能
### NER Normalization 数字与金额的识别
#### Money 金额识别
```python
import smoothnlp
print(smoothnlp.money_recognize("价值两千五百元"))
print(smoothnlp.money_recognize("我新买的iphone原价要八百刀"))
```

```shell
[{'normalized_entity': '元2500', 'type': 'MONEY', 'start': 2, 'end': 7}]
[{'normalized_entity': '$800', 'type': 'MONEY', 'start': 13, 'end': 16}]
```
#### Numerical 数字识别
```python
smoothnlp.number_recognize("后宫佳丽有三千")
```

```shell
[{'normalized_entity': '3000', 'type': 'NUMBER', 'start': 5, 'end': 7}]
```

### CoreNLP相关
#### 自训练Annotator-模型配置
在完成自训练模型后,须将对应的模型地址参数指向模型文件地址. 关于CoreNLP官方支持的Annotator相关Argument具体文档, 
可以看[这里](https://stanfordnlp.github.io/CoreNLP/annotators.html). 

SmoothNLP中实现了3种模型加载方式. 下面将用情感模型(sentiment.model)举例

* 通过编写*.properties* 文件并在启动Server的时候加载 (推荐)
```angular2
java -jar corenlp-chinese-smoothnlp-0.1-jar-with-dependencies.jar -props nlp.properties
```

* 启动Server时通过传入额外参数实现 (不推荐)
```angular2
java -jar corenlp-chinese-smoothnlp-0.1-jar-with-dependencies.jar sentiment.model edu/stanford/nlp/models/sentiment/sentiment_model_zh.ser.gz
```

* 在调用Server时传入Annotator相关参数 (推荐,由于CoreNLP相关模型支持Latent加载)
这里举例一个wget在shell里的调用
```
wget --post-data '今天天气不错' 'localhost:9000/?properties={"annotators": "tokenize,sentiment", 
"sentiment.model":"edu/stanford/nlp/models/sentiment/sentiment_model_zh.gz","outputFormat": "json","pipelineLanguage":"zh"}' -O -
```


### TODO
* [ ] 重构HomePage Document, 修改为Java中API, 和其他基础信息
* [ ] python 项目中添加Jpype支持
* [ ] Segment/切词模块的开发