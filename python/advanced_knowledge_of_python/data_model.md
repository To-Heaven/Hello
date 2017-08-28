## words in this message
a desk of 	一副  
respond		响应  
specific		具体的，明确的  
leverage		作用  
reinvent		重复创造  
delegate		代表  
automatically	自动  

## Magic and Pythonic
### A python card desk   ——《the fluent python》
- namedtuple
	- namedtuple can used to build classes of objects that are just bundles of attributes with no custom methods, like a databases record. 

- __getitem__
	- delegates to the [] opeartor. so , the object automatically support slicing
	- make the object iterable, reverse as well

```python

from collections import namedtuple
from random import shuffle

Card = namedtuple('Card', 'rank suit')

class CardDesk:
    ranks = [str(i) for i in range(2, 11)] + list('JQKA')
    suits = '黑桃 红桃 方块 梅花'.split()

    def __init__(self):
        # 组合
        self.__cards = [Card(rank, suit) for rank in CardDesk.ranks
                                        for suit in CardDesk.suits]

    def __len__(self):
        return len(self.__cards)

    def __repr__(self):
        return repr(self.__cards)

    def __getitem__(self, item):
        return self.__cards[item]

    def __bool__(self):
        return bool(len(self.__cards))

    def __setitem__(self, key, value):
        self.__cards[key] = value

    def __delitem__(self, key):
        del self.__cards[key]

card = CardDesk()
print(card[0])
print(card[-1])
print(card[5:10])

for i in card:
    print(i)
    
print(len(card))


print(card)
print(bool(card))

card[0] = Card(5, '梅花')
print(card)
del card[0]
print(card)
print(len(card))
```       

> implicit          隐性的    

### magic method
- Note:**special method is that they are meant to be called by the python interpreter not by you !!!** Don't write like  '  object.__getitem__()  ',  python calls the __getitem__ we implement
- our code should not have many direct calls to special methods.
	- we should be implementing special methods more often than invoking them explicitly

### emulating number types
- implement a class to represent two-dimensional vectors

```python
from math import hypot


class Vector:
    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y

    def __repr__(self):
        return 'Vector(%r, %r)' % (self.x, self.y)

    def __abs__(self):
        return hypot(self.x, self.y)

    def __bool__(self):
        return bool(abs(self))

    def __add__(self, other):
        x = self.x + other.x
        y = self.y + other.y
        return Vector(x, y)

    def __mul__(self, other):
        return Vector(self.x * other, self.y * other)

```

> inspection         检查
> magnitude        大小
> explicit                明确的清楚的


### string representation
- __repr__
	- called by the repr builtin to get the string representation of the object for inspection
	- if an object do not has a __repr__, it would be shown in the console like <classname  object at 0x5481...>
	- in __repr__ implementation, use   ‘ %r  ’   to obtain the standard representation of the attributes to be displayed.
	- the string returned by __repr__ should be unambiguous . match the source code necessary to re-create the object being represented
	- __repr__ and __Str__
		- __str__ is called by the print function , it should return a string suitable for display to end users
	- Note: **__repr__should be the better choice while we just want to implement one of these special methods**. because when no custom __str__ is available, python will call __repr_ as fallback


### bollean value of a custome type
- Note: by default , instances of user defined classes are considered truthy, unless either __bool__ or  __len__ is implemented.
- bool(x) calls x.__bool__() and uses  the result 
	- if __bool__ is not implemented, python tries to invoke x.__len__(), and if __len__() return zero, bool return False, otherwise True

