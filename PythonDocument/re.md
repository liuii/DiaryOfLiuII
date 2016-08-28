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
- **re.I(re.IGNORECASE):**忽略大小写（括号内是完整写法，下同）  
- **re.M(MULTILINE):** 多行模式，改变'^'和'$'的行为（参见上图）  
- **re.S(DOTALL):** 点任意匹配模式，改变'.'的行为  
- **re.L(LOCALE):** 使预定字符类 \w \W \b \B \s \S 取决于当前区域设定  
- **re.U(UNICODE):** 使预定字符类 \w \W \b \B \s \S \d \D 取决于unicode定义的字符属性  
- **re.X(VERBOSE):** 详细模式。这个模式下正则表达式可以是多行，忽略空白字符，并可以加入注释。  
`re`模块还提供了一个方法`escape(string)`，用于将`string`中的正则表达式元字符之前加上转义符再返回。  

#### 2.2 Match  
`Match`对象是一次匹配的结果，包含了很多关于此次匹配的信息，可以使用`Match`提供的可读属性或方法来获取这些信息。  
**【属性】**  
1. **string**: 匹配时使用的文本。  
2. **re**: 匹配时使用的Pattern对象。  
3. **pos**: 文本中正则表达式开始搜索的索引。值与Pattern.match()和Pattern.seach()方法的同名参数相同。  
4. **endpos**: 文本中正则表达式结束搜索的索引。值与Pattern.match()和Pattern.seach()方法的同名参数相同。  
5. **lastindex**: 最后一个被捕获的分组在文本中的索引。如果没有被捕获的分组，将为None。  
6. **lastgroup**: 最后一个被捕获的分组的别名。如果这个分组没有别名或者没有被捕获的分组，将为None。  
**【方法】**  
1. **group([group1, …]):** 获得一个或多个分组截获的字符串；指定多个参数时将以元组形式返回。`group1`可以使用编号也可以使用别名；编号`0`代表整个匹配的子串；不填写参数时，返回`group(0)`；没有截获字符串的组返回`None`；截获了多次的组返回最后一次截获的子串。  
2. **groups([default]):** 以元组形式返回全部分组截获的字符串。相当于调用`group(1,2,…last)`。`default`表示没有截获字符串的组以这个值替代，默认为`None`。  
3. **groupdict([default]):** 返回以有别名的组的别名为键、以该组截获的子串为值的字典，没有别名的组不包含在内。`default`含义同上。  
4. **start([group]):** 返回指定的组截获的子串在`string`中的起始索引（子串第一个字符的索引）。`group`默认值为`0`。  
5. **end([group]):** 返回指定的组截获的子串在string中的结束索引（子串最后一个字符的索引+1）。`group`默认值为`0`。  
6. **span([group]):** 返回`(start(group), end(group))`。  
7. **expand(template):** 将匹配到的分组代入`template`中然后返回。template中可以使用`\id`或`\g<id>`、`\g<name>`引用分组，但不能使用编号**0**。`\id`与`\g<id>`是等价的；但`\10`将被认为是第`10`个分组，如果你想表达`\1`之后是字符`'0'`，只能使用`\g<1>0`。  
``` Python
import re
m = re.match(r'(\w+) (\w+)(?P<sign>.*)', 'hello world!')

print("m.string:", m.string)
print("m.re:", m.re)
print("m.pos:", m.pos)
print("m.endpos:", m.endpos)
print("m.lastindex:", m.lastindex)
print("m.lastgroup:", m.lastgroup)
 
print("m.group(1,2):", m.group(1, 2))
print("m.groups():", m.groups())
print("m.groupdict():", m.groupdict())
print("m.start(2):", m.start(0))
print("m.end(2):", m.end(2))
print("m.span(2):", m.span(2))
print(r"m.expand(r'\2 \1\3'):", m.expand(r'\2 \1\3'))
```

