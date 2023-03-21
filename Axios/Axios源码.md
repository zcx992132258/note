#Axios 架构图
![[Pasted image 20230318162549.png]]



<details>
<summary><strong>目录</strong></summary>

- [构造方法 createInstance](#createInstance)
  * [原型绑定方法 bind](#supports-keys-with-dots)


</details>
##<a id="createInstance">构造方法 createInstance</a> 
```javascript
function createInstance(defaultConfig) {

// 实例化一个 axios 对象
  const context = new Axios(defaultConfig);

// 返回一个函数  Axios 原型方法 request this 指向 Axios 实例对象
  const instance = bind(Axios.prototype.request, context);

// instance 继承 Axios 所有原型方法 this 指向 context 实例对象
  utils.extend(instance, Axios.prototype, context, { allOwnKeys: true });

// instance 继承 axios 实例 context 所有属性方法
  utils.extend(instance, context, null, { allOwnKeys: true });

// 建立创建方法 接受配置传参并且和默认配置进行合并
  instance.create = function create(instanceConfig) {

return createInstance(mergeConfig(defaultConfig, instanceConfig));

};

return instance;

}

````

###<a id='bind'>原型绑定方法 bind</a>

```javascript
// 手动实现bind
function bind(fn, thisArg) {

  return function wrap() {

    return fn.apply(thisArg, arguments);

  };
}
````

###extend

```javascript
/*
	继承
	@param {Object} a
	@parma {Object} b
	@parma {Object} thisArg
	@param {Object} data
	@param {Boolean} data.allOwnKeys
*/
const extend = (a, b, thisArg, { allOwnKeys } = {}) => {
  forEach(
    b,
    (val, key) => {
      if (thisArg && isFunction(val)) {
        a[key] = bind(val, thisArg);
      } else {
        a[key] = val;
      }
    },
    { allOwnKeys }
  );

  return a;
};

/*
遍历数组对象
@param {Object} obj 用来遍历的对象
@param {Function} fn 回调函数
@param {Object} data
@param {Boolean} data.allOwnKeys
*/
function forEach(obj, fn, { allOwnKeys = false } = {}) {
  // 如果是obj是空就取消
  if (obj === null || typeof obj === "undefined") {
    return;
  }

  let i;

  let l;

  //如果不是一个对象 就转数组
  if (typeof obj !== "object") {
    obj = [obj];
  }

  //如果是数组就进行遍历
  if (isArray(obj)) {
    for (i = 0, l = obj.length; i < l; i++) {
      fn.call(null, obj[i], i, obj);
    }
  } else {
    // 拿到有所key数组进行遍历
    const keys = allOwnKeys
      ? Object.getOwnPropertyNames(obj)
      : Object.keys(obj);

    const len = keys.length;

    let key;

    for (i = 0; i < len; i++) {
      key = keys[i];

      fn.call(null, obj[key], key, obj);
    }
  }
}
```










