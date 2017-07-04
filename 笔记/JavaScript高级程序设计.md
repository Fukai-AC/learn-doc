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
直接添加属性到构造函数的prototype上，每个实例都会包含这些属性，而且共享的是一个属性。会产生一种情况（修改了原型上的属性值，所有实例都会改变）：
  ```javascript
  function People() {}
  People.prototype.name = 'hello';
  People.prototype.age = 1;
  People.prototype.show_name = function() {
    console.log(this.name)
  }
  let people1 = new People();
  let people2 = new People();

  function Person(name, gender) {
    Person.prototype.name = name;
    Person.prototype.gendar = gendar;
    Person.prototype.show_name = function {
      console.log(this.name);
    }
  }
  let person1 = new Person('小明', 10);
  let person2 = new Person('小红', 11);
  person1.name // 小红
  person2.name // 小红
  person1.show_name() //小红
  ```
  1.原型对象  
  当创建一个函数的时候，会为该函数创建一个constructor的属性，这个属性指向了该函数的原型对象。当调用该函数创建新的实例的时候，则该新实例的一个属性[[Prototype]]作为一个指针指向了该函数的原型对象，所以说，实例指向的是构造函数的原型对象而不是该构造函数。所以新的实例是不含prototype属性的，而且无法获取到[[Prototype]]，在Es5中增加了Object.getPrototypeof()方法可以返回[[Prototype]]的值即该对象的原型。  
  当读取对象的属性时会先查找该对象是否有该属性，找到的话就返回该属性，没有的话会再去找该对象的原型对象中的属性。   
  当修改实例中的属性时将会给该实例创建一个新的属性，而不会修改原型对象的属性，可通过delete删除该实例上的属性。   
  使用hasOwnProperty()函数可判断属性是否是当前对象的属性，true表示是实例属性，false表示是原型属性。

  ```javascript
  function People() {}
  People.prototype.name = 'hello';
  People.prototype.age = 1;
  People.prototype.show_name = function() {
    console.log(this.name)
  }
  let people1 = new People();
  let people2 = new People();
  people1.name = 'nihao';
  people1.name; // nihao
  people2.name; // hello
  people1.hasOwnProperty('name'); // true
  people2.hasOwnProperty('name'); // false
  delete people1.name;
  people1.name // hello
  people1.hasOwnProperty('name'); // false
  ```
  2.in操作符  
    单独使用in操作符时，只要属性存在该对象，不论是实例属性还是原型属性都会返回true。!(object.hasOwnProperty('name') && 'name' in object)该表达式可判断属性是否是原型属性。  
    for-in循环时会返回能够通过对象访问的可枚举的属性，不论该属性存在于对象属性还是实例属性中。  
    Object.keys()会返回该实例的可枚举的实例属性而不返回原型属性。