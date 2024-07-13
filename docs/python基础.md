# python不完整的基础

与或非优先级

![image-20240713130402194](python%E5%9F%BA%E7%A1%80.assets/image-20240713130402194.png)![image-20240713130426083](python%E5%9F%BA%E7%A1%80.assets/image-20240713130426083.png)



### 列表

```python

shopping_list = []
shopping_list.append("显示器")   #往列表添加值
print(shopping_list)
```

列表可以放不同类型的数据（**同许多语言不一样**），**自由灵活**

```python
list1 =['Hello"]
list1.append(66.6)
list1.append(True)
list1.append(None)
print(list1)
#["Hello"，66.6，True, None]
```

**len**()获取列表长度

l利用索引来获得某个位置的元素

```python
lisit1[1]
```

##### 列表相关函数

```python
price =[799，1024，200，800]
max_price = max(price)   #获取列表最大值
min_price = min(price)
sorted_price = sorted(price)			#返回一个排序好的列表，不改变原来的列表
print(max_price)
print(min_price)
print(sorted_price)
```

## 字典

键值对

![image-20240713131508482](python%E5%9F%BA%E7%A1%80.assets/image-20240713131508482.png)

```python
contacts = {"小明":"13700000000" , "小花":"13700000001"}  

```

获取某个健的值

```python
contacts["小明"]
```



##### 补充：

列表属于可变类型，**不能作为键**

![image-20240713131901300](python%E5%9F%BA%E7%A1%80.assets/image-20240713131901300.png<img src="python%E5%9F%BA%E7%A1%80.assets/image-20240713132137885.png" alt="image-20240713132137885" style="zoom:50%;" />

**元组**：与列表的区别是元组使用圆括号。**元组可作为字典的键**

元组不可变，**添加删除等操作通通不可使用**

```python
example_tuple = ("键盘"，键帽)
```





##### 字典是可变的：

往字典中添加新的键值对，**更新值的操作同样如此**

```python
contacts["小丽"]  ="12345678901"
```

如果想知道某个键是否存在

```python
"小明" in contacts
#存在返回True  （注意在python中布尔类型是大写）
```

删除键值对

```python
del contacts["小明"]
len(contacts)  #查询字典中有多少键值对
```

## 迭代

![image-20240713133040007](python%E5%9F%BA%E7%A1%80.assets/image-20240713133040007.png)

**对列表、字典、字符串进行迭代**：for循坏下的语句记得要带上缩进！！！！



##### 字典对应的方法

![image-20240713133316674](python%E5%9F%BA%E7%A1%80.assets/image-20240713133316674.png)

```python
temperature_dict = {"111":36.4,"112":36.7,"113":38.1}
for staff_id,temperature in temperature_dict.itens():   
    #.items()返回所有键值对，分别将单个键与值赋给staff_id和temperature
    if temperature >= 38:
        print(staff_id)
```



##### range函数

这是一个自然数列，**结束值不包含在内**

<img src="python%E5%9F%BA%E7%A1%80.assets/image-20240713134130214.png" alt="image-20240713134130214" style="zoom:50%;" />

或，第三个参数为步长，如果不指定默认为1

<img src="python%E5%9F%BA%E7%A1%80.assets/image-20240713134256900.png" alt="image-20240713134256900" style="zoom:50%;" />

## while循环

![image-20240713135018412](python%E5%9F%BA%E7%A1%80.assets/image-20240713135018412.png)

## format方法

<img src="python%E5%9F%BA%E7%A1%80.assets/image-20240713135157652.png" alt="image-20240713135157652" style="zoom:50%;" />

<img src="python%E5%9F%BA%E7%A1%80.assets/image-20240713135333991.png" alt="image-20240713135333991" style="zoom:50%;" />

<img src="python%E5%9F%BA%E7%A1%80.assets/image-20240713135247955.png" alt="image-20240713135247955" style="zoom:50%;" />

## 定义函数

![image-20240713135542735](python%E5%9F%BA%E7%A1%80.assets/image-20240713135542735.png)

![image-20240713140000962](python%E5%9F%BA%E7%A1%80.assets/image-20240713140000962.png)

## 引入模块

**第三种不推荐**

第三方的模块引用前需要安装

![image-20240713140347567](python%E5%9F%BA%E7%A1%80.assets/image-20240713140347567.png)

## 类

<img src="python%E5%9F%BA%E7%A1%80.assets/image-20240713141047910.png" alt="image-20240713141047910" style="zoom:50%;" />

```python
class 类名:
    #定义的代码
    def _init_(self):
        #构造函数，可以传入多个参数，但第一个必须数self表示占位
    
    def speak():
        #方法，注意要有缩进，表示在该类种
```

<img src="python%E5%9F%BA%E7%A1%80.assets/image-20240713141634977.png" alt="image-20240713141634977" style="zoom:33%;" />

##### 继承

在类的属性里写明父类，在构造方法中使用super关键字获取并赋值从父类继承的方法

![image-20240713142559212](python%E5%9F%BA%E7%A1%80.assets/image-20240713142559212.png)

## 读文件

读文件前用open()打开文件，结束需用close()关闭文件

![image-20240713143208604](python%E5%9F%BA%E7%A1%80.assets/image-20240713143208604.png)

如果不想open()和close()可使用下面的方法，**将会在文件读取完毕后自动关闭:**

![image-20240713143407402](python%E5%9F%BA%E7%A1%80.assets/image-20240713143407402.png)

## 捕捉异常

![image-20240713144331773](python%E5%9F%BA%E7%A1%80.assets/image-20240713144331773.png)