#### 2.3 Pattern  
`Pattern`对象是一个编译好的正则表达式，通过`Pattern`提供的一系列方法可以对文本进行匹配查找。  
`Pattern`不能直接实例化，必须使用`re.compile()`进行构造。  
`Pattern`的可读**属性**：  
1. **pattern:** 编译时用的表达式字符串。  
2. **flags:** 编译时用的匹配模式。数字形式。  
3. **groups:** 表达式中分组的数量。  
4. **groupindex:** 以表达式中有别名的组的别名为键、以该组对应的编号为值的字典，没有别名的组不包含在内。  
``` Python
import re
p = re.compile(r'(\w+) (\w+)(?P<sign>.*)', re.DOTALL)

print("p.pattern:", p.pattern)
print("p.flags:", p.flags)
print("p.groups:", p.groups)
print("p.groupindex:", p.groupindex)
```

#### 2.4 search  
`search(string[, pos[, endpos]])`或`re.search(pattern, string[, flags])`  
这个方法用于查找字符串中可以匹配成功的子串。从`string`的`pos`下标处起尝试匹配`pattern`，如果`pattern`结束时仍可匹配，则返回一个`Match`对象；若无法匹配，则将`pos`加1后重新尝试匹配；直到`pos=endpos`时仍无法匹配则返回`None`。  
`pos`和`endpos`的默认值分别为`0`和`len(string))`；  
`re.search()`无法指定这两个参数，参数`flags`用于编译`pattern`时指定匹配模式。  
``` Python
import re

# 将正则表达式编译成Pattern对象
pattern = re.compile(r'world')

# 使用search()查找匹配的子串，不存在能匹配的子串时将返回None
# 这个例子中使用match()无法成功匹配
match = pattern.search('hello world!')

if match:
    # 使用Match获得分组信息
    print(match.group())
```

#### 2.5 split  
`split(string[, maxsplit])`或`re.split(pattern, string[, maxsplit])`  
按照能够匹配的子串将`string`分割后返回列表。`maxsplit`用于指定最大分割次数，不指定将全部分割。  
``` Python
import re
 
p = re.compile(r'\d+')
print(p.split('one1two2three3four4'))
```

#### 2.6 finditer
`finditer(string[, pos[, endpos]])`或`re.finditer(pattern, string[, flags])`  
搜索`string`，返回一个顺序访问每一个匹配结果（`Match对象`）的迭代器。  
``` Python
import re

p = re.compile(r'\d+')
for m in p.finditer('one1two2three3four4'):
    print(m.group(), end=' ')
```

#### 2.7 sub
`sub(repl, string[, count])`或`re.sub(pattern, repl, string[, count])`  
使用`repl`替换`string`中每一个匹配的子串后返回替换后的字符串。  
当`repl`是一个字符串时，可以使用`\id`或`\g<id>`、`\g<name>`引用分组，但不能使用编号0。  
当`repl`是一个方法时，这个方法应当只接受一个参数（`Match`对象），并返回一个字符串用于替换（返回的字符串中不能再引用分组）。  
`count`用于指定最多替换次数，不指定时全部替换。  
``` Python
import re

p = re.compile(r'(\w+) (\w+)')
s = 'i say, hello world!'
print(p.sub(r'\2 \1', s))

def func(m):
    return m.group(1).title() + ' ' + m.group(2).title()

print(p.sub(func, s))
```

#### 2.8 subn  
`subn(repl, string[, count])`或`re.sub(pattern, repl, string[, count])`  
返回元组(`sub(repl, string[, count])`, `替换次数`)  
``` Python
import re

p = re.compile(r'(\w+) (\w+)')
s = 'i say, hello world!'

print(p.subn(r'\2 \1', s))

def func(m):
    return m.group(1).title() + ' ' + m.group(2).title()

print(p.subn(func, s))
```
========  
参考资料：[Python正则表达式指南](http://www.cnblogs.com/huxi/archive/2010/07/04/1771073.html "Goto the reference page.")
