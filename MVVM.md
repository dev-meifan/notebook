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
        home: 'sz'
    }
};

Object.entries(data).forEach(([key, value]) => {

    // set操作只能对临时变量进行赋值，避免直接更新原数据导致堆栈溢出
    let currentValue = data[key];

    Object.defineProperty(data, key, {
        enumerable: true,
        configurable: true,
        get () {
            console.log('get:', value);
            return value;
        },
        set (newValue) {
            currentValue = newValue;
            console.log('set:', newValue);
        }
    });
});
```

这里有一个问题，就是利用Object.defineProperty做数据劫持只能一层层绑定。
