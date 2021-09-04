
# 数据类型的基本操作


## 字典操作

### 字典合并更新


```python
a = {'a':1, 'b':2, 'c':3}
b = {'aa':1, 'bb':2, 'cc':3}

```


```python
dict(a, **b)
```




    {'a': 1, 'b': 2, 'c': 3, 'aa': 1, 'bb': 2, 'cc': 3}




```python
c = {}
c.update(a)
c.update(b)
c
```




    {'a': 1, 'b': 2, 'c': 3, 'aa': 1, 'bb': 2, 'cc': 3}



### 字典使用注意：for 循环过程中，不能改变字典的键值序列。否则会报错 


```python
example_dict= {"A":1, "B":2}
for key, value in example_dict.items():
    if key == "A":
        example_dict["C"] = 3
        
# 针对此种情况，类似于列表for循环过程中删除一样，另存一个变量，存储需要操作的键值对，在for结束时操作即可

```


    ---------------------------------------------------------------------------

    RuntimeError                              Traceback (most recent call last)

    <ipython-input-125-c29204b4c504> in <module>
          1 example_dict= {"A":1, "B":2}
    ----> 2 for key, value in example_dict.items():
          3     if key == "A":
          4         example_dict["C"] = 3


    RuntimeError: dictionary changed size during iteration


### 字典删除键值对：pop


```python
example_dict= {"A":1, "B":2}
example_dict.pop("A")
```




    1




```python
example_dict
```




    {'B': 2}



## 字符串的替换提取



```python
str_var = "-(([B2_edge] -【edge】)*【eb1】+([B2_flap] -【flap】)*【eb2 】)"

```

提取框括号中的内容


```python
import re
vars = re.findall(r'\[([^\]]+)\]',str_var,)
cons = re.findall(r"【([^】]+)】",str_var,)
print(vars)
print(cons)
```

    ['B2_edge', 'B2_flap']
    ['edge', 'eb1', 'flap', 'eb2 ']


### 替换特殊字符


```python
a = "b"
b = "0.5"
str_var_3 = "【b】 + 10 + 【b】"
str_var_2 = str_var_3.replace(a , b)
str_var_2
```




    '【0.5】 + 10 + 【0.5】'



#### 正则能匹配更多


```python
re.sub(r"[【,】]","",str_var_2)
```




    '-(([B2_edge] -edge)*eb1+([B2_flap] -flap)*eb2 )'



### 字符串的去空格或者tab



```python
str_s = " sd fas  asf asdfasdf "
# 采用pattern复用性更高
pattern = re.compile(r"[\s,\t]")
str_s = pattern.sub("", str_s)
# pat
# str_s = re.sub(r"[\s,\t]","",str_s)
str_s
```




    'sdfasasfasdfasdf'



### 字符串搜索


```python
# 注意str.find找不到返回-1
str_s.find("测试")
```




    -1




```python
# 正则需要使用.group()获取内容
# 注意search，找到即返回。
re.search(r"asdfas", str_s).group()
```




    'asdfas'



### 正则匹配换行符

正则符号：“.”默认不匹配换行符，需手动更改匹配模式:re.DOTALL。（如果匹配要求较为复杂，可能引起一些其他错误。）


```python
import re
str_test = "测试文本\n第一行\n第二行\n第三行\n结束"
result_1 = re.findall(r"文本(.+)结束", str_test)
print(result_1)
result_2 = re.findall(r"文本(.+)结束", str_test, re.DOTALL)
print(result_2)
```

    []
    ['\n第一行\n第二行\n第三行\n']


### 正则提取表达式中的非运算符


