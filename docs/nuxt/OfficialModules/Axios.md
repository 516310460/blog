# ⚡ Axios

> 安全，轻松的Axios与Nuxt.js集成

## 特性

✓ 自动为客户端和服务器端设置基本URL

✓ 公开功能，以便我们可以轻松地全局设置身份验证令牌setToken$axios

✓ 请求基本URL时自动启用withCredentials

✓ SSR中的代理请求头（对于auth有用）

✓ 获取样式请求

✓ 在提出请求时与Nuxt.js Progressbar集成

✓ 集成 [Proxy Module](https://github.com/nuxt-community/proxy-module)

✓ 具有自动重试请求 [axios-retry](https://github.com/softonic/axios-retry)

## 使用

yarn 和 npm 安装:

```js
yarn add @nuxtjs/axios
OR
npm install @nuxtjs/axios
```

nuxt.config.js

```js
module.exports = {
  modules: [
    '@nuxtjs/axios',
  ],
​
  axios: {
    // proxyHeaders: false
  }
}
```

### Component

`asyncData`

```js
async asyncData({ $axios }) {
  const ip = await $axios.$get('http://icanhazip.com')
  return { ip }
}
```

`methods`/`created`/`mounted`/etc
```js
methods: {
  async fetchSomething() {
    const ip = await this.$axios.$get('http://icanhazip.com')
    this.ip = ip
  }
}
```

Store `nuxtServerInit`

```js
async nuxtServerInit ({ commit }, { $axios }) {
  const ip = await $axios.$get('http://icanhazip.com')
  commit('SET_IP', ip)
}
```

Store actions

```js
// In store
{
  actions: {
    async getIP ({ commit }) {
      const ip = await this.$axios.$get('http://icanhazip.com')
      commit('SET_IP', ip)
    }
  }
}
```

## 扩展

如果您需要通过注册拦截器和更改全局配置来自定义axios，则必须创建一个nuxt插件。

nuxt.config.js

```js
{
  modules: [
    '@nuxtjs/axios',
  ],
​
  plugins: [
    '~/plugins/axios'
  ]
}
```

plugins/axios.js

```js
export default function ({ $axios, redirect }) {
  $axios.onRequest(config => {
    console.log('Making request to ' + config.url)
  })
​
  $axios.onError(error => {
    const code = parseInt(error.response && error.response.status)
    if (code === 400) {
      redirect('/400')
    }
  })
}
```

### 拦截器

Axios插件提供帮助程序，可以更轻松，更快速地注册axios拦截器。

- onRequest(config)

- onResponse(response)

- onError(err)

- onRequestError(err)

- onResponseError(err)

默认情况下，这些函数不必返回任何内容。

示例: （plugins/axios.js）

```js
export default function ({ $axios, redirect }) {
  $axios.onError(error => {
    if(error.code === 500) {
      redirect('/sorry')
    }
  })
}
```

### 获取样式请求

Axios插件还支持使用前缀 `$` 的样式方法来获取请求：

```js
// Normal usage with axios
let data = (await $axios.get('...')).data
​
// Fetch Style
let data = await $axios.$get('...')
```

### 设置头部信息
`setHeader(name, value, scopes='common')`

Axios实例有一个帮助方法，可以轻松设置任何标头。

参数：

* **name**: 标题的名称
* **value**: 标头的值
* **scopes**: 默认仅针对特定类型的请求发送。
  * Type: 数组或字符串
  * Defaults: 默认所有类型的请求为 `common`
  * 可以是 `get`, `post`, `delete`, ...

```js
// Adds header: `Authorization: 123` to all requests
this.$axios.setHeader('Authorization', '123')
​
// Overrides `Authorization` header with new value
this.$axios.setHeader('Authorization', '456')
​
// Adds header: `Content-Type: application/x-www-form-urlencoded` to only post requests
this.$axios.setHeader('Content-Type', 'application/x-www-form-urlencoded', [
  'post'
])
​
// Removes default Content-Type header from `post` scope
this.$axios.setHeader('Content-Type', false, ['post'])
```

### 设置Token
`setToken(token, type, scopes='common')`

Axios实例有一个帮助方法，可以轻松设置全局身份验证标头。

参数：

* **token**: 授权令牌
* **type**: 授权令牌前缀（通常为 `Bearer`）
* **scopes**: 默认仅针对特定类型的请求发送。
  * Type: 数组或字符串
  * Defaults: 默认所有类型的请求为 `common`
  * 可以是 `get`, `post`, `delete`, ...

```js
// Adds header: `Authorization: 123` to all requests
this.$axios.setToken('123')
​
// Overrides `Authorization` header with new value
this.$axios.setToken('456')
​
// Adds header: `Authorization: Bearer 123` to all requests
this.$axios.setToken('123', 'Bearer')
​
// Adds header: `Authorization: Bearer 123` to only post and delete requests
this.$axios.setToken('123', 'Bearer', ['post', 'delete'])
​
// Removes default Authorization header from `common` scope (all requests)
this.$axios.setToken(false)
```

## 选项

您可以使用 `axios` 模块选项或部分选项在 `nuxt.config.js` 配置

### `prefix`、`host`和`port`

该选项使用在 `baseURL` 和 `browserBaseURL`

可以自定义 API_PREFIX, API_HOST (或 HOST) 和 API_PORT (或 PORT) 环境变量。
