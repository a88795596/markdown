# JOI参数校验包

[github](https://github.com/hapijs/joi)

[API文档](https://github.com/hapijs/joi/blob/v16.0.0-rc1/API.md)

## 安装

```javascript
npm install --save @hapi/joi
```

## 基础使用

```javascript
validate(value, schema, [options], [callback]);
```

- `value` 需要校验的值
- `schema` 校验规则
- `options` 校验配置  

  1. `abortEarly` 是否需要全部校验 true
  2. `allowUnknown` 是否允许对象包含未知键 false
  3. `convert` 尝试将值转换为所需类型（例如，字符串为数字）true
  4. `escapeHtml` HTML的安全校验。出于安全考虑，错误消息模板何时会将特殊字符转义为HTML实体。默认为false
  5. `stripUnknown`  从对象和数组中删除未知元素。默认为false
  6. .........

- `callback` 校验完毕的回调函数，可选，带error参数和value参数
  1. 如果验证失败，则出现错误原因，否则null
  2. 应用任何类型转换和其他修饰符的验证值（输入保持不变）。value如果验证失败，abortEarly则可能不完整true。如果未提供回调，则返回具有错误 和值属性的对象。

### 常用校验类型

1. string
2. number
3. object
4. array
5. date
6. boolean

>在没有回调的情况下使用时，此函数返回一个类似Promise的对象，可以用作promise或简单对象，如下例所示

```javascript
const schema = {
    a: Joi.number()
};

const value = {
    a: '123'
};

Joi.validate(value, schema, (err, value) => { });
// err -> null
// value.a -> 123 (number, not string)

// or
const result = Joi.validate(value, schema);
// result.error -> null
// result.value -> { "a" : 123 }

// or
const promise = Joi.validate(value, schema);
promise.then((value) => {
    // value -> { "a" : 123 }
});
```

## 基础案例

```javascript
const Joi = require('@hapi/joi');

const schema = Joi.object().keys({
    // 限定类型为字符串，只能为数字和字母，长度最少3，最高30，必填项
    username: Joi.string().alphanum().min(3).max(30).required(),
    // 限定类型为字符串，符合自定义正则校验。
    password: Joi.string().regex(/^[a-zA-Z0-9]{3,30}$/),
    // 可选类型为字符串或者数值。
    access_token: [Joi.string(), Joi.number()],
    // 限定类型为数值，整数，最小1900，最高2013
    birthyear: Joi.number().integer().min(1900).max(2013),
    // 限定类型为字符串，邮件格式，必须有两个域
    email: Joi.string().email({ minDomainSegments: 2 })

    // 要求username和birthyear同时出现，password和access_token不能同时出现
}).with('username', 'birthyear').without('password', 'access_token');

// Return result.
const result = Joi.validate({ username: 'abc', birthyear: 1994 }, schema);
// result.error === null -> valid

// You can also pass a callback which will be called synchronously with the validation result.
Joi.validate({ username: 'abc', birthyear: 1994 }, schema, function (err, value) { });  // err === null -> valid
```

### 数组嵌套对象校验

```javascript
const schemaItem = Joi.object().keys({
  name: Joi.string().min(2).max(10).required(),
  password: Joi.string().regex(/^[a-zA-Z0-9]{3,30}$/).required(),
  age: Joi.number().integer().min(18).max(80),
  email: Joi.string().email({ minDomainSegments: 2 }),
  phone: Joi.string().regex(/^\d{11}$/)
})
const schema = Joi.array().min(1).max(3).items(schemaItem)
Joi.validate(arr, schema)
```
