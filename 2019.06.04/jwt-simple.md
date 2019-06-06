# [jwt-simple](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html#rfc.section.3.1)

## 简介

jwt, JSON WEB TOKEN 是一种token加密方案。由三部分构成，`Header` `payload` `Signature`。通过jwt生成的token将三者转化为`base64`用`.`链接为一个字符串，即生成的token。

## 使用案例

```javascript
const jwt = require("jwt-simple");
const HS = "HS256";
const config = require("../config")

function enCode(obj) {
  // 配置的密钥关键字 设置的一个Array
  let keys = config.secretKey;
  // 转化密钥为字符串
  let other = getKey(keys);
  // 加密
  let token = makeToken(obj, other);
  return token
}

function deCode(token) {
  let keys = config.secretKey;
  let other = getKey(keys);
  //解密并返回
  return deToken(token, other);
}

```

### Header的构成。

header是一个包含了两个属性的对象，分别是`typ`和`cty`。
`typ` 该值用来声明类型，默认`jwt`
`cty` 加密方式，在生成TOKEN时可以在makeToken第二个参数指定。可用值有4种。

```javaScript
  // jwt-simple源码
  var algorithmMap = {
    HS256: 'sha256',
    HS384: 'sha384',
    HS512: 'sha512',
    RS256: 'RSA-SHA256'
  };
  var typeMap = {
    HS256: 'hmac',
    HS384: 'hmac',
    HS512: 'hmac',
    RS256: 'sign'
  };
```

### Payload的构成

`payload`是我们token中需要携带的信息，一般以一个对象的形式传入，它内置包含了多个校验参数。

1. `iss`， 用以记录token的发行人。
2. `sub`， 确定了作为JWT主体的委托人。该权利要求的处理通常是特定于应用的。该子value是一个区分大小写的字符串，包含StringOrURI值。使用此声明是可选的
3. `aud`， 受众，指定token的接收者。`aud`的value是一个区分大小写的字符串数组，每个字符串都包含一个StringOrURI值。在JWT有一个观众的特殊情况下，`aud`的value可以是包含StringOrURI值的单个区分大小写的字符串。对受众价值的解释通常是针对具体应用的。使用此声明是可选的。
4. `exp`， 用来设定到期时间。在jwt-simple中默认开启校验，时间以秒为单位，在decode时如果exp到期会抛出error，要注意error处理。
5. `nbf`， token开始生效时间，使用者可以提供一些小的余地，通常不超过几分钟，以解决时钟偏差。它的值必须是包含NumericDate值的数字。
6. `iat`， token的发布时间。该声明可用于确定JWT的年龄。它的值必须是包含NumericDate值的数字
7. `jti`， 唯一标识符。JWT Token的ID，避免发出相同token。

### Signature的生成方式

```JavaScript
// jwt-simple源码
/**
 * @param {*} input Header和Payload生成的值，也就是token的前两项
 * @param {*} key 传入的加密字符串
 * @param {*} method 加密方式
 * @param {*} type
 * @param {*} signature
 */
function verify(input, key, method, type, signature) {
  if(type === "hmac") {
    return (signature === sign(input, key, method, type));
  }
  else if(type == "sign") {
    return crypto.createVerify(method)
                 .update(input)
                 .verify(key, base64urlUnescape(signature), 'base64');
  }
  else {
    throw new Error('Algorithm type not recognized');
  }
}
```

在解析token时会进行token有效性的校验，错误会抛出error

## 注意事项

* 生成和解析Token

  ```typescript
  export type TAlgorithm = 'HS256' | 'HS384' | 'HS512' | 'RS256';

  export interface IOptions {
    header: any;
  }
  // 设置noVerify来决定是否开启内置字段校验。 在jwt-simple中校验失败都会抛出error，所以要做好错误处理。默认开启。
  export function decode(token: string, key: string, noVerify?: boolean, algorithm?: TAlgorithm): any;

  export function encode(payload: any, key: string, algorithm?: TAlgorithm, options?: IOptions): string;
  ```

* 生成的token比较容易解密，不建议放置敏感信息。
* TS舒服