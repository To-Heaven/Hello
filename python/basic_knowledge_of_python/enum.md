# enum模块

- An enumeration is a set of names (members) bound to unique, constant values

## Two alternative ways to create enumeration
- using the subclass of Enum(pythonic)

```python
from enum import Enum


class Weekend(Enum):
    MONDAY = '星期一'
    TUESDAY = '星期二'
    WEDNESDAY = '星期三'
    THURSDAY = '星期四'
    FRIDAY = '星期五'
    SATURDAY = '星期六'
    SUNDAY = '星期天'
# -------------------------------------------------
"Weekend" is an enumeration(or a enum)
"Weekend.MONDAY" is the member of enumeration and 
 enum member has name and  value
member name  : Weekend.MONDAY.name
member value  : Weekeend.MONDAY.value  

for member in Weekend:
    print(member, 'value:', member.value )
```

	
- using the function syntex 

```python
Weekend = Enum('Weekend', 'MONDAY TUESDAY WEDNESDAY THURESDY FRIDAY SATUDAY SUNDAY')

print(Weekend.MONDAY)
print(Weekend['MONDAY'])
print(Weekend.MONDAY.value)

for member in Weekend:
    print(member, 'value:', member.value)
```



## Note
- enumeration members are hashable, so they canbe used in dictories and sets
- enumeration is iterable, use the loop to get all members
- having two enum members with the same name is invalid(raise TypeError)
- two enum members are allowed to have the same value
	- by default, enumerations allow multiple names as aliases for the same value
	- **but:**
		- the last member which defined last is an alias to the first one.
		- by-value and by-name lookup of the last member will return the first member 
- members of enumerations are compared by identify
- enumerations can be pickled 

## Operations for enumeration objects
- enum.__members__  
	- this variable of enum is a OrderedDict object coontain tuple of (member.name ,  member object)
	- `OrderedDict([('MONDAY', <Weekend.MONDAY: 1>) . . . ])`
	- 等价于 `enum._member_map`
- enum._member_names
	- this variable of enum is a list of all members' name 
- enum._value2member_map
	- a dict , keys are member's value, and the value is the member itself


```python
from enum import Enum, unique, auto

class MyAuto(Enum):
    def _generate_next_value_(name, start, count, last_values):
        return name

@unique
class Weekend(MyAuto):
    MONDAY = auto()
    TUESDAY = auto()
    WEDNESDAY = auto()
    THURSDAY = auto()
    FRIDAY = auto()
    SATURDAY = auto()
    SUNDAY = auto()

for name, member in Weekend.__members__.items():
    print(name, '  |  ',  member,  '  |  ', repr(member))
```


## Ways to get the member
1. enum(value)
2. enum[name]
3. use the attribute 
	- enum.name


## member attributes
- mem.value
- mem.name


## class decorator --- unique
- this is a decorator specifically for enumerations . 
- search any aliase in an enumeration's __members__, if aliase exists, raise ValueErrorwith the detail

```python
from enum import Enum, unique

@unique
class Weekend(Enum):
    MONDAY = '星期一'
    TUESDAY = '星期二'
    WEDNESDAY = '星期三'
    THURSDAY = '星期四'
    FRIDAY = '星期五'
    SATURDAY = '星期六'
    SUNDAY = '星期天'
    OTHERDAY = '星期一'		# 重复一个值
# -------------------------------------------------------------------
Traceback (most recent call last):
  File "D:/files/python/enum_pr.py", line 10, in <module>
    class Weekend(Enum):
  File "D:\Python36\lib\enum.py", line 830, in unique
    (enumeration, alias_details))
ValueError: duplicate values found in <enum 'Weekend'>: OTHERDAY -> MONDAY
```

## automatic value
- use `auto()` to set the member.value
	- The values are chosen by Enum._generate_next_value_()
	- **the default _generate_next_value_() methods is to provide the next int in sequence with the last int provided**	
		- defaulting to 1 as the starting number and not 0 is that 0 is False in a boolean sense, but enum members all evaluate to True

```python
from enum import Enum, unique

@unique
class Weekend(Enum):
    MONDAY = auto()
    TUESDAY = auto()
    WEDNESDAY = auto()
    THURSDAY = auto()
    FRIDAY = auto()
    SATURDAY = auto()
    SUNDAY = auto()
```

- overridden the method of `_generate_next_value_`

```python
from enum import Enum, unique, auto


class MyAuto(Enum):					# overridden the method , and provided the value of member 
    def _generate_next_value_(name, start, count, last_values):
        return name, count

@unique
class Weekend(MyAuto):				# Weekend must inherited from MyAuto
    MONDAY = auto()
    TUESDAY = auto()
    WEDNESDAY = auto()
    THURSDAY = auto()
    FRIDAY = auto()
    SATURDAY = auto()
    SUNDAY = auto()
```