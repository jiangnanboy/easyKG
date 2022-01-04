# easyKG

## 说明
本项目主要实现知识图谱相关技术
## 前言

项目设计主要有以下几大功能：

- 知识表示学习， Knowledge Representation Learning
- 实体识别与链接， Entity Recognition and Linking
- 实体关系抽取， Entity Relation Extraction
- 事件检测与抽取， Event Detection and Extraction
- 知识存储与查询， Knowledge Storage and Query
- 知识推理， Knowledge Reasoning

因此主要有六个功能模块：krl（知识表示学习）、erl（实体识别与链接）、ere（实体关系抽取）、ede（事件检测与抽取）、ksq（知识存储与查询）以及kr（知识推理）。

## 当前已实现的功能

### krl（知识表示学习）

### erl（实体识别与链接）

- 命名实体识别， ner

### ere（实体关系抽取）

- 关系抽取， re

### ede（事件检测与抽取）

- 语义角色标注， srl

### ksq（知识存储与查询）

### kr（知识推理）

## 模型
- ner: Albert-CRF，https://github.com/jiangnanboy/albert_ner
- srl: Albert-CRF，https://github.com/jiangnanboy/albert_srl
-  re: Albert-FC，https://github.com/jiangnanboy/albert_re

## 训练数据说明
 #### ner 实体识别

BIO形式标注，data/ner

训练数据示例如下：

```
海 O
钓 O
比 O
赛 O
地 O
点 O
在 O
厦 B-LOC
门 I-LOC
与 O
金 B-LOC
门 I-LOC
之 O
间 O
的 O
海 O
域 O
。 O
```

#### srl 语义角色标注

BIOES形式标注，data/srl

训练数据示例如下，其中各列为`字`、`是否语义谓词`、`角色`，每句仅有一个谓语动词为语义谓词，即每句中第二列取值为1的是谓词，其余都为0.

```
她 0 O
介 0 O
绍 0 O
说 0 O
， 0 O
全 0 B-ARG0
行 0 I-ARG0
业 0 E-ARG0
全 0 B-ARGM-TMP
年 0 E-ARGM-TMP
生 1 B-REL
产 1 E-REL
化 0 B-ARG1
肥 0 I-ARG1
二 0 I-ARG1
千 0 I-ARG1
七 0 I-ARG1
百 0 I-ARG1
二 0 I-ARG1
十 0 I-ARG1
万 0 I-ARG1
吨 0 E-ARG1
```

#### re 关系抽取

data/re

训练数据示例如下，其中各列为`头实体`、`尾实体`、`关系`、`句子`。

```
李敖	王尚勤	夫妻	李敖后来也认为，“任何当过王尚勤女朋友的人，未来的婚姻都是不幸的！
傅家俊	丁俊晖	好友	改写23年历史2010年10月29日，傅家俊1-5输给丁俊晖，这是联盟杯历史上首次出现了中国德比，丁俊晖傅家俊携手改写了
梁左	梁天	兄弟姐妹	-简介梁左与丈夫英达梁欢，女，梁欢和梁天的妹妹，英达的现任妻子。
```
 ## 使用
 训练、测试及预测的使用见examples/
### ner

#### 训练及预测 （examples/test_ner.py）
```
ner = NER(args)
if train_bool(args.train):
    ner.train()
    # dev_score: 0.9886729163992931    acc_loss: 6.1900248776582885    best_val_loss: 12.737978113349527  
else:
    ner.load()
    # ner.test(args.test_path)
    pprint(ner.predict('据新华社报道，安徽省六安市被评上十大易居城市！'))
    #[{'start': 7, 'stop': 10, 'type': 'LOC', 'word': '安徽省'},{'start': 10, 'stop': 13, 'type': 'LOC', 'word': '六安市'},{'start': 1, 'stop': 4, 'type': 'ORG', 'word': '新华社'}]
    
    print(ner.predict('相比之下，青岛海牛队和广州松日队的雨中之战虽然也是0∶0，但乏善可陈。'))
    #[{'start': 5, 'stop': 10, 'word': '青岛海牛队', 'type': 'ORG'}, {'start': 11, 'stop': 16, 'word': '广州松日队', 'type': 'ORG'}]
    
```

