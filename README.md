#### 箭头函数不适合哪些场景？

1. 不可以用在构造函数原型方法上
```js
Object.prototype = () => {
    // 这里构造函数的原型方法需要通过this获取对象实例
}

const person = {
    name: 'mary',
    getName: () => {
        // 这里this指向window
        console.log(this,name);
    }
};

person.getName();
```

2. 不适用于动态回调场景
```js
const btn = document.getElementById('btn');

btn.addEventListentener('click', () => {
    // 通常这里需要获取btn实例
    console.log(this === window);
});
```

#### Symbol(TODO)

```js
Symbol.for()
Symbol.keyFor()
Symbol.asyncIterator()
Symbol.hasInstance()
...
```