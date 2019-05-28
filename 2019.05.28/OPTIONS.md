# axios请求中发送请求之前多一次OPTIONS请求

## 原因

> 在跨域情况下，发送一些比较复杂的请求时，会先发送一次OPTIONS请求来判断当前域名在请求目标域名下是否有访问的权限，只有请求成功了才能继续后续的主体请求

### 什么是复杂的请求

  1. 使用下列请求方法之一：
      - GET
      - POST
      - HEAD

  2. Fetch 规范定义了对[CORS安全的首部字段集合](https://fetch.spec.whatwg.org/#cors-safelisted-request-header)，不得人为设置该集合之外的其他首部字段。该集合为：
      - Accept
      - Accept-Language
      - Content-Language
      - Content-Type
      - DPR
      - Downlink
      - Save-Data
      - Viewport-Width
      - Width

  3. Content-Type 的值仅限于下列三者之一：
      - text/plain
      - multipart/form-data
      - application/x-www-form-urlencoded

  4. 请求中的任意XMLHttpRequestUpload 对象均没有注册任何事件监听器；XMLHttpRequestUpload 对象可以使用 XMLHttpRequest.upload 属性访问。

## 如何避免预检请求呢？如何把预检请求改为简单请求呢？

  1. 不设置自定义请求头
  2. 'Content-Type'设置为'applicationx-www-form-urlencoded'
      `axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded'`
  3. 把json格式的data参数用qs序列化成字符串；