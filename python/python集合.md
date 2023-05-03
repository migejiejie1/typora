```shell
>>> x = set('abcde')
>>> y = set('bdxyz')

>>> x
set(['a', 'c', 'b', 'e', 'd'])                    # 2.6 display format

>>> 'e' in x                                      # Membership 成员
True

>>> x – y                                         # Difference 差集
set(['a', 'c', 'e'])


>>> x | y                                         # Union 并集
set(['a', 'c', 'b', 'e', 'd', 'y', 'x', 'z'])


>>> x & y                                         # Intersection 交集
set(['b', 'd'])


>>> x ^ y                                         # Symmetric difference (XOR) 补集
set(['a', 'c', 'e', 'y', 'x', 'z'])


>>> x > y, x < y                                  # Superset, subset  父级，子级
(False, False)

```

