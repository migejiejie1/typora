# **yaml文件解析详解**

## 前言

yaml文件是什么？yaml文件其实也是一种配置文件类型，相比较ini，conf配置文件来说，更加的简洁，操作也更加简单，同时可以存放不同类型的数据，不会改变原有数据类型，所有的数据类型在读取时都会原样输出，yaml文件依赖python的第三方库PyYaml模块



## PyYaml安装

yaml文件处理需要借助python的第三方库，因此我们第一步需要安装

打开CMD执行命令: pip install pyyaml 



## 读yaml文件

### yaml存字典并读取

config.yaml

```yaml
cnblog: linux超
address: BeiJing
Company: petrochina
age: 18
now: 8.14
empty1: null
empty2: ~
```

parseyaml.py

```python
#!/usr/local/python3/bin/python3

import yaml

with open("01.yml", "r", encoding="utf8") as f:
    context = yaml.load(f, Loader=yaml.FullLoader)
print("读取内容", context, type(context))
print(context["cnblog"], type(context["cnblog"]))
print(context["age"], type(context["age"]))
print(context["now"], type(context["now"]))
print(context["empty1"], type(context["empty1"]))
```

输出

```shell
读取内容 {'cnblog': 'linux超', 'address': 'BeiJing', 'Company': 'petrochina', 'age': 18, 'now': 8.14, 'empty1': None, 'empty2': None} <class 'dict'>
linux超 <class 'str'>
18 <class 'int'>
8.14 <class 'float'>
None <class 'NoneType'>

Process finished with exit code 0
```

从输出结果及yaml文件内容你可以看到，当前输出的内容是一个字典类型，yaml文件中存储的字符串输出仍是字符串类型，int型仍是int型等，存储None类型可以使用null，~符号以及None，这也是区别ini配置文件的地方，且文件内容使用[key：value]的形式定义，当然key和value也可以使用双引号修饰；上面的yaml文件只存储了一组数据，你也可以存放多组数据，看下面的实例

### yaml存多组数据并读取

config.yaml

```yaml
cnblog: linux超
address: BeiJing
Company: petrochina
age: 18
now: 8.14
---
name: linux超
gender: 男
```

parseyaml.py

```python
#!/usr/local/python3/bin/python3

import yaml

with open("config.yml", "r", encoding="utf-8") as f:
    context = yaml.load_all(f, Loader=yaml.FullLoader)
    print(context)
    for i in context:
        print(i)
```

输出

```shell
我是一个生成器 <generator object load_all at 0x01DDDAB0>
{'cnblog': 'linux超', 'address': 'BeiJing', 'Company': 'petrochina', 'age': 18, 'now': 8.14}
{'name': 'linux超', 'gender': '男'}

Process finished with exit code 0
```

想解决打印generator对象的问题, 修改parseyaml.py内容如下: 详见 **Python 解决打印generator对象的问题.md** 文档

parseyaml.py

```python
#!/usr/local/python3/bin/python3

import yaml

with open("config.yml", "r", encoding="utf-8") as f:
    context = yaml.load_all(f, Loader=yaml.FullLoader)
    contexts = list(context)
    for i in contexts:
        print(i)
```

输出

```shell
{'cnblog': 'linux超', 'address': 'BeiJing', 'Company': 'petrochina', 'age': 18, 'now': 8.14}
{'name': 'linux超', 'gender': '男'}

Process finished with exit code 0
```

通过输出结果及yaml存储内容可以看出，当yaml文件存储多组数据在一个yaml文件中时，需要使用3个横杆分割，读取数据时需要使用load_all方法，而且此方法返回一个生成器，需要使用for循环迭代读取每一组数据, 下面再看一下yaml如何存储列表类型数据

### yaml存储列表并读取

config.yaml

```yaml
- linux超
- BeiJing
- petrochina
- 18
- 8.14
```

parseyaml.py

```python
#!/usr/local/python3/bin/python3

import yaml

with open("config.yml", "r", encoding="utf8") as f:
    context = yaml.load(f, Loader=yaml.FullLoader)
print("读取内容", context, type(context))
```

输出

```shell
读取内容 ['linux超', 'BeiJing', 'petrochina', 18, 8.14] <class 'list'>

Process finished with exit code 0
```

当yaml文件存储列表数据时，需要使用一个横杠[- 元素]表示为列表的一个元素，除了列表以外还可以存储元组，或者说支持强制类型转换

### yaml存储元组并读取

config.yml

```yaml
--- !!python/tuple # 列表转成元组
- 1
- 2
- 3
---
age: !!str 18 # int 类型转换为str
```

 parseyaml.py

```python
#!/usr/local/python3/bin/python3

import yaml

with open("./config.yml", "r", encoding="utf-8") as f:
    context = yaml.load_all(f, Loader=yaml.FullLoader)
    for i in context:
        print(i)
```

输出

```shell
(1, 2, 3)
{'age': '18'}

Process finished with exit code 0
```

yaml文件使用两个！！号可以对数据进行类型转换，但是在我看来感觉没有用，当然可能我没遇见过需要做类型转化的情况；你还可以像下面这样存放更加复杂的数据，比如字典嵌套字典及列表

config.yaml

```yaml
info:
  - user:
      username: linux超
      password: linuxxiaochao
company:
  first: petrochina
  second: lemon teacher
```

parseyaml.py

