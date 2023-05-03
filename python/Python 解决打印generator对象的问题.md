### **Python 解决打印generator对象的问题**

#### 打印generator数组（列表）中的内容（python3）

循环中不适用数组定义封装而直接用函数调用，（…）会使用元组，则会出现generator对象

```python
 def sentence_to_id(self, sentence):
        word_ids = (self.word_to_id(cur_word) for cur_word in sentence.split())
        return word_ids
```

如果直接打印generator对象的话，会出现类似

```python
<generator object Vocab.sentence_to_id.. at 0x00000155F4948CA8>
```

试试使用`print(word_ids[0]);`
则会出现 TypeError: ‘generator’ object is not subscriptable
最后，将此对象转换成list列表

```python
    def sentence_to_id(self, sentence):
        word_ids = (self.word_to_id(cur_word) for cur_word in sentence.split())
        word_ids = list(word_ids)
        return word_ids

```

成功~~~