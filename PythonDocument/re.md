# Python.re

### 1 正则表达式基础  
#### 1.1 Python支持的正则表达式元字符和语法：  
![](https://github.com/liuii/DiaryOfLiuII/blob/master/PythonDocument/re01.png?raw=true)  
  
#### 1.2 反斜杠`\`的处理
可以通过使用Python原生字符串来解决问题：`r'\\'`或`r'(\w+)\s`。  

### 2 `re`模块的使用  
#### 2.1 开始使用re  
一般的步骤是先将正则表达式的字符串形式编译为`Pattern`实例，然后用`Pattern`实例处理文本并获得匹配的结果，最后使用`Match`实例获得信息。  
``` Python
import re

# 将正则表达式编译成Pattern对象
pattern = re.compile(r'hello')

# 使用Pattern匹配文本，获得匹配结果，无法匹配时将返回None
match = pattern.match('hello world!')

if match:
    # 使用Match获得分组信息
    print(match.group())
    
### 输出 ###
# hello
```
另外`re`提供了模块方法，上述代码可以直接写成：
``` Python
import re

# 使用模块方法匹配文本，获得匹配结果，无法匹配时将返回None
match = re.match(r'hello', 'hello world!')

if match:
    # 使用Match获得分组信息
    print(match.group())

### 输出 ###
# hello
```

**re.compile(strPattern[, flag])**  
`flag`参数是匹配模式，取值可以用按位或运算符`|`表示同时生效，例如`re.I | re.M`。同时也可以在`regex`字符串中指定模式，例如下面的两条语句就是等价的：  
``` Python
re.compile('pattern', re.I | re.M)
re.compile('(?im)pattern')
```
`flag`可选的值包括：  
- re.I(re.IGNORECASE): 忽略大小写（括号内是完整写法，下同）  
- re.M(MULTILINE): 多行模式，改变'^'和'$'的行为（参见上图）  
- re.S(DOTALL): 点任意匹配模式，改变'.'的行为  
- re.L(LOCALE): 使预定字符类 \w \W \b \B \s \S 取决于当前区域设定  
- re.U(UNICODE): 使预定字符类 \w \W \b \B \s \S \d \D 取决于unicode定义的字符属性  
- re.X(VERBOSE): 详细模式。这个模式下正则表达式可以是多行，忽略空白字符，并可以加入注释。  
`re`模块还提供了一个方法`escape(string)`，用于将`string`中的正则表达式元字符之前加上转义符再返回。  

#### 2.2 Match  
`Match`对象是一次匹配的结果，包含了很多关于此次匹配的信息，可以使用`Match`提供的可读属性或方法来获取这些信息。  
1. **string**: 匹配时使用的文本。  
2. **re**: 匹配时使用的Pattern对象。  
3. **pos**: 文本中正则表达式开始搜索的索引。值与Pattern.match()和Pattern.seach()方法的同名参数相同。  
4. **endpos**: 文本中正则表达式结束搜索的索引。值与Pattern.match()和Pattern.seach()方法的同名参数相同。  
5. **lastindex**: 最后一个被捕获的分组在文本中的索引。如果没有被捕获的分组，将为None。  
6. **lastgroup**: 最后一个被捕获的分组的别名。如果这个分组没有别名或者没有被捕获的分组，将为None。  