```python
import re
expr_str_list = [
    "-((75216.86*Twr_D009410_BS1-6350.94)*1000)*cos((WTG_Nac_Pos-318.5)*(pi)/180)-((74934.65*Twr_D009410_BS2+77.56)*1000)*sin((WTG_Nac_Pos-318.5)*(pi)/180)",
    "(-27356.4*Twr_D076400_Trq-3.619252E+03)*1000",
#     "mod(WTG_Rotor_Pos*180/pi,360)",
"NacMF_Zone2HS1_BS1*(394194812.267713)+(107181569.455591)",
    "Hub_PB2B49_BS4*(379273707.376435)+(-666004630.15302)",
]
expr_factors_list = []
pattern = re.compile(
                     r"^[+-][0-9.]+[Ee][+-]?[0-9]+"   # 开头是正负负号的科学计数
                     r"|(?<=\()[+-][0-9.]+[Ee][+-]?[0-9]+"    # 括号中开头是正负符号的科学计数
                     r"|[0-9.]+[Ee][+-]?[0-9]+"    # 正常科学计数
                     r"|^[+-][0-9.]+"  # 开头是正负符号的数字
                     r"|(?<=\()[+-][0-9.]+"    # 在括号中的开头正负符号的数字
                     r"|[^+\-*/()]+"  # 正常数
                     )   

for expr_str in expr_str_list:
    expr_factors = pattern.findall(expr_str)
    expr_factors_list.append(expr_factors)

import pprint
pp = pprint.PrettyPrinter()
pp.pprint(expr_factors_list)

```

    [['75216.86',
      'Twr_D009410_BS1',
      '6350.94',
      '1000',
      'cos',
      'WTG_Nac_Pos',
      '318.5',
      'pi',
      '180',
      '74934.65',
      'Twr_D009410_BS2',
      '77.56',
      '1000',
      'sin',
      'WTG_Nac_Pos',
      '318.5',
      'pi',
      '180'],
     ['-27356.4', 'Twr_D076400_Trq', '3.619252E+03', '1000'],
     ['NacMF_Zone2HS1_BS1', '394194812.267713', '107181569.455591'],
     ['Hub_PB2B49_BS4', '379273707.376435', '-666004630.15302']]


#### 验证提取到的是数字还是变量


```python
def is_number(s):
    try:
        float(s) # for int, long and float
    except ValueError:
        try:
            complex(s) # for complex
        except ValueError:
            return False
    return True

for expr_factors in expr_factors_list:
    for factor in expr_factors:
        if is_number(factor):
            print(factor)
```

    75216.86
    6350.94
    1000
    318.5
    180
    74934.65
    77.56
    1000
    318.5
    180
    -27356.4
    3.619252E+03
    1000
    394194812.267713
    107181569.455591
    379273707.376435
    -666004630.15302


### 正则按条件替换字符

#### 利用分组


```python
# 例：只替换pi值，而非变量中的字符pi
str_test_1 = 'A_5pibe+5*pi/180'
result = re.sub(r"([+\-*/()]+|,|^)pi([+\-*/()]+|$|,)", "\g<1>3.1415926\g<2>", str_test_1)
print(result)
```

    A_5pibe+5*3.1415926/180


#### 利用前后回顾


```python
# 　　　　　　前瞻： exp1(?=exp2) exp1后面的内容要匹配exp2
# 　　　　　　负前瞻: exp1(?!exp2) exp1后面的内容不能匹配exp2
# 　　　　　　后顾: (?<=exp2)exp1 exp1前面的内容要匹配exp2
# 　　　　　　负后顾: (?<!exp2)exp1 exp1前面的内容不能匹配exp2
pattern = re.compile(r"(?<!\w)pi(?=[+\-*/()+]+|,|$)")
result_1 = pattern.sub("3.1415926",str_test_1)
print(result_1)
```

    A_5pibe+5*3.1415926/180


### 正则查找文中重复的字符串


```python
import re
str_test = 'aaabccc11fdsa'
re.findall(r'((\w)\2+)', str_test)
```




    [('aaa', 'a'), ('ccc', 'c'), ('11', '1')]




```python
[match[0] for match in re.findall(r'((\w)\2+)', str_test)]
```




    ['aaa', 'ccc', '11']



### 正则判定字符是否符合要求


```python
example_str = "x_11"
pattern = re.compile(r"x_[0-9]$")
result = pattern.match(example_str)
if result:
    print("符合规则")
else:
    print("不符合规则")
```

    不符合规则


## 集合操作


```python
set1 = {1,2,3,4,5}
set2 = {2,3,6}
set3 = {4,5,6}
set4 = {1}
# 
print(len(set4 - set1))
# 求集合并集
set4 = set2 | set3
print(set4)
# 求集合交集
set5 = set3 & set2
print(set5)
# 在1中存在，而2和3中不存在的。
set6 = set1 - (set2 | set3)
print(set6)
# print(set4 - set1)
```

    0
    {2, 3, 4, 5, 6}
    {6}
    {1}


### 集合添加元素


```python
set_1 = {1,2,3}
set_1 = set_1 | {4}
a = 5
set_1 = set_1 | {a}
set_1
```




    {1, 2, 3, 4, 5}



## 列表操作

### 利用列表排序，找到给定值得所属档次


