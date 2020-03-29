# 面试准备

## 深拷贝 deepClone

```Javascript
function deepClone(target, hash = new WeakMap()) {
  // 不是对象或者是null 直接返回
  if (typeof target !== "object" || target == null) return target;
  // 通过WeakMap判断是否已有 有则返回对象引用
  if (hash.has(target)) return hash.get(target);
  // 判断是数组还是字符串
  const result = target instanceof Array ? [] : {};
  // 结果放进WeakMap里，key是target，值是result
  hash.set(target, result);
  // for in遍历
  for (const key in target) {
    // 避开原型链上的属性
    if (Object.prototype.hasOwnProperty.apply(target,key)) {
      // 递归调用
      result[key] = _deepClone(target[key]);
    }
  }
  // 返回结果
  return result;
}
```

## 防抖 debounce

```Javascript
function debounce(handler, wait = 300, immediate = false) {
  // 声明一个变量timer
  let timer;
  // 判断参数是否符合预期
  if (typeof handler !== "function") throw new Error("The first params shoule be a Function");
  if (typeof wait !== "number") throw new Error("The second params shoule be a Number");

  return function() {
    // 缓存上下文和参数
    const context = this,
      args = [...arguments];
    // 如果timer存在就清除timer
    timer && clearTimeout(timer);

    if (immediate) {
      // immediate为true且定时器不存在，则设置定时器,并在指定时间后timer设置为null
      const callNow = !timer;
      timer = setTimeout(() => {
        timer = null;
      }, wait);
      // 且立即执行一次handler,通过apply绑定上下文和传入参数
      callNow && handler.apply(context, args);
    } else {
      // immediate为false则设置定时器
      timer = setTimeout(() => {
        handler.apply(context, args);
      }, wait);
    }
  };
}
```

## 节流 throttle

```Javascript
function throttle(handler, wait = 300) {
  // 声明一个变量timer
  let timer;
  return function() {
    // timer存在则直接返回怎么也不干
    if (timer) return;
    // 缓存上下文和参数
    const context = this,
      args = [...arguments];
    // 设置定时器
    timer = setTimeout(() => {
      // 执行handler,通过apply绑定上下文和传入参数
      handler.apply(context, args);
      // timer设置为null
      timer = null;
    }, wait);
  };
}
```

## 是否是纯对象 isPlainObject

```Javascript
function isPlainObject(obj) {
  return Object.prototype.toString.call(obj) === "[object Object]";
}
```

## 深度对比 isEqual

```Javascript
function isEqual(target1, target2) {
  // 直接先对比，相等就返回
  if (target1 === target2) return true;

  const isPlainObject = obj => Object.prototype.toString.call(obj) === "[object Object]";
  //  如果两者任意一个不是纯对象，直接返回对比结果
  if (!isPlainObject(target1) || !isPlainObject(target2)) return target1 === target2;

  // 拿到两个keys的数组，长度不一致那肯定不一样
  const target1Keys = Object.keys(target1);
  const target2Keys = Object.keys(target2);
  if (target1Keys.length !== target2Keys.length) return false;

  // 以target1为基准遍历，递归调用isEqual对比每一项，任何一项不一样直接返回false
  for (const key in target1) {
    if (target1.hasOwnProperty(key)) {
      if (!isEqual(target1[key], target2[key])) return false;
    }
  }
  // 全一样
  return true;
}
```

## 拍平数组 flatArray

```Javascript
function flatArray(arr, depth = Infinity) {
  // 不是数组则报错
  if (!arr instanceof Array) throw new TypeError("Expect Array");
  // 判断是否支持ES10的flat方法
  if (Array.prototype.flat) {
    // 支持则直接调用
    return arr.flat(depth);
  } else {
    // 看数组内元素是否都是普通类型
    const isDeep = arr.some(item => Array.isArray(item));
    // 如果都是普通类型则直接返回
    if (!isDeep) return arr;
    // 用数组concat方法拍平数组第一层
    const result = [].concat(...arr);
    // 递归调用，拍平深层数组
    return flatArray(result);
  }
}
```

## 数组快排 quickSort

