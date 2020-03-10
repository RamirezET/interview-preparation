# 面试准备

## 深拷贝 DeepClone

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

## 防抖 Debounce

```Javascript
function debounce(handler, wait = 300, immediate = false) {
  let timer;
  if (typeof handler !== "function")
    throw new Error("The first params shoule be a Function");
  if (typeof wait !== "number")
    throw new Error("The second params shoule be a Number");
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

## 节流 Throttle

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
