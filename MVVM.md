### MVVM响应式框架基本原理

直观上，视图会根据数据的变化进行自动更新，那完成这个过程框架层面做了哪些操作呢？

1. 依赖收集：收集视图依赖的数据
2. 数据劫持/数据代理：感知被视图依赖的数据的变化
3. 发布/订阅模式，更新视图：数据变化时通知视图进行更新

#### 数据劫持
- Vue2：Object.defineProperty

```js
let data = {
    name: 'mary',
    details: {
        age: 18,
        home: 'sz',
        children: ['hello', 'world']
    }
};

Object.keys(data).forEach(key => {

    // set操作只能对临时变量进行赋值，避免直接更新原数据导致堆栈溢出
    let currentValue = data[key];

    Object.defineProperty(data, key, {
        enumerable: true,
        configurable: true,
        get () {
            console.log('get:', currentValue);
            return currentValue;
        },
        set (newValue) {
            currentValue = newValue;
            console.log('set:', newValue);
        }
    });
});
```

这里有一个问题，就是利用Object.defineProperty做数据劫持只能一层层绑定。这里继续完善下，支持深层绑定。

```JS
function observe (data) {

    if (!data || typeof data !== 'object') {
        return;
    }

    Object.keys(data).forEach(key => {

        // set操作只能对临时变量进行赋值，避免直接更新原数据导致堆栈溢出
        let currentValue = data[key];

        observe(currentValue);

        Object.defineProperty(data, key, {
            enumerable: true,
            configurable: true,
            get () {
                console.log('get:', currentValue);
                return currentValue;
            },
            set (newValue) {
                currentValue = newValue;
                console.log('set:', newValue);
            }
        });
    });
}

observe(data);

data.details.children.push('javascript');
```

可以先猜猜当前的输出。。。

结果是：

```js
get: {
  age: [Getter/Setter],
  home: [Getter/Setter],
  children: [Getter/Setter]
}
get: [ [Getter/Setter], [Getter/Setter] ]
```

并未触发set操作，触发的两次get操作为data.details和data.details.children；至此会发现数组变化劫持不到，后面单独补充实现；

- Vue3：Proxy

#### 监听数组

```js
const arrExtend = Object.create(Array.prototype);

const arrMethods = [
    'pop',
    'shift',
    'unshift',
    'splice',
    'sort',
    'reverse',
    'push'
];

arrMethods.forEach(method => {
    const oldMethod = Array.prototype[method];
    const newMethod = function (...args) {
        oldMethod.apply(this, args);
        console.log(method + '被执行了');
    };

    arrExtend[method] = newMethod;
});

let arr = [1,2,3,4];

arr.__proto__ = arrExtend;

arr.push(5);

```

以上就是简单实现数组监听。