```Javascript
  function quickSort(target) {
    // 不是数组或者数组长度小于2，则直接返回
    if (Object.prototype.toString.apply(target) !== '[object Array]' || target.length < 2) return target
    // 拷贝一份数组
    const _arr = [...target]
    // 找到并取出基准元素
    const pivotItem = _arr.splice(Math.floor(_arr.length / 2), 1)
    // 定义两个临时数组，分别储存比基准小和比基准大的元素
    const smallerArr = []
    const biggerArr = []
    // 遍历数组，将元素分类
    for (let index = 0; index < _arr.length; index++) {
      const element = _arr[index]
      pivotItem > element ? smallerArr.push(element) : biggerArr.push(element)
    }
    // 将两个临时数组递归调用，并将两个数组和基准元素合并起来
    const result = this._quickSort(smallerArr).concat(pivotItem, this._quickSort(biggerArr))
    // 返回新数组
    return result
  },
```

## 数组冒泡排序 bubbleSort

```Javascript
  function bubbleSort(target) {
    // 不是数组或者数组长度小于2，则直接返回
    if (Object.prototype.toString.apply(target) !== '[object Array]' || target.length < 2) return target
    // 拷贝一份数组
    const _arr = [...target]
    // 遍历数组
    for (let index = 0; index < _arr.length; index++) {
      for (let innerIdx = index + 1; innerIdx < _arr.length; innerIdx++) {
        // 相邻元素两两对比，元素交换，大的元素交换到后面
        if (_arr[index] > _arr[innerIdx]) {
          [_arr[index], _arr[innerIdx]] = [_arr[innerIdx], _arr[index]]
        }
      }
    }
    // 返回新数组
    return _arr
  }
```

## 获取指定长度随机字符 getRandomStr

```Javascript
    function getRandomStr(len = 10) {
      const _strLib = "abcdefghijklmnopqrstuvwxyz0123456789";
      const _strLibLen = _strLib.length;
      let res = "";
      for (let index = 0; index < len; index++) {
        res += _strLib[Math.floor(Math.random() * _strLibLen)];
      }
      return res;
    }
```

## 手写 bind \_bind

```Javascript
    Function.prototype._bind = function(context, ...args) {
      // bind函数第一个参数是要绑定的context，后面的参数则为要传递给函数的参数
      // 返回一个新函数，新函数调用时依然可以传参，所以将两组参数合并
      return (...extraArgs) => this.apply(context, args.concat(extraArgs));
    };
```

## 手写 call \_call

```Javascript
    Function.prototype._call = function(context, ...args) {
      // 获取随机字符用作临时名
      const tempFnName = getRandomStr();
      // fn._call() this就是fn，将this赋值给context下一个key名
      context.fnTempName = this;
      // 通过context.fnTempName调用fn，隐式绑定，传入参数，并且拿到返回结果
      const result = context.fnTempName(...args);
      // 过河拆桥，用完了把他一删
      delete context.fnTempName;
      // 返回调用结果
      return result;
    };
```

## 手写 apply \_apply

```Javascript
    Function.prototype._apply = function(context, args) {
      // 获取随机字符用作临时名
      const tempFnName = getRandomStr();
      // fn._call() this就是fn，将this赋值给context下一个key名
      context.fnTempName = this;
      // 通过context.fnTempName调用fn，隐式绑定，传入参数，并且拿到返回结果
      // 这里与call不一样的就是参数的传递，apply支持的是第二个参数为数组
      const result = context.fnTempName(...args);
      // 过河拆桥，用完了把他一删
      delete context.fnTempName;
      // 返回调用结果
      return result;
    };
```

## 手写 promise \_promise

> [最简实现Promise，支持异步链式调用（20行）](https://juejin.im/post/5e6f4579f265da576429a907)

```Javascript
function _Promise(exec) {
  this.onResolvedCbs = [];
  exec(value => {
    setTimeout(() => {
      this.data = value;
      this.onResolvedCbs.forEach(cb => cb(value));
    });
  });
}

_Promise.prototype.then = function(onResolved) {
  return new _Promise(resolve => {
    console.log(resolve);
    this.onResolvedCbs.push(() => {
      const result = onResolved(this.data);
      result instanceof _Promise ? result.then(resolve) : resolve(result);
    });
  });
};

new _Promise(resolve => {
  setTimeout(() => {
    resolve(1);
  }, 500);
})
  .then(res => {
    console.log(res);
    return new _Promise(resolve => {
      setTimeout(() => {
        resolve(2);
      }, 500);
    });
  })
  .then(console.log);
```