```python
# 例有6档：<-100,<-10,<0,<10,<100,
def get_level(ref_value, reverse, left_close = True):
    """
    ref_value:要判定的所属档次的值
    reverse：level档次是从小到大，还是从大到小
    left_close:档次区间是否是左闭合，注意，要按照reverse的顺序算左闭合
    """
    
    adjust_level = 0  # 等于情况的调整值
    level_list = [i/abs(i) * 10 ** abs(i) if i !=0 else 0 for i in range(-2,3)]  # 档次阈值列表
    print(level_list)
    if ref_value in level_list:
        adjust_level = 1
    level_list.append(ref_value)
    level_list.sort(reverse = reverse)
    level = level_list.index(ref_value) + adjust_level
    
    return level

get_level(100, True, True)
```

    [-100.0, -10.0, 0, 10.0, 100.0]





    1



### 列表的插入


```python
example_list = [1,2,3]
example_list.insert(0, 0)
example_list
```




    [0, 1, 2, 3]



# 时间处理

## 字符串转成浮点型/时间戳


```python
import time
str_1 = "2018-12-03_09:49:18"
time_str = time.mktime(time.strptime(str_1, '%Y-%m-%d_%H:%M:%S'))
time_str

```




    1543801758.0



## 将时间戳/浮点型，转换成时间


```python
time_str = "18/12/03 09:49:18"
time_1 = time.localtime(time_str)
time_1
```




    time.struct_time(tm_year=2018, tm_mon=12, tm_mday=12, tm_hour=1, tm_min=5, tm_sec=0, tm_wday=2, tm_yday=346, tm_isdst=0)



## 格式时间，即转成字符串


```python
# 格式化成2016-03-20 11:45:39形式
print(time.strftime("%Y-%m-%d %H:%M:%S", time_1))
 
# 格式化成Sat Mar 28 22:24:24 2016形式
print(time.strftime("%a %b %d %H:%M:%S %Y", time_1))

```

    2018-12-12 01:05:00
    Wed Dec 12 01:05:00 2018


## 获取当前时间


```python
localtime = time.localtime(time.time())

print(localtime)
```

    time.struct_time(tm_year=2018, tm_mon=12, tm_mday=24, tm_hour=16, tm_min=59, tm_sec=41, tm_wday=0, tm_yday=358, tm_isdst=0)


## 获取某月日历


```python
import calendar
cal = calendar.month(2016, 1)
print("以下输出2016年1月份的日历:")
print(cal)
```

    以下输出2016年1月份的日历:
        January 2016
    Mo Tu We Th Fr Sa Su
                 1  2  3
     4  5  6  7  8  9 10
    11 12 13 14 15 16 17
    18 19 20 21 22 23 24
    25 26 27 28 29 30 31
    


## 获取第几周第几天


```python
from datetime import datetime
```


```python
datetime.now().isocalendar() 
```




    (2019, 48, 4)



# 类/对象操作

## 类属性查看


```python
class Dog:
    color = "red"
    def eat():
        print("gutou")
```


```python
Dog.__dict__
```




    mappingproxy({'__module__': '__main__',
                  'color': 'red',
                  'eat': <function __main__.Dog.eat()>,
                  '__dict__': <attribute '__dict__' of 'Dog' objects>,
                  '__weakref__': <attribute '__weakref__' of 'Dog' objects>,
                  '__doc__': None})




```python
#　类外部可以通过__dict__查看，但无法修改或添加。但对象的属性可以通过这个修改添加
Dog.__dict__['look'] = '看'
```


    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-33-b0f6cecb2756> in <module>
          1 #　类外部可以通过__dict__查看，但无法修改或添加。但对象的属性可以通过这个修改添加
    ----> 2 Dog.__dict__['look'] = '看'
    

    TypeError: 'mappingproxy' object does not support item assignment


## 对象属性操作


```python
dog = Dog()
dog.__dict__
```




    {}




```python
dog.__dict__['legs'] = 4
dog.legs
```




    4



## 通过对象创建对象


```python
dog_copy = dog.__class__()
dog_copy.color
```




    'red'



# 文件处理


## 写入

### 将字典写入文件


```python
import json

dict1 = {"a": "b", "c": "d"}
dictObj = {
    'andy': {
        'age': 23,
        'city': 'shanghai',
        'skill': 'python'
    },
    'william': {
        'age': 33,
        'city': 'hangzhou',
        'skill': 'js'
    }
}


jsObj = json.dumps(
    dictObj,
    ensure_ascii=False,
    sort_keys=True,
    indent=4,
    separators=(
        ',',
        ': '))

fileObject = open('jsonFile.json', 'w')
fileObject.write(jsObj)
fileObject.close()
```

