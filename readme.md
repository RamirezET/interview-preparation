# 面试准备

## 深拷贝 deepClone

```Javascript
function deepClone(target, hash = new WeakMap()) {
  if (typeof target !== "object" || target == null) return target;
  if (hash.has(target)) return hash.get(target);
  
  const result = target instanceof Array ? [] : {};
  hash.set(target, result);
  for (const key in target) {
    if (target.hasOwnProperty(key)) {
      result[key] = _deepClone(target[key]);
    }
  }
  return result;
}
```

## 防抖 debounce

```Javascript
function debounce(handler, wait = 300, immediate = false) {
  let timer;
  if (typeof handler !== "function") throw new Error("The first params shoule be a Function");
  if (typeof wait !== "number") throw new Error("The second params shoule be a Number");

  return function() {
    const context = this,
      args = [...arguments];

    timer && clearTimeout(timer);

    if (immediate) {
      const callNow = !timer;
      timer = setTimeout(() => {
        timer = null;
      }, wait);
      callNow && handler.apply(context, args);
    } else {
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
  let timer;
  return function() {
    if (timer) return;

    const context = this,
      args = [...arguments];

    timer = setTimeout(() => {
      handler.apply(context, args);
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
  
  // 拿到俩个这的keys的数组，长度不一致那肯定不一样
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