### re

#### 训练及测试 (examples/test_re.py)
```
    re = RE(args)
    if train_bool(args.train):
        re.train()
        '''
        epoch: 58, acc_loss: 0.0080563764
        dev_score: 0.67
        val_loss: 0.06119863061138485, best_val_loss: 0.055413116525741
        '''
    else:
        re.load()
        # ner.test(args.test_path)
        # 钱钟书	辛笛	同门	与辛笛京沪唱和聽钱钟书与钱钟书是清华校友，钱钟书高辛笛两班。
        print(re.predict(text=('钱钟书', '辛笛', '与辛笛京沪唱和聽钱钟书与钱钟书是清华校友，钱钟书高辛笛两班。')))
        # (0.9997676014900208, '同门')

        # 平儿	贾琏	夫妻	此外，如贾琏偷娶尤二姐事，平儿虽然告诉了凤姐，但她对凤姐虐待尤二姐、害死尤二姐一事并不赞成
        print(re.predict(text=('平儿', '贾琏', '此外，如贾琏偷娶尤二姐事，平儿虽然告诉了凤姐，但她对凤姐虐待尤二姐、害死尤二姐一事并不赞成')))
        # (0.9999169111251831, '夫妻')
```

### srl

#### 训练及测试 (examples/test_srl.py)
```
    srl = SRL(args)
    if train_bool(args.train):
        srl.train()
        '''
        epoch: 45, acc_loss: 46.13663248741068
        dev_score: 0.931195765914934
        val_loss: 50.842400789260864, best_val_loss: 50.84240078926086
        '''
    else:
        srl.load()
        # ner.test(args.test_path)
        text = '代表朝方对中国党政领导人和人民哀悼金日成主席逝世表示深切谢意'
        predicates_indicator = [0, 0, 0, 0, 0, 0, 0, 0, 0,0,0,0,0,0,0,1,1, 0, 0, 0, 0, 0, 0, 0,0,0,0,0,0,0]
        assert len(text) == len(predicates_indicator)
        pprint(srl.predict(text, predicates_indicator))    
        # tag_predict: ['O', 'O', 'O', 'O', 'O', 'B-ARG0', 'I-ARG0', 'I-ARG0', 'I-ARG0', 'I-ARG0', 'I-ARG0', 'I-ARG0', 'I-ARG0', 'I-ARG0', 'E-ARG0', 'B-REL', 'E-REL', 'B-ARG1', 'I-ARG1', 'I-ARG1', 'I-ARG1', 'I-ARG1', 'I-ARG1', 'E-ARG1', 'O', 'O', 'O', 'O', 'O', 'O']
        # {'ARG0': '中国党政领导人和人民', 'ARG1': '金日成主席逝世', 'REL': '哀悼'}
```

## 项目组织结构

### 项目架构
- data
    - ner
    - re
    - srl
- easykg
    - ede
        - srl, 语义角色标注
    - ere
        - re， 关系抽取
    - erl
        - ner， 命名实体识别
- utils

### 功能

- [x] 关系抽取代码
- [x] 语义角色标注代码
- [x] 命名实体识别代码

## 参考
- [transformers](https://github.com/huggingface/transformers)
- [Matching the Blanks: Distributional Similarity for Relation Learning](https://arxiv.org/pdf/1906.03158.pdf)
- [Simple BERT Models for Relation Extraction and Semantic Role Labeling](https://arxiv.org/pdf/1904.05255.pdf)

## 打赏

如果该项目对您有所帮助，欢迎打赏~

![image](https://raw.githubusercontent.com/jiangnanboy/easyKG/master/image/20220104203727.png)
