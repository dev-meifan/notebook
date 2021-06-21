#### jquey中的$符号是方法还是对象？

```js
const node = $('p');
node.addClass('desc');
```

从上面来看，我们可能会觉得$是一个方法；但是，jquery还可以像下面那样调用ajax。

```js
$.ajax();
```

那它到底是个方法还是一个类呢？

```js
var jQuery = (function () {
    var $;

    // ...

    $ = function (selector, context) {
        return function (selector, context) {
            var dom = [];

            dom.__proto__ = $.fn;

            // ...

            return dom;
        }
    };

    $.fn = {
        addClass: function () {}
    };

    $.ajax = function () {};

    return $;
})();
```

以上是jquery源码架构；按照面向对象编程的思想，其实是比较好理解和实现的，例如我们用ES class 实现：

```js
class $ {
    constructor (selector, context) {
        this.selector = selector;
        this.context = context;
        // ...
    }

    // 定义静态方法，直接调用
    static ajax () {}

    // 定义原型方法
    addClass () {}

    // ...
}

```

这样就比较好理解了，面向对象的设计也比较巧妙；