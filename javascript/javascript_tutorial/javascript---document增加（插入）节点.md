## 增加节点
- 创建节点对象
	- `document.createElement(str_tag)`

## 添加子节点	
- 使用节点对象的appendChild方法为其添加节点
	- 子节点会被添加到children列表的最后
	- `par.appendChild(sub)`

## 修改节点位置
- 直接将节点对象作为参数传递给目标节点的appendChild方法即可

## 插入节点到指定位置
- 使用节点对象的`par.insertBefore(sub, ref)`方法
	- par是父节点对象
	- sub是要插入的节点对象
	- ref是sub节点对象的位置参考对象


## 替换标签
- `par.replaceChild(new_sub, old_sub)`
	- 通过父标签来替换目标标签

- 有以下HTML结构

```html
<p id="js">JavaScript</p>
<div id="list">
    <p id="java">Java</p>
    <p id="python">Python</p>
    <p id="scheme">Scheme</p>
</div>

```

- 操作如下

```javascript
var js = document.getElementById('js');
console.log(js);
var list = document.getElementById('list');
list.appendChild(js);
console.log(list);


var golang = document.createElement('p');
golang.id = 'golang';
golang.innerText = 'go';
list.appendChild(golang);


var d = document.createElement('style');
d.type = 'text/css';
d.innerHTML = 'p {color:red}';
document.getElementsByTagName('head')[0].appendChild(d);


var ziawang = document.createElement('h1');
ziawang.innerText = 'ziawang';
var ref = document.getElementById('list');
var par = document.getElementsByTagName('body')[0];
par.insertBefore(ziawang, ref);
```