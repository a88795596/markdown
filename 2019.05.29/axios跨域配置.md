# axios 跨域配置注意项

[npm官网](https://www.npmjs.com/package/axios)

[中文网](http://www.axios-js.com/)

## 代理配置

> 在配置跨域代理时，可以不配置基础域名，因为代理配置中匹配的正则是以 `config.url` 来进行匹配。在配置了 `config.baseUrl` 后会因为URL变化导致匹配失败，不能进行跨域。axios的跨域配置只能在开发环境中使用，实际生产环境中还是需要服务端配置跨域。

### 配置多项代理

```javascript
    devServer: {
      proxy: {
        '/api': {
          target: '<url>', // 目标域名
          ws: true,
          changeOrigin: true, //是否跨域
          pathRewrite: { // 重写URL
            "^/api": "" // 正则匹配
          },
          router: {
            // when request.headers.host == 'dev.localhost:3000',
            // override target 'http://www.example.org' to 'http://localhost:8000'
            'dev.localhost:3000': 'http://localhost:8000'
          }
        },
        '/foo': {
          target: '<other_url>'
        }
      }
    }
```