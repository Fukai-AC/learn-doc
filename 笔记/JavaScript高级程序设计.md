## 第六章 面向对象的程序设计
  
### 6.1 理解对象
  对象是一个无序属性的集合，其属性可以是基本值，对象或者函数。

一.对象属性（分两类）  
1. 数据属性
>*  [Configurable] (default:true)可通过delete删除或是否可以修改属性特性  
>*  [Enumerable] (defaule:true)是否可通过for-in循环
>*  [Writable] (default:true)是否可修改属性值
>*  [Value] (default:undefined)属性值，读写都在这个位置

例如:  
```javascript
  let tmp_object = {};
  Object.defineProperty(tmp_object, 'tmp_prop', {
    writable: false,
    value: 'hello world',
  });
  tmp_object.tmp_prop = 'hello';
  console.log(tmp_object.tmp_prop); // 'hello world'
  // 修改了configurable为false后就不能通过Object.defineProperty修改任何属性
  Object.defineProperty(tmp_object, 'tmp_prop', {
    configurable: false,
  });
  delete tmp_object.tmp_prop; // false
```

2. 访问器属性（必须使用[Object.defineProperty](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)来定义）
>*  [Configurable] (default:true)可通过delete删除或能为修改属性特性  
>*  [Enumerable] (defaule:true)是否可通过for-in循环
>*  [Get] (default:undefined)读取属性时调用的函数
>*  [Set] (default:undefined)写入属性时调用的函数

例如：
```javascript
  // 实现对象修改的存档
function super_object() {
  let tmp = null;
  let operator_list = [];
  Object.defineProperty(this, 'tmp_prop', {
    get: () => {
      console.log('give me the value');
      return tmp;
    },
    set: (value) => {
      console.log('set tmp_prop value ' + value);
      operator_list.push({ set_value: value });
      tmp = value;
    }
  });
  this.get_operator_list = () => {
    return operator_list;
  }
}
let new_prop = new super_object();
new_prop.tmp_prop; // give me the value, null
new_prop.tmp_prop = 1; // set tmp_prop value 1
new_prop.tmp_prop = 2; // set tmp_prop value 2
new_prop.tmp_prop; // 2
new_prop.get_operator_list(); // [{set_value: 1}, {set_value: 2}]
```