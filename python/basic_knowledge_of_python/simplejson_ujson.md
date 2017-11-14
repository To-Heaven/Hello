# simplejson 
#### 使用
- simplejson是python的第三方模块，提供更快的序列化和反序列化速度，simplejson模块提供了和json模块相同的功能调用接口，如果你有强迫症，这你可这样使用它

```python
>>> import simplejson as json
>>> d = {
... 'name': 'ziawang',
... 'age': 23
... }
>>> d_json = json.dumps(d)
>>> d_json
'{"name": "ziawang", "age": 23}'
>>> new_d = json.loads(d_json)
>>> new_d
{'name': 'ziawang', 'age': 23}
>>> id(d)
61035264
>>> id(new_d)
6164849

```


# ujson
- 如果你想为你的python解释器添加ujson包，你的计算机中必须要有`Microsoft Visual C++ 14.0`环境