```python
#!/usr/local/python3/bin/python3

import yaml

with open("config.yml", "r", encoding="utf8") as f:
    context = yaml.load(f, Loader=yaml.FullLoader)
print("读取内容\n", context, type(context))
```

输出

```shell
读取内容
 {'info': [{'user': {'username': 'linux超', 'password': 'linuxxiaochao'}}], 'company': {'first': 'petrochina', 'second': 'lemon teacher'}} <class 'dict'>

Process finished with exit code 0
```

### 小结

实际工作中大概就是存储字典，列表，或者相互嵌套的数据较常见，那么在存储和读取时需要掌握以下几点

1.存储字典时，以[key：value]的形式定义

2.存储列表时，需要使用[- 元素]表示列表

3.存储多组数据时，需要每组数据之间使用3个横杠-分割分割

4.数据嵌套时，需要注意缩进，和编写python代码的缩进规则相同，唯一不同是，yaml中的缩进只要统一即可不需要指定缩进多少

5.读取一组数据时，直接使用load(stream, loader)方法， 读取多组数据时需要使用load_all(stream, loader)方法，此方法返回的是一个生成器，需要使用for循环读取每一组数据，还需要注意两个方法中的最好像我代码中一样传递loader参数为FullLoader，否则会报Warnning



## 写yaml文件

向yaml文件中写数据就比较简单了，直接使用dump方法和dump_all方法即可，无论多复杂的数据都可以直接写入，看实例

### dump写入一组数据

```python
#!/usr/local/python3/bin/python3

import yaml

response = {
    "status": 1,
    "code": "1001",
    "data": [
        {
            "id": 80,
            "regname": "toml",
            "pwd": "QW&@JBK!#&#($*@HLNN",
            "mobilephone": "13691579846",
            "leavemount": "0.00",
            "type": "1",
            "regtime": "2019-08-14 20:24:45.0"
        },
        {
            "id": 81,
            "regname": "toml",
            "pwd": "QW&@JBK!#&#($*@HLNN",
            "mobilephone": "13691579846",
            "leavemount": "0.00",
            "type": "1",
            "regtime": "2019-08-14 20:24:45.0"
        }
    ],
    "msg": "获取用户列表成功"
}

try:
    with open("./config.yml", "w", encoding="utf-8") as f:
        yaml.dump(data=response, stream=f, allow_unicode=True)
except Exception as e:
    print("写入yaml文件内容失败")
    raise e
else:
    print("写入yaml文件内容成功")
```

生成的yaml文件内容

```python
code: '1001'
data:
- id: 80
  leavemount: '0.00'
  mobilephone: '13691579846'
  pwd: QW&@JBK!#&#($*@HLNN
  regname: toml
  regtime: '2019-08-14 20:24:45.0'
  type: '1'
- id: 81
  leavemount: '0.00'
  mobilephone: '13691579846'
  pwd: QW&@JBK!#&#($*@HLNN
  regname: toml
  regtime: '2019-08-14 20:24:45.0'
  type: '1'
msg: 获取用户列表成功
status: 1
```

### dump_all写入多组数据

```python
#!/usr/local/python3/bin/python3

import yaml

response = {
    "status": 1,
    "code": "1001",
    "data": [
        {
            "id": 80,
            "regname": "toml",
            "pwd": "QW&@JBK!#&#($*@HLNN",
            "mobilephone": "13691579846",
            "leavemount": "0.00",
            "type": "1",
            "regtime": "2019-08-14 20:24:45.0"
        },
        {
            "id": 81,
            "regname": "toml",
            "pwd": "QW&@JBK!#&#($*@HLNN",
            "mobilephone": "13691579846",
            "leavemount": "0.00",
            "type": "1",
            "regtime": "2019-08-14 20:24:45.0"
        }
    ],
    "msg": "获取用户列表成功"
}

info = {
    "name": "linux超",
    "age": 18
}

try:
    with open("./config.yml", "w", encoding="utf-8") as f:
        yaml.dump_all(documents=[response, info], stream=f, allow_unicode=True)
except Exception as e:
    print("写入yaml文件内容失败")
    raise e
else:
    print("写入yaml文件内容成功")
```

生成的yaml文件内容

```shell
code: '1001'
data:
- id: 80
  leavemount: '0.00'
  mobilephone: '13691579846'
  pwd: QW&@JBK!#&#($*@HLNN
  regname: toml
  regtime: '2019-08-14 20:24:45.0'
  type: '1'
- id: 81
  leavemount: '0.00'
  mobilephone: '13691579846'
  pwd: QW&@JBK!#&#($*@HLNN
  regname: toml
  regtime: '2019-08-14 20:24:45.0'
  type: '1'
msg: 获取用户列表成功
status: 1
---
age: 18
name: linux超
```

### 小结

1.写入一组数据直接使用dump方法或者dump_all方法也可

2.写入多组数据只能使用dump_all方法

3.写入数据时最重要的一点需要注意：如果你的数据包含中文，dump和dump_all 方法需要添加allow_unicode=True参数，否则中文写入后不会正常显示



## 总结

1.yaml存储数据规则-多组数据使用---分割，数据嵌套时注意缩进，存储字典使用[key: value]的形式，存储列表使用[- 元素]的形式，使用load读一组数据，使用load_all 可以读多组数据

2.yaml文件写入一组数据直接使用dump方法，写入多组数据使用dump_all方法，注意写入数据带中文，需要指定参数allow_unicode=True