
# ES6 class

---

### What is Class?

- 類別(class)：設計藍圖
- 物件(object)：實際蓋好的房子
- 兩者關係：設計藍圖(類別)決定房子應該怎麼蓋，決定幾台電梯、幾間房間、走道如何設計。實際蓋好的房子(物件)是照著設計藍圖所蓋出來的房子，人只能照設計藍圖的設計使用這間房子。 

[保哥的OOP-Basis-What-is-class-and-object](https://blog.miniasp.com/post/2009/08/27/OOP-Basis-What-is-class-and-object)


---


![](https://openhome.cc/Gossip/Java/images/Class-1.PNG)


---

### ES6 Class ?

- 一個語法糖
- 基於prototype實現
- 更簡潔的方式建立物件與處理繼承

---

### prototype chain ?
![](https://4.bp.blogspot.com/-fatzOLLqlGM/V2dXLiCs5RI/AAAAAAAAmwE/PLkLHJTmOkIiIz0ftJVdsdWmVhzJqgt8wCLcB/s640/1.png)

---


**ES6**
```javascript=
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    toString() {
        return '(' + this.name + ',' + this.age + ')';
    }
}
var p = new Person('Mia', 18);

```

---

**ES5**
```javascript=
function Person(name, age) {
    this.name = name;
    this.age = age;
}

Person.prototype.toString = function () {
    return '(' + this.name + ',' + this.age + ')';
}
var p = new Person('Mia', 18);
```

---

## ES6 class 解決了什麼問題?

- extends可以更優雅的寫繼承,而非使用
    - ``Object.create(..)`` 
    - ``.prototype `` 
    - ``__proto__`` 
    - `` Object.setPrototypeOf(..)``
- 簡潔的 super 達到相對多型的目的
- extends 擴充內建函數``Array RegExp ``
- class 語法只能指定方法，不能設定屬性，這避免開發者誤將屬性（狀態或資料）放在類別中造成的共用問題


---

# **ES6 class的坑**

---


定義一個``class``
```javascript=
class C {
    constructor() {
        this.num = Math.random();
    }
    rand() {
        console.log( "Random: " + this.num );
    }
}

```

---

透過``prototype``改變了``class``裡面的方法
```javascript=

var c1 = new C();
c1.rand();  "Random: 0.4324299..."

//覆寫..
C.prototype.rand = function() {
	console.log( "Random: " + Math.round( this.num * 0 ));
};

var c2 = new C();
c2.rand();  "Random:0"
c1.rand();  "Random:0" 

```


---

定義一個``class``並透過``prototype``設定``count++``

```javascript=
class C {
	constructor() {
		C.prototype.count++;
		//`this.count` 通過委託如我們期望的那樣工作
		console.log( "Hello: " + this.count );
	}
}

```

---

增加共享属性``count``

```javascript=
// 增加共享属性
C.prototype.count = 0;
var c1 = new C();
// Hello: 1

var c2 = new C();
// Hello: 2

c1.count === 2; // true
c1.count === c2.count; // true

```

---

分離的遮蔽屬性
```javascript=

class C {
	constructor(id) {
// 噢，一個坑，我們用實例上的屬性值遮蔽了`id()`方法
		this.id = id;
	}
	id() {
		console.log( "Id: " + this.id );
	}
}
var c1 = new C( "c1" );
c1.id(); // TypeError -- `c1.id`現在是字串"c1"

```

---

```javascript=
class P {
	foo() { console.log( "P.foo" ); }
}

class C extends P {
	foo() {
		super();
	}
}

var c1 = new C();
c1.foo(); // "P.foo"

var D = {
	foo: function() { console.log( "D.foo" ); }
};

var E = {
	foo: C.prototype.foo
};

// E 链接到 D 来进行委托
Object.setPrototypeOf( E, D );

E.foo(); // "P.foo"
```

---



---

## 建立子類別extends

---

#### 擴展Date

```javascript=
class myDate extends Date {
  constructor() {
    super();
  }

  getFormattedDate() {
    var months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'];
    return this.getDate() + '-' + months[this.getMonth()] + '-' + this.getFullYear();
  }
}
```


---

#### 擴展 null

```javascript=
class nullExtends extends null {
  constructor() {}
}

Object.getPrototypeOf(nullExtends); // Function.prototype
Object.getPrototypeOf(nullExtends.prototype) // null

new nullExtends(); //ReferenceError: this is not defined

```

---

## super

---

Using super in classes
```javascript=
class Banner extends Component {
 constructor(props) {
     // ReferenceError，使用this前super需要先被调用！
     //這裡呼叫super()是調用父類的constructor(props);
     this.something;
 }
};
```

---

Super-calling static methods
```javascript=
class Human {
  constructor() {}
  static ping() {
    return 'ping';
  }
}
class Computer extends Human {
  constructor() {}
  static pingpong() {
    return super.ping() + ' pong';
  }
}
Computer.pingpong(); // 'ping pong'
```

---

### React 的class component?
```javascript=
class Banner extends Component {
  state = {
    openAtStart: true,
    autoToggle: false,
    transition: true,
    transitionClass: "",
  };
   static defaultProps = {
    openAtStart: true, // [boolean] true | false
    autoToggle: false, // [boolean|number] true | false | 3000
    transition: true,
    whenTransition:function(){
      console.log('Transition!!!!')
    }
 };
```

---

## 沒有constructor && super ?

```javascript=
class Banner extends Component {
  stste={
    openAtStart: true,
    autoToggle: false,
    transition: true,
    transitionClass: "",
    classText: "",
    currentClass: OPENED,
     imgClass:null,
    whenTransition: function () {
      console.log(' default callback !!!! ')
    },
  }
}
```

---

其實是babel幫你加了
根據[ ES.next 類提案](https://github.com/tc39/proposal-class-fields)
```javascript=
class Banner extends Component {
  constructor(...args) {
    super(...args);
    _defineProperty(this, "stste", {
      openAtStart: true,
      autoToggle: false,
      transition: true,
      transitionClass: "",
  }
}
```

---

## static ?
```javascript=
class say {
    static hello(name){
        console.log('hello',name)
    }
}
say.hello('Q_Q') // hello Q_Q
//如果沒有static
say.hello('Q_Q') // say.hello is not a function
```

---

**不會被實例繼承，而是直接通過類來調用**
![](https://i.imgur.com/H17XE1D.png)

---

**曾經用class集中管理api**
```javascript=
export default class ProductAPI {
    static getProduct(page = 1){
        return  axios({
            method:'GET',
            url:`${apiUrl}/api/${dbName}/products/?page=${page}`,
        })
    }
    
    static addProduct(){

    }
}
```

---

如何使用?
```javascript=
import ProductAPI from '../../api/API-Product';

    getProduct = (e) => {
        ProductAPI.getProduct().then((response)=>{
            this.setState({
                productArray:response.data.products
            })
        })
    }
```

---


## 參考資料

- [MDN](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Classes/extends)
- [Summer](https://cythilya.github.io/2018/10/28/es6-class/)
- [pjchender](https://pjchender.blogspot.com/2016/07/javascript-es6classes.html)
- [You-Dont-Know-JS](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/apA.md)
- [overreacted](https://overreacted.io/zh-hant/why-do-we-write-super-props/)
[The constructor is dead, long live the constructor!](https://hackernoon.com/the-constructor-is-dead-long-live-the-constructor-c10871bea599)

---

### Thank you! :sheep: 

You can find me on

- GitHub
- or email me
- Office