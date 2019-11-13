---
title: Python Json序列化时时间处理
date: 2019-11-06 16:23:09
tags: 
  - python
categories: 
  - python
---

在Python使用Json序列化字典的时候,如果遇到值是Datetime,Date格式的会报错,总不能手工一个个转,今天查了一下,可以使用dumps的default参数指定一个函数,loads的object_hook指定一个函数,解决这些转换问题

<!--more-->

```
import json
from datetime import datetime, date

def json_dumps_default(obj):
    if isinstance(obj, datetime):
        return obj.strftime('%Y-%m-%d %H:%M:%S')
    elif isinstance(obj, date):
        return obj.strftime('%Y-%m-%d')
    else:
        return obj


def json_loads_hook(dct):
    for k, v in dct.items():
        if k in ["onlinedate", "pre_onlinedate", "mergedate"]:
            try:
                dct[k] = datetime.strptime(v, '%Y-%m-%d %H:%M:%S')
            except:
                pass
    return dct


if __name__ == "__main__":
    data = {"onlinedate": "2019-11-15 16:30:36", "name": "wisp888"}

    d1 = json.dumps(data)
    print(d1)

    d2 = json.loads(d1, object_hook=json_loads_hook)
    print(d2)

    d3 = json.dumps(d2, default=json_dumps_default)
    print(d3)
```

结果:
```
{"onlinedate": "2019-11-15 16:30:36", "name": "wisp888"}
{'onlinedate': datetime.datetime(2019, 11, 15, 16, 30, 36), 'name': 'wisp888'}
{"onlinedate": "2019-11-15 16:30:36", "name": "wisp888"}
```