## 读取

### 遍历文本的行内容


```python
with open("jsonFile.json", "r") as f:
    lines = f.readlines()
for line in lines:
    print(line)
```

    {
    
        "andy": {
    
            "age": 23,
    
            "city": "shanghai",
    
            "skill": "python"
    
        },
    
        "william": {
    
            "age": 33,
    
            "city": "hangzhou",
    
            "skill": "js"
    
        }
    
    }



```python
np.sin(30)
```


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    <ipython-input-5-332a93682cfd> in <module>
    ----> 1 np.sin(30)
    

    NameError: name 'np' is not defined


## Python处理配置文件

### conf格式


```python
import configparser

cf=configparser.ConfigParser()
cf.read("conf1.conf")
#返回所有的section
s=cf.sections()
print('section:',s) # 配置文件中，所有版块项

o=cf.options("db")
print('options:',o)     # 配置文件中，所有的options项

v=cf.items("db")
print('db:',v)      # 配置文件中，所有的db项目及其值，列表嵌套元组

print('-'*60)
#可以按照类型读取出
db_host=cf.get("db","db_host")
db_port=cf.getint("db","db_port")   # 默认获取字符串型，
db_user=cf.get("db","db_user")
db_pass=cf.get("db","db_pass")


#返回的是整型的
threads=cf.getint("concurrent","thread")
processors=cf.getint("concurrent","processor")

print("db_host:",db_host, type(db_host))
print("db_port:",db_port, type(db_port))
print("db_user:",db_user, type(db_user))
print("db_pass:",db_pass, type(db_pass))

print("thread:",threads)
print("processor:",processors)
#修改一个值，再写回去
cf.set("db","db_pass","zhaowei")
cf.write(open("conf1.conf","w"))
```

    section: ['db', 'concurrent']
    options: ['db_host', 'db_port', 'db_user', 'db_pass']
    db: [('db_host', '127.0.0.1'), ('db_port', '3306'), ('db_user', 'root'), ('db_pass', 'zhaowei')]
    ------------------------------------------------------------
    db_host: 127.0.0.1 <class 'str'>
    db_port: 3306 <class 'int'>
    db_user: root <class 'str'>
    db_pass: zhaowei <class 'str'>
    thread: 10
    processor: 20


### yaml格式

python 处理yaml文件，需要安装pyyaml库。读取后，直接是一个Python字典类型数据。


```python
import yaml
with open("yaml_test1.yaml", "r") as f:
    content = yaml.load(f)
print(content)
```

    {'AutoOpenNum': 99, 'AutoOpenPdf': True, 'ChannelList': [{'Chan': 'B1_D001100_BS000T180', 'Unit': '(-)'}, {'Chan': 'B1_D001100_BS090T270', 'Unit': '(-)'}, {'Chan': 'B2_D001100_BS000T180', 'Unit': '(-)'}, {'Chan': 'B2_D001100_BS090T270', 'Unit': '(-)'}, {'Chan': 'B3_D001100_BS000T180', 'Unit': '(-)'}, {'Chan': 'B3_D001100_BS090T270', 'Unit': '(-)'}, {'Chan': 'B1Pitch_Motor_Spd', 'Unit': '(-)'}, {'Chan': 'B1Pitch_Motor_Trq', 'Unit': '(-)'}, {'Chan': 'B2Pitch_Motor_Spd', 'Unit': '(-)'}, {'Chan': 'B2Pitch_Motor_Trq', 'Unit': '(-)'}, {'Chan': 'B3Pitch_Motor_Spd', 'Unit': '(-)'}], 'IOVers': 2.2, 'PlotParams': {'Display': True, 'TimeSeriesPlot': {'graphics': {'alpha': 0.7, 'colors': ['k', 'g', 'r', 'b', 'y', 'c', 'm'], 'height': 3.5, 'heightSpace': 0.3, 'lineStyles': ['-', '--', '-.', ':'], 'markerStyles': ['o', 's', 'v', '+', 'x', '^'], 'width': 9.0, 'widthSpace': 0.1}, 'psdMax': 5.0, 'psdStep': 0.2, 'time0': True, 'timeMark': True, 'tsStep': 20.0}}, 'ResultList': [{'Name': 'vdm_sample', 'Path': 'sample_data\\2019-01-07_07-00-00.h5', 'SolverType': 'VDM_RAW'}], 'SavePlotFile': False}



