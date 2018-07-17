�Ӵ��json���ݲ��ñ��ϣ�һ����ķǱ�����Ե����ˣ�������yaml

��ҿ�����[����](http://nodeca.github.io/js-yaml/)ʵ��yaml�﷨

��������

1. ��Сд����
2. ʹ��������ʾ�㼶��ϵ
3. ��ֹʹ��tab����������
4. �������������ƣ�ֻҪͬ�����뼴��
5. ʹ��# �����ע��
6. �ַ������Բ������ű�ע

���ݽṹ��

## 1. map  ɢ�б��ֵ䣩

#### ע�⣺ð�ź���Ҫ�пո����
```
name: ����
age: 24

# ��Ӧ��json��ʽΪ��
{'name':'����','age':24}


Ҳ��д��һ�У�
{name: ����,age: 24}
```

### 1.1. mapǶ��map

```
person:
    name: ����
    age: 24
    
��Ӧ��json��ʽΪ��
{ person: { name: '����', age: 24 } }
```

## 1.2. mapǶ��list

```
language:
    - python
    - JavaScript
    - Java
    
��Ӧ��json��ʽΪ��
{ language: [ 'python', 'JavaScript', 'Java' ] }
```


## 2. list ����

### ע�⣺-�ź���Ҳ�ÿո����

```
- a
- b
- c

# ��Ӧ��json��ʽΪ��
['a','b','c']

Ҳ��д��һ�У�
[a,b,c]
```

### 2.1. listǶ��list

```
-
  - python
  - javascript
  - java
- 
  - c++
  - c
  - R
  
  
��Ӧ��json��ʽΪ��
[ [ 'python', 'javascript', 'java' ], [ 'c++', 'c', 'R' ] ]
```

## 2.2. listǶ��map

```
- 
  - 
  person:
      name: ����
      age: 24
      
- 
  - ����
- 
  - aa
  
��Ӧ��json��ʽΪ��

[ { person: { name: '����', age: 24 } }, [ '����' ], [ 'aa' ] ]
```

#### ����ע��һ�£�Ҫ���б����ֵ�ͬ������д��ͬ���б��µ�һ���б����ϸ�����

```
- 
  person:
      name: ����
      age: 24
  - ����
  - a
  
 ����д��ʽ�ǲ��Ե�
```

## 3. scalar ����

```
boolean: 
    - TRUE  #true,True������
    - FALSE  #false��False������
float:
    - 3.14
    - 6.8523015e+5  #����ʹ�ÿ�ѧ������
int:
    - 123
    - 0b1010_0111_0100_1010_1110    #�����Ʊ�ʾ
null:
    nodeName: 'node'
    parent: ~  #ʹ��~��ʾnull
string:
    - ����
    - 'Hello world'  #����ʹ��˫���Ż��ߵ����Ű��������ַ�
    - newline
      newline2    #�ַ������Բ�ɶ��У�ÿһ�лᱻת����һ���ո�
date:
    - 2018-02-17    #���ڱ���ʹ��ISO 8601��ʽ����yyyy-MM-dd
datetime: 
    -  2018-02-17T15:02:31+08:00    #ʱ��ʹ��ISO 8601��ʽ��ʱ�������֮��ʹ��T���ӣ����ʹ��+����ʱ��
```
��Ӧ��json��ʽΪ��

```
{ boolean: [ true, false ],
  float: [ 3.14, 685230.15 ],
  int: [ 123, 685230 ],
  null: { nodeName: 'node', parent: null },
  string: [ '����', 'Hello world', 'newline newline2' ],
  date: [ Sat Feb 17 2018 08:00:00 GMT+0800 (�й���׼ʱ��) ],
  datetime: [ Sat Feb 17 2018 15:02:31 GMT+0800 (�й���׼ʱ��) ] }
```



## 4. 