> 总结 Python 中字典的常见使用方式。

## 1、检查key是否存在于字典
使用更具有 pythonic 的用法是使用 `in` 关键字来判断 key 是否存在于字典中。
```python
if key in dictionary:
    print True
```

## 2、使用setdefault初始化字典键值
使用字典的时候常会遇到这样的场景：动态更新字典，如果 key 不在字典中，那么就添加它并把它对应的值初始为空列表 `[]`，然后把元素 append 到空列表中。
```python
dictionary = {}
dictionary.setdefault("key", []).append("list_item")
```
字典调用 `setdefault` 方法时，首先检查 key 是否存在，如果存在该方法什么也不做，如果不存在 `setdefault` 就会创建该 key，并把第二个参数 `[]` 作为 key 对应的值。

## 3、使用defaultdict()初始化字典
初始化一个字典时，如果初始情况下希望所有 key 对应的值都是某个默认的初始值，比如有一批用户的信用积分都初始为 100，现在想给 a 用户增加 10 分。
```python
from collections import defaultdict
d = defaultdict(lambda: 100)
d['a'] += 10
```

## 4、高效合并字典
在 python3.5 中提供了一种全新的 pythonic 方法：
```python
x = {'a': 1, 'b': 2}
y = {'b': 3, 'c': 4}
z = {**x, **y}
```
不过考虑到大部分系统还是基于 python2，所以一种更兼容的 pythonic 方法是：
```python
z = x.copy()
z.update(y)

# 可以把它封装为一个函数
def merge_dicts(*dict_args):
    result = {}
    for dictionary in dict_args:
        result.update(dictionary)
    return result
z = merge_dicts(a, b, c, d, e, f, g) 
```
通过字典推导式的方法来合并字典：
```python
x = {'a': 1, 'b': 2}
y = {'b': 3, 'c': 4}
z = {k: v for d in dicts for k, v in d.items()}
```

性能对比：

* 直接使用 `{**x, **y}` 是最快的
* 使用 `update` 次之
* 使用字典推导式相对来说是最慢的


