# hello-world
my first project

这不是主分支


问答机器人目前主要是三中类型
1、基于规则的模型(Rule-based model) ----特定问题对应特定答案 K-V结构，需要非常强大的知识库，定义这么一套规则工程量很大
2、基于检索的模型(Retrieval-based model) ----在第一种的基础上加了一些类似于检索的形式，在K-V结构上，通过问题与问题之间的匹配度去给到相应的答案，通过把问题归类来让用户有更好的人机交互体验
3、生成式模型(Generative model)----通过一套生产序列的算法框架seq2seq（说实话看视频的时候不太明白），目前市面上应用的这种还很不稳定，准确率不太高

问答机器人方案（Web项目）
目前网站的自动问答机器人一般都是第一种和第二种方式的结合，有专业领域的知识库和普通规则的知识库。
1.知识库很重要，这两种模式的结合是离不开一个 K-V 数据结构的知识库。首先会进行一个分词，然后标注词性，形成语料库
2.首先就是完全匹配，如果没有答案。
3.然后就把用户的问题把进行一个分词，通过分词得到词性标志，然后通过算出TF-IDF（提取关键字，算出这个关键字出现的频率）结合编辑距离（用户的问题和知识库中问题的相似度）去分析问题的语义，然后将分析的问题意思拿到知识库去最大程度的匹配。
4.最后机器人工作流程就是通过控制聊天窗口去通过HTTP发送用户问题的请求，后台这块就获取这个问题，执行上面的匹配逻辑，最后返回应答，然后将结果组装到聊天窗口上。

第一种模式的代码实现
视频中的代码
from chatterbot import ChatBot
import jieba
bot=ChatBot(
    'Default Response Example Bot',
    storage_adapter='chatterbot.storage.JsonFileStorageAdapter',
    logic_adapters=[
        {
            'import_path':'chatterbot.logic.BestMatch'
        },
        {
            'import_path':'chatterbot.logic.LowConfidenceAdapter',
            'threshold':0.65,
            'default_response':'我不知道你在说撒子'
        }
    ],
    trainer='chatterbot.trainers.ListTrainer'

)

bot.train([
    '你是谁?',
    '我是你的机器人小弟',
    'have you read the documentation?',
    'no i have not',
    'this should help get you started: http://chatterbot.rtfd.org/en/latest/quickstart.html'
])
#首先输入
question=input('你好，有什么可以帮助你的嘛？:')
#然后分词
question_cut = "".join(jieba.cut(question))
response=bot.get_response(question_cut)
print(response)


简易版机器人  学习

#/usr/bin/env python3
#coding:utf-8

dict = {
    "你好" : "你好",
    "很高兴见到您" : "我也很能高兴见到你",
    "你喜欢吃什么水果 " : "我喜欢橘子",
    "你今年多大了" : "27 岁了",
    "你很漂亮" : "谢谢"
}

flag = ('c')
work = True

print('你好，我是python机器人')
print('你有时间跟我聊聊吗')
while flag == 'c' or 't':
    flag = input("你可以选择是否跟我聊天(c),还是决定练习下我的对话能力(t),或者让我推下(l)?(c/t/l)")
    if flag == "t":
        question = input("请输入你想问的:")
        answer = input("请输入问题答案:")
        dict[str(question)] = str(answer)
        print("学习成功")
        print("现在我已经学会了%d个问题"%len(dict))
        continue
    elif flag == 'c':
        if len(dict) == 0:
            print("现在我还不会回答任何问题，请先让我学习:")
            continue
        chat_word = input("谢谢你跟我聊天，你想对我说点什么?:")

        for key in sorted(dict.keys()):
            if str(chat_word) == key:
                work = True
                print(dict[key])
                break
            else:
                work = False
        if work == False:
            print("Sorry，这个问题我回答不上来")
            work = True
    elif flag == 'l':
        print("好的，那我们下次再聊")
        break
    else:
        print("请输入提示指令")
        continue

网络数据爬取

import urllib.request
from bs4 import BeautifulSoup
response = urllib.request.urlopen('http://corpus.zhonghuayuwen.org')
html = response.read()
soup = BeautifulSoup(html,"html5lib")
text = soup.get_text(strip=True)
tokens = [t for t in text.split()]
print (tokens)

自己版

import Levenshtein
import numpy
import jieba
dict={
    '你好' : '你好',
    '很高兴见到你' : '我也很高兴见到你',
    '你最喜欢什么' : '网球',
    '你知道罗杰拿了多少个大满贯吗？' : '20个',
    '你知道罗杰是哪个国家吗？' : '瑞士',
}
while True:
    question=input('你好，有什么可以帮助你的嘛？(n/退出):')
    if question=='n':
     break
    for key in sorted(dict.keys()):
        if question==key:
            print(dict[key])
    else:
        print('我不知你在说什么 ')
        
        
        from chatterbot import ChatBot
from chatterbot.trainers import ListTrainer
import nltk

# Create a new instance of a ChatBot
bot = ChatBot(
    'Example Bot',
    storage_adapter='chatterbot.storage.SQLStorageAdapter',
    logic_adapters=[
        {
            'import_path': 'chatterbot.logic.BestMatch',
            'default_response': 'I am sorry, but I do not understand.',
            'maximum_similarity_threshold': 0.90
        }
    ]
)

trainer = ListTrainer(bot)

# Train the chat bot with a few responses
trainer.train([
    'How can I help you?',
    'I want to create a chat bot',
    'Have you read the documentation?',
    'No, I have not',
    'This should help get you started: http://chatterbot.rtfd.org/en/latest/quickstart.html'
])

# Get a response for some unexpected input
response = bot.get_response('How do I make an omelette?')
print(response)
