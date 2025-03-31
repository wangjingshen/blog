## 背景
最近研发同事提了一个统计 16S 富集文库的引物序列的需求，此前脚本使用发现存在一些可以优化的地方，同一条 UMI 的不同 reads 可能含有不同的引物序列，其中有一部分是同一条引物存在简并碱基导致的，此前脚本在处理这个情况会每个子引物序列分别加 1，最终简并引物合并时会加 2；这里更合理的是最终简并引物只计一次，脚本里涉及到这一块的是字典需要进行合并，现整理相关内容如下。

## 方法
方法1：使用字典的 update 函数，若存在相同的键，使用后面键的值
```
merged_dict = d1.copy()
merged_dict.update(d2)
```

方法2: 使用解包操作(**)，若存在相同的键，使用后面键的值
```
merged_dict = {**d1, **d2}
```

方法3：使用 itertools 包的 chain 函数
```
merged_dict = dict(chain(d1.items(), d2.items()))
```

方法4：使用字典推导式
```
merged_dict = {k:v for d in [d1,d2] for k,v in d.items()}
```

方法5：使用合并操作符 |
```
merged_dict = d1 | d2
```

方法6：使用 collections.ChainMap，存在相同的键，使用前面键的值
```
merged_dict = dict(ChainMap(d1,d2))
```

## 一个特殊例子
两个嵌套字典的合并，第一层合并键，第二层相同键的值相加
```
def merge_dicts2(dict1, dict2):
    '''
    子字典相同 key 的值相加
    '''
    merged_dict = {}
    n = 0 
    for key in set(dict1.keys()).union(dict2.keys()):
        if key in dict1 and key in dict2:
            update_dict = {}
            subdicts = [dict1.get(key), dict2.get(key)]
            for item in subdicts:
                for k, v in item.items():
                    if k in update_dict:
                        update_dict[k] = int(update_dict[k]) + int(v)
                    else:
                        update_dict[k] = v
            
            merged_dict[key] = update_dict
        elif key in dict1:
            merged_dict[key] = dict1[key]
        else:
            merged_dict[key] = dict2[key]
    return merged_dict

# 使用示例
dict1 = {"t1":{"t11":1},"t2": {'t21':1}}
dict2 = {"t2":{'t21':1, "t22":1},"t3": {"t31":3}}
merge_dicts2(dict1, dict2)

{'t1': {'t11':1}, 't2': {'t21': 2, 't22': 1}, 't3': {'t31': 3}}
```