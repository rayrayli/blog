---
title: python中字符串插值替换
date: 2021-07-05 23:08:57
tags: language
---

python string interpolation，我在这里翻译成字符串插值替换。 字符串插值替换常见于`f-strings`, `uniterploated c-string` 和`str.format()`三种字符串格式化方法中. 这篇blog来比较一下三种不同的插值方法，以及在什么场景下分别用哪一种。


## c-string
 即printf-style，这是python中最原始的字符串的格式化方式，学过C语言的都知道，常见使用方式为字符串中使用%conversionType 占位 ， conversiontype 用来代表需要插值的类型，字符串为%s, 整型为%d，等等。
 比如：
`	'my name is %s, I'm %d years old.' % ( 'Rui', 18) `

 
## str.format()

为python 2.6引入, 支持特定类型的格式化，比如Datetime：
```
from datetime import date
d = date.fromordinal(730920) # 730920th day after 1. 1. 0001
fmt='the {1} is {0:%d}, the {2} is {0:%B}.format(d, "day", "month")
'The day is 11, the month is March.'
```
同一个字符串中支持关键字参数和位置参数的混合使用等。


## f-string
Python 3.6 引入的。保留了str.format()的特性，同时减少了运行时间，又增加了可读性。
例如：
age = 30, name = Rui
'my name is {name} , I'm {max(18, age)} years old'

## 该用哪一个

先说结论：
如果你是python 3.6 以上的版本，默认使用f-strings，否则使用printf-style。
如果是logging的情况，使用uninterpolated c-strings。
如果是templating的情况，使用str.format().

###  性能比对

f-strings 既包含了str.format()的功能，又拥有c-strings的执行速度。

为了测试他们的性能，我安装了line_profiler, 并且写一段python代码，重复执行10000次进行比较f-string, c-string 和 str.format() 的运行速度：
```
#!/usr/bin/python  
  
import line_profiler  
import atexit  
  
profile = line_profiler.LineProfiler()  
atexit.register(profile.print_stats)  
  
  
@profile  
def printf_style(p1, p2):  
    'param 1: %s, param2: %s' %(p1, p2)  
  
  
@profile  
def f_string_style(p1, p2):  
    f'param 1:{p1}, param2: {p2}'  
  
  
@profile  
def format_style(p1, p2):  
    'param1: {}, param2: {}'.format(p1, p2)  
  
  
p1 = "hi, this is p1"  
p2 = Exception("hi, this is exception")  
  
for i in range(10000):  
    printf_style(p1, p2)  
    f_string_style(p1, p2)  
    format_style(p1, p2)
```

使用`kernprof -l -v compare.py` 进行调用显示profile，执行结果如下，Time列的单位是微秒us：
```
Line #      Hits         Time  Per Hit   % Time  Line Contents
    12     10000       7408.0      0.7    100.0      'param 1: %s, param2: %s' %(p1, p2)
    17     10000       6769.0      0.7    100.0      f'param 1:{p1}, param2: {p2}'
    22     10000      11052.0      1.1    100.0      'param1: {}, param2: {}'.format(p1, p2)

```

从上面的执行结果可以看出来，f-strings 最快，str.format()最慢，而printf-style 跟f-string是接近的，时间差别可以忽略不计。因此我们在python3.6 以上的版本默认使用f-string，f-string可读性更高一点。


### logging
对于logging，我们使用不可插值的c-string， 把字符串当成参数传入logging 函数，比如：
`logger.debug('param 1 %s', p1)`

这种情况下，logger的展开是惰性插值的（lazy interpolation），即如果你的log level没有被启用，字符串是不会进行插值处理的，这样能省很多时间，因为字符串的插值处理需要寻找到目标字符串的位置并且插入是比较耗费时间的。

我们继续使用一段程序进行性能比对：

```
#!/usr/bin/python  
  
import line_profiler  
import atexit  
import logging  
  
profile = line_profiler.LineProfiler()  
atexit.register(profile.print_stats)  
  
  
class Handler(logging.Handler):  
    def emit(self, rec):  
        self.format(rec)  
  
  
logging.basicConfig(handlers=[Handler(level=logging.ERROR)])  
logger = logging.getLogger()  
  
  
@profile  
def printf_style_debug(p1, p2):  
    logger.debug('param 1: %s, param2: %s', p1, p2)  
  
  
@profile  
def f_string_style_debug(p1, p2):  
    logger.debug(f'param 1:{p1}, param2: {p2}')  
  
  
@profile  
def printf_style_error(p1, p2):  
    logger.error('param 1: %s, param2: %s',p1, p2)  
  
  
@profile  
def f_string_style_error(p1, p2):  
    logger.error(f'param 1:{p1}, param2: {p2}')  
  
  
p1 = "hi, this is p1"  
p2 = Exception("hi, this is exception")  
  
for i in range(10000):  
    printf_style_debug(p1, p2)  
    f_string_style_debug(p1, p2)  
    printf_style_error(p1, p2)  
    f_string_style_error(p1, p2)
```


使用`kernprof -l -v loggercompare.py` 进行调用并打印profile结果如下，Time列的单位是微秒us：
```
Line #      Hits         Time  Per Hit   % Time  Line Contents
    22     10000      18870.0      1.9    100.0      logger.debug('param 1: %s, param2: %s', p1, p2)
    27     10000      20943.0      2.1    100.0      logger.debug(f'param 1:{p1}, param2: {p2}')
    32     10000     551198.0     55.1    100.0      logger.error('param 1: %s, param2: %s',p1, p2)
    37     10000     556495.0     55.6    100.0      logger.error(f'param 1:{p1}, param2: {p2}')
```
可以看到，在debug没有开的情况下，printf-style 比f-string要快10%。

另外如果logging过程出现了exception，使用 uninterplated printf-style 的logging方式可以让logging 库捕获此类exception，而f-strings则直接抛出异常。

### templating

看到这里，你会想str.format() 又慢，可读性又差，是不是就没有用了？ 答案是str.format()用来进行templating比起f-strings是个不错的选择。

```
#!/usr/bin/python  
  
  
def iter_name():  
    info = [  
        ["rui","18", "NJ"],  
  ["ray", "19", "NY"],  
  ["eric", "20", "china"],  
  ]  
  
    for i in info:  
        yield {'name': i[0], 'age': i[1], 'loc': i[2]}  
  
  
def print_info(tmp):  
    for name in iter_name():  
        print(tmp.format(**name))  
  
  
print_info("hi, my name is {name}, I'm {age}, my location is {loc}")
```

执行结果：
```
hi, my name is rui, I'm 18, my location is NJ
hi, my name is ray, I'm 19, my location is NY
hi, my name is eric, I'm 20, my location is china
```
而f-string中变量在定义了以后即需要被替换求值，相比f-string和c-string，str.format() 用来做templating更加方便。











