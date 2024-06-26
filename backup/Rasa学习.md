# 总览
## rasa opensource 和 enterprise的关系
![image](https://github.com/codermartin/gmeek-blog/assets/11265278/d63775e2-fa23-4caf-a82f-b13d8583d9cb)
1. Opensource 提供基础的话术搭建和运行服务
2. Rasa Pro 提供分析、安全和可视化能力
3. Rasa X 提供了易用的低代码用户接口

## 架构图
![image](https://github.com/codermartin/gmeek-blog/assets/11265278/38093081-0598-4cf3-8a14-759735067080)
1. 蓝色是 rasa opensource
2. 紫色是 rasa enterprise
3. 橘色是 第三方中间件

后面主要考虑先学习 rasa opensource，然后根据需要涉及到 rasa enterprise。
参考：https://rasa.com/docs/rasa

# 开始了
## 安装
rasa是python项目。正常玩的话，就用pip安装即可。
```shell
pip3 install rasa
```
生成的目录如下
```shell
-> % tree
.
├── actions
│   ├── actions.py
│   ├── __init__.py
│   └── __pycache__
│       ├── actions.cpython-310.pyc
│       └── __init__.cpython-310.pyc
├── config.yml
├── credentials.yml
├── data
│   ├── nlu.yml
│   ├── rules.yml
│   └── stories.yml
├── domain.yml
├── endpoints.yml
├── models
│   └── 20240626-133941-obliging-boundary.tar.gz
└── tests
    └── test_stories.yml
```
自己需要开发的话，用源码安装。这里用了poetry而不是pip安装。poetry适合在什么场合使用？相比pip有什么异同？后续学习的坑又增加了。
```shell
curl -sSL https://install.python-poetry.org | python3 -
git clone https://github.com/RasaHQ/rasa.git
cd rasa
poetry install
```
python3.10版本，如果涉及cryptography，还需要安装 rustc/cargo。貌似和加密相关。
rasa默认的某些模型，依赖spacy/mitie，如果需要开发也要安装。

## Hello world
rasa的hello world实际上是搭建一个话术(或者说是bot/agent都行)。
```shell
rasa init
```
## Cheat Sheet[#](https://rasa.com/docs/rasa/command-line-interface#cheat-sheet)

| Command                   | Effect                                                                                             |
| :-------------------------- | :--------------------------------------------------------------------------------------------------- |
| `rasa init`               | Creates a new project with example training data, actions, and config files.                       |
| `rasa train`              | Trains a model using your NLU data and stories, saves trained model in`./models`.                  |
| `rasa interactive`        | Starts an interactive learning session to create new training data by chatting to your assistant.  |
| `rasa shell`              | Loads your trained model and lets you talk to your assistant on the command line.                  |
| `rasa run`                | Starts a server with your trained model.                                                           |
| `rasa run actions`        | Starts an action server using the Rasa SDK.                                                        |
| `rasa visualize`          | Generates a visual representation of your stories.                                                 |
| `rasa test`               | Tests a trained Rasa model on any files starting with`test_`.                                      |
| `rasa test e2e`           | Runs end-to-end testing fully integrated with the action server that serves as acceptance testing. |
| `rasa data split nlu`     | Performs a 80/20 split of your NLU training data.                                                  |
| `rasa data split stories` | Do the same as`rasa data split nlu`, but for your stories data.                                    |
| `rasa data convert`       | Converts training data between different formats.                                                  |
| `rasa data migrate`       | Migrates 2.0 domain to 3.0 format.                                                                 |
| `rasa data validate`      | Checks the domain, NLU and conversation data for inconsistencies.                                  |
| `rasa export`             | Exports conversations from a tracker store to an event broker.                                     |
| `rasa evaluate markers`   | Extracts markers from an existing tracker store.                                                   |
| `rasa marker upload`      | Upload marker configurations to Analytics Data Pipeline                                            |
| `rasa license`            | Display licensing information.                                                                     |
| `rasa -h`                 | Shows all available commands.                                                                      |
## 模型训练
```shell
rasa train
```
会在models目录下生成对应模型
## 模型测试
```shell
rasa interactive
```