```python
content["AutoOpenNum"] = 999
with open("yaml_test2.yaml", "w") as f:
    f.write(yaml.dump(content))

```

### json格式


```python
json_dict = {"key1":{"key1_1":"a", "key1_2":[1,2,3]},
            "key2" :{"key2_1":[[1,2,3],[2,3,4]]}          }
json_ = json.dumps(json_dict,
                   ensure_ascii=False,
                   indent=4,
                   sort_keys=True,
                   separators=(',', ': '))

with open("json_test.json", "w", encoding="utf8") as f:
    f.write(json_)
```

# 文件路径操作

## 获取当前脚本路径

用__file__ 来获得脚本所在的路径是比较方便的，但这可能得到的是一个相对路径，比如在脚本test.py中写入：

#!/usr/bin/env python

print  \__file__ 

按相对路径./test.py来执行，则打印得到的是相对路径，
按绝对路径执行则得到的是绝对路径。
而按用户目录来执行（~/practice/test.py），则得到的也是绝对路径（~被展开）

所以为了得到绝对路径，我们需要 os.path.realpath(__file__)。


```python
import os
# __file__ 只有在文件执行时才有用

# 获取此行代码所在文件的绝对路径
os.path.realpath(__file__) 

# 获取此行代码所在的相对路径

```


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    <ipython-input-18-ae1a64dbf415> in <module>
          1 import os
    ----> 2 os.path.realpath(__file__)
    

    NameError: name '__file__' is not defined


## 获取路径下所有文件及文件夹


```python
import os
os.walk(".")
for root, subfolders, files in os.walk("."):
    print(root)
    print(subfolders)
    print(files)
```

    .
    ['.ipynb_checkpoints']
    ['tips_01.ipynb', 'numpy_pandas_tips.ipynb', 'jsonFile.json']
    ./.ipynb_checkpoints
    []
    ['numpy_pandas_tips-checkpoint.ipynb', 'tips_01-checkpoint.ipynb']


## 获取文件加下最新的文件


```python

def new_report(test_report):
    lists = os.listdir(test_report)                                    #列出目录的下所有文件和文件夹保存到lists
    print(list)
    lists.sort(key=lambda fn:os.path.getmtime(test_report + "/" + fn))#按时间排序
    file_new = os.path.join(test_report,lists[-1])                     #获取最新的文件保存到file_new
    print(file_new)
    return file_new
if __name__=="__main__":
    test_report="./" # 目录地址
    new_report(test_report)
```

    <class 'list'>
    ./tips_01.ipynb


## 获取路径下指定名称特点的文件



```python
import glob
path_list = glob.glob("y*.yaml") # 通配符
print(path_list)
```

    ['yaml_test1.yaml', 'yaml_test2.yaml']


## 获取绝对路径、文件名、上一级路径


```python
import glob
path_list = glob.glob("y*.yaml") # 通配符
for path in path_list:
    print(os.path.abspath(path))  # 绝对路径
    print(os.path.basename(path)) # 文件名
    print(os.path.dirname(os.path.abspath(path))) # 上一级路径
    print(os.path.pardir)
    
```

    /home/sht/workspace/jupyter_workspace/Tips_of_Python/yaml_test1.yaml
    yaml_test1.yaml
    /home/sht/workspace/jupyter_workspace/Tips_of_Python
    ..
    /home/sht/workspace/jupyter_workspace/Tips_of_Python/yaml_test2.yaml
    yaml_test2.yaml
    /home/sht/workspace/jupyter_workspace/Tips_of_Python
    ..


# 动态模块导入

Python动态模块导入，主要依赖importlib包


```python
import glob # 该模块可以用通配符的方式获取路径下所有文件
import importlib
import os


def import_modules(pathname: str) -> dict: # Python3.5以后加入的类型提示或检测，有助于ide环境下类型检测，不影响运行
    """
    导入指定路径或者目录下的模块，并返回模块信息

    :param pathname: 要导入的模块路径(相对路径)，可以导入指定目录下的模块，只要符合glob路径表达式写法即可
    :return: 模块信息字典
    """
    modules_dict = {}
    module_paths = glob.glob(pathname)
    for path in module_paths:
        module_name = path.replace(os.sep, '.')[:-3]
        module = importlib.import_module(module_name)
        for element in dir(module):  # 注意，element为str类型
            # 获取用户自定义的函数和变量名称。
            if not element.startswith('__'):
                modules_dict[element] = eval('module.{}'.format(element))  # 实际的对象要通过eval来获取

    return modules_dict


# if __name__ == '__main__':
    # 导入views目录下的所有模块，并打印模块信息
#     module_info = import_modules('views/**.py')
#     print(module_info)
# --------------------- 
# 作者：浅醉樱花雨 
# 来源：CSDN 
# 原文：https://blog.csdn.net/u013314786/article/details/78965741 
# 版权声明：本文为博主原创文章，转载请附上博文链接！
```


