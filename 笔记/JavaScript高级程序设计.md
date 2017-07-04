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

### 6.2 创建对象
  利用[Object的构造函数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object)或者对象[字面量](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Object_initializer)可以创建单个对象。当创建多个对象时使用一些设计模式将会更有用处。  
#### 一.工厂模式
  例如：
  ```javascript
  function create_people(name, gender) {
    let people = new Object();
    people.name = name;
    people.gender = gender;
    return people;
  }
  let people1 = create_people('people1', 'male');
  let people2 = create_people('people2', 'female');
  ```
#### 二.构造函数模式
  ```javascript
  function People(name, gender) {
    this.name = name;
    this.gender = gender;
    this.show_name = function() {
      console.log(this.name);
    };
  }
  let people1 = new People('people1', 'male');
  let people2 = new People('people2', 'female')
  ```
  new something(...);操作符执行时经过以下几步：  
  1.创建一个新的对象，继承someting的prototype.  
  2.构造函数something被执行，函数内的this指向这个新对象.  
  3.如果something返回了对象则对象取代这个新对象，若没有则使用这个新对象.  
  注意事项：  
  当使用构造函数实例,会给每个实例的方法，创建一个新的方法的实例，通常会将方法放在构造函数之外。
  ```javascript
  function People(name, gender) {
    this.name = name;
    this.gender = gender;
    this.show_name = show_name;
  }
  function show_name() {
    console.log(this.name);
  }
  ```
  由于这样会产生很多只属于某个构造函数的全局函数，产生很多冗余。  
#### 三.原型模式