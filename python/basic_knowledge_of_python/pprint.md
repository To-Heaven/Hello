# pprint 模块

## introduction
- this module provides a capability to “pretty-print” arbitrary Python data structures

## Note


```python
import pickle
import pprint


info = {
    'name':'ziawang',
    'phone':123456789101,
    'has girl friend':True,
    'bad experience':None
}

with open('info.pickle', 'wb') as wb:
    pickle.dump(info, wb)

with open('info.pickle', 'rb') as rb, \
    open('ziawang.txt', 'w') as w :
    info_load =pickle.load(rb)
    pprint.pprint(info_load, indent=4, stream=w)
------------------------------------------------------------------------------
# ziawang.txt
{   'bad experience': None,
    'has girl friend': True,
    'name': 'ziawang',
    'phone': 123456789101}


```