```python
var = 10
list1 = [-100,-10,0,10,100]
list1.append(var)
list1.sort(reverse= True)
```


```python
list1
```




    [100, 10, 10, 0, -10, -100]




```python
list1.index(var)
```




    1




```python
import os
output = os.popen("cat /proc/cpuinfo")

print(output.read())
```

    processor	: 0
    vendor_id	: GenuineIntel
    cpu family	: 6
    model		: 79
    model name	: Intel(R) Xeon(R) CPU E5-2682 v4 @ 2.50GHz
    stepping	: 1
    microcode	: 0x1
    cpu MHz		: 2499.996
    cache size	: 40960 KB
    physical id	: 0
    siblings	: 1
    core id		: 0
    cpu cores	: 1
    apicid		: 0
    initial apicid	: 0
    fpu		: yes
    fpu_exception	: yes
    cpuid level	: 13
    wp		: yes
    flags		: fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ss syscall nx pdpe1gb rdtscp lm constant_tsc rep_good nopl eagerfpu pni pclmulqdq ssse3 fma cx16 pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand hypervisor lahf_lm abm 3dnowprefetch fsgsbase tsc_adjust bmi1 hle avx2 smep bmi2 erms invpcid rtm rdseed adx smap xsaveopt
    bogomips	: 4999.99
    clflush size	: 64
    cache_alignment	: 64
    address sizes	: 46 bits physical, 48 bits virtual
    power management:
    
    



```python
"ce".endswith("e")
```




    True




```python
dict1 = {"A":{"x_1":1},"B":2}
dict2 = {"A":{"x_2":2},"B":2}
dict1.update(dict2)
dict1
```




    {'A': {'x_2': 2}, 'B': 2}




```python
dict1
```




    {'A': 2, 'B': 2}



# 装饰器的使用


```python
from functools import wraps

class A:
    @decorator1
    def func1(self, a):
        return a * 2
    
    # 生成channel对象
    def decorator1(self, func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            result = func(*args, **kwargs)
            result = result + 1
            return result
        return wrapper

a = A()
print(a.func1(2))
```

    5


# 过滤序列

## 按大小选取序列的前几个


```python
import heapq
nums = [10, 2, 9, 100, 80]
heapq.nlargest(3,nums)
```




    [100, 80, 10]




```python
heapq.nsmallest(3,nums)
```




    [2, 9, 10]




```python
students = [{'names':'CC', 'score':100, 'height':189},
           {'names':'CC', 'score':10, 'height':169},
           {'names':'CC', 'score':80, 'height':179},]
heapq.nsmallest(2, students, key = lambda x:x['height'])
```




    [{'names': 'CC', 'score': 10, 'height': 169},
     {'names': 'CC', 'score': 80, 'height': 179}]



## 按条件选取/过滤序列


```python

```


#  日志设置

## 日志过滤器实现只打印当前文件的logger


```python
import logging
class LoggerFliter(logging.Filter):
    """
    log Filter for lackey
    Remove the failed match log record generated by the lackey module.
    """
    def filter(self, record):
        if record.filename.find(os.path.basename(__file__))>-1:
            return True
        return False
# logger.addFilter(LoggerFliter())
```

## 日志的disabled属性，实现只打印当前logger


```python
# all_loggers = list(logging.root.manager.loggerDict.values())
# for log in all_loggers:
#     if log is not logger:
#         log.disabled = True
```

log.disabled 属性为True的情况下，该log不再输出

# 获取动态编译后的函数


