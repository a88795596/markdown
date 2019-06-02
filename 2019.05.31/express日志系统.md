# express中搭建访问日志系统

## 需要的插件

  1. [file-stream-rotator](https://www.npmjs.com/package/file-stream-rotator)
  2. [morgan](https://www.npmjs.com/package/morgan)

## 示例代码

```javaScript
  let morgan = require('morgan');
  let FileStreamRotator = require('file-stream-rotator');
  let fs = require('fs');
  let path = require('path');
  //设置路径
  const logDirectory = path.join(__dirname, "../../", 'log');
  //判断是否存在文件夹，不存在则新建（注意权限）
  fs.existsSync(logDirectory) || fs.mkdirSync(logDirectory);
  // create a rotating write stream
  var accessLogStream = FileStreamRotator.getStream({
    date_format: 'YYYYMMDD',
    filename: path.join(logDirectory, 'log-%DATE%.log'),
    frequency: 'daily',
    size: "1m",
    verbose: false
  })
  // 配置日志记录模板
  const template = `
  REQUEST_Time:    :date[iso]
  REQUEST_URL :    :url
  REFERRER    :    :referrer
  REQUEST_TYPE:    :method
  HTTP_STATUS :    :status
  HTTP_VERSION:    :http-version
  REQUEST_IP  :    :remote-addr
  REQUEST_HEAD:    :req[header]
  USER_ANGENT :    :user-agent
  `;
  /**
  * 
  * @param {Object} req
  * @param {Object} res 
  * @returns {Boolean} flag 返回false则进行日志记录， 返回true则跳过不进行记录
  * 过滤函数，根据请求自定义记录日志
  */
  function skip(req, res) {
    let flag = false;
    let skipArray = ["js", "css", "ico"];
    let reg = new RegExp(`.(${skipArray.join("|")})$`, "i")
    flag = reg.test(req.path);
    return flag;
  }
  let log = morgan(template, { stream: accessLogStream, skip });
  // 根据环境判断是否需要记录到日志，开发环境直接控制台输出
  module.exports = process.env.NODE_ENV === "development" ? morgan(template) : log;
```