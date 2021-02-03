# Yunjia-project
# elements from criminal case

import json
import jsonlines

# 导出标注数据annotation
name_jsonl = [] # 建立要素名称集合
with open('/Users/qyx/Desktop/project_100_dataset.jsonl', 'r+', encoding='utf8') as f:
    for item in f:
        name_json = json.loads(item)
        name_jsonl.append(name_json) # 逐条提取内容

list = ['annotations'] 
AnnoResult = []
for i in name_jsonl:
    for key,value in i.items():
        if key in list: #只提取annotations对应的键值
            AnnoResult.append(value) 

# 制作中文对照字典dict1
key1 = ['id'] # 要素编码
key2 = ['text'] # 要素中文名称
with open('/Users/qyx/Desktop/project_10_labels.json', 'r+', encoding='utf8') as g: 
    json_a = json.load(g)

m = []
for i in json_a: 
    for key,value in i.items(): 
        if key in key1:
            m.append(value)
n = []
for i in json_a: 
    for key,value in i.items():         
        if key in key2:
            n.append(value)

dict1 = dict(zip(m,n))

# 统计各要素出现次数
def name_count(anno):
    count_dict={}
    for item in anno:
        for s in item:
            for key,value in s.items():
                if key in ['label']:
                    if value in count_dict:
                        count_dict[value] += 1
                    else: 
                        count_dict[value] = 1
    return count_dict
count_draft = name_count(AnnoResult) # 要素统计结果（键名为数字编号）

# 键名（数字编号）转中文
keykey = dict1.keys()
count_final = {} # 最终统计结果
for key in count_draft:
    if key in keykey:
        new = dict1[key] # 数字对应的中文
        count_final[new] = count_draft[key] # 中文替换数字键（key）

# 统计未出现的键名并转中文
NeverAppear = []
for key in keykey:
    if key in count_draft:
        continue
    else:
        v = dict1[key]
        NeverAppear.append(v)

# 保存数据

import csv

my_dict = count_final
with open('Count.csv', 'w') as f:
    for key in my_dict.keys():
        f.write("%s,%s\n"%(key,my_dict[key]))


my_list = NeverAppear
with open('NA.csv', 'w') as f:
    for item in my_list:
        f.write("%s\n"%(item))