```python
str_test = """
def func2():
    print('This is func2')
"""
scope = dict()
exec(str_test, scope)
print(dir(scope))
func2 = scope['func2']
print(scope)
func2()
```

    ['__class__', '__contains__', '__delattr__', '__delitem__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__setitem__', '__sizeof__', '__str__', '__subclasshook__', 'clear', 'copy', 'fromkeys', 'get', 'items', 'keys', 'pop', 'popitem', 'setdefault', 'update', 'values']
    {'__builtins__': {'__name__': 'builtins', '__doc__': "Built-in functions, exceptions, and other objects.\n\nNoteworthy: None is the `nil' object; Ellipsis represents `...' in slices.", '__package__': '', '__loader__': <class '_frozen_importlib.BuiltinImporter'>, '__spec__': ModuleSpec(name='builtins', loader=<class '_frozen_importlib.BuiltinImporter'>), '__build_class__': <built-in function __build_class__>, '__import__': <built-in function __import__>, 'abs': <built-in function abs>, 'all': <built-in function all>, 'any': <built-in function any>, 'ascii': <built-in function ascii>, 'bin': <built-in function bin>, 'callable': <built-in function callable>, 'chr': <built-in function chr>, 'compile': <built-in function compile>, 'delattr': <built-in function delattr>, 'dir': <built-in function dir>, 'divmod': <built-in function divmod>, 'eval': <built-in function eval>, 'exec': <built-in function exec>, 'format': <built-in function format>, 'getattr': <built-in function getattr>, 'globals': <built-in function globals>, 'hasattr': <built-in function hasattr>, 'hash': <built-in function hash>, 'hex': <built-in function hex>, 'id': <built-in function id>, 'input': <bound method Kernel.raw_input of <ipykernel.ipkernel.IPythonKernel object at 0x7f2059f1f080>>, 'isinstance': <built-in function isinstance>, 'issubclass': <built-in function issubclass>, 'iter': <built-in function iter>, 'len': <built-in function len>, 'locals': <built-in function locals>, 'max': <built-in function max>, 'min': <built-in function min>, 'next': <built-in function next>, 'oct': <built-in function oct>, 'ord': <built-in function ord>, 'pow': <built-in function pow>, 'print': <built-in function print>, 'repr': <built-in function repr>, 'round': <built-in function round>, 'setattr': <built-in function setattr>, 'sorted': <built-in function sorted>, 'sum': <built-in function sum>, 'vars': <built-in function vars>, 'None': None, 'Ellipsis': Ellipsis, 'NotImplemented': NotImplemented, 'False': False, 'True': True, 'bool': <class 'bool'>, 'memoryview': <class 'memoryview'>, 'bytearray': <class 'bytearray'>, 'bytes': <class 'bytes'>, 'classmethod': <class 'classmethod'>, 'complex': <class 'complex'>, 'dict': <class 'dict'>, 'enumerate': <class 'enumerate'>, 'filter': <class 'filter'>, 'float': <class 'float'>, 'frozenset': <class 'frozenset'>, 'property': <class 'property'>, 'int': <class 'int'>, 'list': <class 'list'>, 'map': <class 'map'>, 'object': <class 'object'>, 'range': <class 'range'>, 'reversed': <class 'reversed'>, 'set': <class 'set'>, 'slice': <class 'slice'>, 'staticmethod': <class 'staticmethod'>, 'str': <class 'str'>, 'super': <class 'super'>, 'tuple': <class 'tuple'>, 'type': <class 'type'>, 'zip': <class 'zip'>, '__debug__': True, 'BaseException': <class 'BaseException'>, 'Exception': <class 'Exception'>, 'TypeError': <class 'TypeError'>, 'StopAsyncIteration': <class 'StopAsyncIteration'>, 'StopIteration': <class 'StopIteration'>, 'GeneratorExit': <class 'GeneratorExit'>, 'SystemExit': <class 'SystemExit'>, 'KeyboardInterrupt': <class 'KeyboardInterrupt'>, 'ImportError': <class 'ImportError'>, 'ModuleNotFoundError': <class 'ModuleNotFoundError'>, 'OSError': <class 'OSError'>, 'EnvironmentError': <class 'OSError'>, 'IOError': <class 'OSError'>, 'EOFError': <class 'EOFError'>, 'RuntimeError': <class 'RuntimeError'>, 'RecursionError': <class 'RecursionError'>, 'NotImplementedError': <class 'NotImplementedError'>, 'NameError': <class 'NameError'>, 'UnboundLocalError': <class 'UnboundLocalError'>, 'AttributeError': <class 'AttributeError'>, 'SyntaxError': <class 'SyntaxError'>, 'IndentationError': <class 'IndentationError'>, 'TabError': <class 'TabError'>, 'LookupError': <class 'LookupError'>, 'IndexError': <class 'IndexError'>, 'KeyError': <class 'KeyError'>, 'ValueError': <class 'ValueError'>, 'UnicodeError': <class 'UnicodeError'>, 'UnicodeEncodeError': <class 'UnicodeEncodeError'>, 'UnicodeDecodeError': <class 'UnicodeDecodeError'>, 'UnicodeTranslateError': <class 'UnicodeTranslateError'>, 'AssertionError': <class 'AssertionError'>, 'ArithmeticError': <class 'ArithmeticError'>, 'FloatingPointError': <class 'FloatingPointError'>, 'OverflowError': <class 'OverflowError'>, 'ZeroDivisionError': <class 'ZeroDivisionError'>, 'SystemError': <class 'SystemError'>, 'ReferenceError': <class 'ReferenceError'>, 'BufferError': <class 'BufferError'>, 'MemoryError': <class 'MemoryError'>, 'Warning': <class 'Warning'>, 'UserWarning': <class 'UserWarning'>, 'DeprecationWarning': <class 'DeprecationWarning'>, 'PendingDeprecationWarning': <class 'PendingDeprecationWarning'>, 'SyntaxWarning': <class 'SyntaxWarning'>, 'RuntimeWarning': <class 'RuntimeWarning'>, 'FutureWarning': <class 'FutureWarning'>, 'ImportWarning': <class 'ImportWarning'>, 'UnicodeWarning': <class 'UnicodeWarning'>, 'BytesWarning': <class 'BytesWarning'>, 'ResourceWarning': <class 'ResourceWarning'>, 'ConnectionError': <class 'ConnectionError'>, 'BlockingIOError': <class 'BlockingIOError'>, 'BrokenPipeError': <class 'BrokenPipeError'>, 'ChildProcessError': <class 'ChildProcessError'>, 'ConnectionAbortedError': <class 'ConnectionAbortedError'>, 'ConnectionRefusedError': <class 'ConnectionRefusedError'>, 'ConnectionResetError': <class 'ConnectionResetError'>, 'FileExistsError': <class 'FileExistsError'>, 'FileNotFoundError': <class 'FileNotFoundError'>, 'IsADirectoryError': <class 'IsADirectoryError'>, 'NotADirectoryError': <class 'NotADirectoryError'>, 'InterruptedError': <class 'InterruptedError'>, 'PermissionError': <class 'PermissionError'>, 'ProcessLookupError': <class 'ProcessLookupError'>, 'TimeoutError': <class 'TimeoutError'>, 'open': <built-in function open>, 'copyright': Copyright (c) 2001-2018 Python Software Foundation.
    All Rights Reserved.
    
    Copyright (c) 2000 BeOpen.com.
    All Rights Reserved.
    
    Copyright (c) 1995-2001 Corporation for National Research Initiatives.
    All Rights Reserved.
    
    Copyright (c) 1991-1995 Stichting Mathematisch Centrum, Amsterdam.
    All Rights Reserved., 'credits':     Thanks to CWI, CNRI, BeOpen.com, Zope Corporation and a cast of thousands
        for supporting Python development.  See www.python.org for more information., 'license': See http://www.python.org/3.6/license.html, 'help': Type help() for interactive help, or help(object) for help about object., '__IPYTHON__': True, 'display': <function display at 0x7f20635e8488>, 'get_ipython': <bound method InteractiveShell.get_ipython of <ipykernel.zmqshell.ZMQInteractiveShell object at 0x7f2059f1ff98>>}, 'func2': <function func2 at 0x7f20354c1950>}
    This is func2


# 装饰器捕获Error


```python
def catch_except(exception_class, msg="Catched Error", return_value=None):
    """
    Catch exception decorator, omit "try...except..."
    :param exception_class: Exception class
    :param msg: when specified exception is caught, will log this message with ERROR level
    :param return_value: If the given value of this parameter is not "None", the value will be returned when the
    specified exception is caught.
    :return:
    """
    def except_execute(func):
        @wraps(func)
        def except_print(*args, **kwargs):
            try:
                return func(*args, **kwargs)
            except exception_class:
                logger.error(f"{msg}")
                import traceback
                logger.error(f'{traceback.format_exc()}')
                if return_value is not None:
                    return return_value
        return except_print
    return except_execute
```

# 获取本机IP地址


```python
# for window OS
# host = [a for a in os.popen('route print').readlines() if ' 0.0.0.0 ' in a][0].split()[-2]
# for linux OS
import os
host = [a for a in os.popen('/sbin/route').readlines() if 'default' in a][0].split()[1]
host
```




    'gateway'




```python

```
