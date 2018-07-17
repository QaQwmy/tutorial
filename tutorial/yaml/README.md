庞大的json数据不好辨认，一款简洁的非标记语言诞生了，它就是yaml

大家可以在[这里](http://nodeca.github.io/js-yaml/)实验yaml语法

基本规则：

1. 大小写敏感
2. 使用缩进表示层级关系
3. 禁止使用tab键进行缩进
4. 缩进长度无限制，只要同级对齐即可
5. 使用# 来添加注释
6. 字符串可以不用引号标注

数据结构：

## 1. map  散列表（字典）

#### 注意：冒号后面要有空格隔开
```
name: 张三
age: 24

# 对应的json格式为：
{'name':'张三','age':24}


也可写成一行：
{name: 张三,age: 24}
```

### 1.1. map嵌套map

```
person:
    name: 张三
    age: 24
    
对应的json格式为：
{ person: { name: '张三', age: 24 } }
```

## 1.2. map嵌套list

```
language:
    - python
    - JavaScript
    - Java
    
对应的json格式为：
{ language: [ 'python', 'JavaScript', 'Java' ] }
```


## 2. list 数组

### 注意：-号后面也用空格隔开

```
- a
- b
- c

# 对应的json格式为：
['a','b','c']

也可写成一行：
[a,b,c]
```

### 2.1. list嵌套list

```
-
  - python
  - javascript
  - java
- 
  - c++
  - c
  - R
  
  
对应的json格式为：
[ [ 'python', 'javascript', 'java' ], [ 'c++', 'c', 'R' ] ]
```

## 2.2. list嵌套map

```
- 
  - 
  person:
      name: 张三
      age: 24
      
- 
  - 哈哈
- 
  - aa
  
对应的json格式为：

[ { person: { name: '张三', age: 24 } }, [ '哈哈' ], [ 'aa' ] ]
```

#### 这里注意一下：要想列表与字典同级必须写成同级列表下的一个列表如上个例子

```
- 
  person:
      name: 张三
      age: 24
  - 哈哈
  - a
  
 这样写格式是不对的
```

## 3. scalar 纯量

```
boolean: 
    - TRUE  #true,True都可以
    - FALSE  #false，False都可以
float:
    - 3.14
    - 6.8523015e+5  #可以使用科学计数法
int:
    - 123
    - 0b1010_0111_0100_1010_1110    #二进制表示
null:
    nodeName: 'node'
    parent: ~  #使用~表示null
string:
    - 哈哈
    - 'Hello world'  #可以使用双引号或者单引号包裹特殊字符
    - newline
      newline2    #字符串可以拆成多行，每一行会被转化成一个空格
date:
    - 2018-02-17    #日期必须使用ISO 8601格式，即yyyy-MM-dd
datetime: 
    -  2018-02-17T15:02:31+08:00    #时间使用ISO 8601格式，时间和日期之间使用T连接，最后使用+代表时区
```
对应的json格式为：

```
{ boolean: [ true, false ],
  float: [ 3.14, 685230.15 ],
  int: [ 123, 685230 ],
  null: { nodeName: 'node', parent: null },
  string: [ '哈哈', 'Hello world', 'newline newline2' ],
  date: [ Sat Feb 17 2018 08:00:00 GMT+0800 (中国标准时间) ],
  datetime: [ Sat Feb 17 2018 15:02:31 GMT+0800 (中国标准时间) ] }
```



## 4. 