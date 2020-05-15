 ## 基础类型
 ### 限定Object的value为固定类型
  ```typescript
  let a:{[s:string]:number};
  a = {
    a: 1,
    v: 2,
  };
  ```

  ## 变量声明
  
  ### let作用域（for)
  会为每个循环创建新的作用域
  ```typescript
  for (let i = 0; i < 10; i++) {
      setTimeout(function() { console.log(i); }, 100 * i);
  }
  output: 1 2 3 4 ...
  ```

  ###解构
  数组解构：
  ```typescript
  // 交换
  [a, b] = [b, a];
  // 剩余变量赋值
  let [a, ...b] = [1, 2, 3];
  // b = [2, 3];
  ```
  对象解构：
  ```typescript
  function f({a, b} = {a: '', b: ''}) {}
  f({a: 'a', b: 'b'});
  ```
  对象展开：
  ```typescript
  let a = {b:1, c:2, d:3};
  let b = {...a, b:10}; //{b: 10, c:2, d:3}

  class C {
    a = 1;
    f() {};
  }
  let c = new C();
  let b = {...c};
  b.a; // 1
  b.f(); // error
  ```

  ### 接口
  ## 函数类型
  ```typescript
  interface Func {
    (a:string, b:string):void;
  }
  let aFunc:Func;
  aFunc = (h, b) => {}; // h:string, b:string
  ```
  ##可索引的类型
  ```typescript
    interface StringArray {
      [index: number]: string;
    }
    let a:StringArray;
    a = ['']
    let b = a[1] // b:string
    
    let c:StringArray;
    c = {
      1: '',
    }
    c[1] //string
  ```
  ##类类型
  ```typescript
  interface ClockConstructor {
    new (): ClockInterface;
  }

  interface ClockInterface {
    a()
  }

  function createClock(ctor:ClockConstructor) {
    return new ctor();
  }
  class a implements ClockInterface {
    a() {};
  }
  createClock(a);
  ```

  ### 类
  ##protected
  构造函数也可以被标记成 protected。 这意味着这个类不能在包含它的类外被实例化，但是能被继承。
  ##readonly
  只读属性必须在声明时或构造函数里被初始化。
  ##存取器
  get,set方法: 只带有 get不带有 set的存取器自动被推断为 readonly。 这在从代码生成 .d.ts文件时是有帮助的，因为利用这个属性的用户会看到不允许够改变它的值。
  ```typescript
    class a {
      private _a:string;
      get a(): string {
        return this._a;
      }
      set a(new_a:string) {
        if (new_a !== '') {
          this._a = new_a;
        } else {
          console.log('error');
        }
      }
    }

    let b = new a();
    b.a = '2';
  ```