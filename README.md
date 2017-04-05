# HTTP Clients compared

<table>
<tr>
  <th></th>
  <th>Request</th>
  <th>Axios</th>
  <th>SuperAgent</th>
  <th>Node-Fetch</th>
</tr>
<tr>
  <td>NodeJS</td>
  <td>+</td>
  <td>+</td>
  <td>+</td>
  <td>+</td>
 </tr>
<tr>
  <td>Browser</td>
  <td>–</td>
  <td>+</td>
  <td>+</td>
  <td>+</td>
</tr>
<tr>
  <td>API</td>
  <td>Callback<br/>Stream</td>
  <td>Promise</td>
  <td>Promise-compatible</td>
  <td>Promise</td>
</tr>
<tr>
  <td>Custom HTTP verbs</td>
  <td>+</td>
  <td>+</td>
  <td>+</td>
  <td>+</td>
</tr> 
<tr>
  <td>Custom HTTP headers</td>
  <td>+</td>
  <td>+</td>
  <td>+</td>
  <td>+</td>
</tr>
<tr>
  <td>Default HTTP headers</td>
  <td><code>Connection</code><br/><code>Host</code></td>
  <td><code>Accept</code><br/><code>Connection</code><br/><code>Host</code><br/><code>User-Agent</code></td>
  <td><code>Accept-Encoding</code><br/>
     <code>Connection</code><br/>
     <code>Host</code><br/>
     <code>User-Agent</code></td>
  <td>
    <code>Accept</code><br/>
    <code>Accept-Encoding</code><br/>
    <code>Connection</code><br/>
    <code>Host</code><br/>
    <code>User-Agent</code>
  </td>
</tr>
<tr>
  <td>HTTPS</td>
  <td>+</td>
  <td>+</td>
  <td>+</td>
  <td>+</td>
</tr>
<tr>
  <td>GET params</td>
  <td><code>object</code>,<br/><code>url</code></td>
  <td><code>object</code>,<br/><code>url</code></td>
  <td><code>object</code>,<br/><code>url</code></td>
  <td><code>url</code></td>
</tr>
<tr>
  <td>Forms</td>
  <td>+</td>
  <td>+</td>
  <td>+</td>
  <td>-</td>
</tr>
<tr>
  <td>Cookies</td>
  <td>+</td>
  <td>-</td>
  <td>+</td>
  <td>-</td>
</tr>
<tr>
  <td>Redirect 302</td>
  <td><code>Adds header</code></td>
  <td>-</td>
  <td>-</td>
  <td>-</td>
</tr>
<tr>
  <td>Basic auth (by default)</td>
  <td>+</td>
  <td>+</td>
  <td>+</td>
  <td>-</td>
</tr>
</table>

## NodeJS

All platforms are available as NPM packages.

## Browser

1. You can bundle Request but it's way too big (~2 Mib) for Browser (due to NodeJS Streams etc.).
2. Node-Fetch just emulates native [fetch API](https://developer.mozilla.org/en/docs/Web/API/Fetch_API).

## GET params

Of course, all of these libraries supports parameters passing through the URL.

```js
let params = 'https://httpbin.org/get?id=30'
```

### Request

Supports callbacks and streams api natively. Also can use promises through external plugins.

```js
let params = {
  url: 'https://httpbin.org/get',
  qs: { id: 30 }
}

# callback
request.get(params, (error, response, body) => {
  if(error) {
    console.log(error)
  } else {
    console.log(response.statusCode)
    console.log(response.headers)
    console.log(body)
  }
})

# streams
request(params).pipe(process.stdout)
```

### Axios

```js
let params = {
  url: 'https://httpbin.org/get',
  params: { id: 30 }
}

axios.get(params)
  .then((res) => {
    console.log(res.status)
    console.log(res.headers)
    console.log(res.data)
  }).catch((err) => {
    console.log(err.status)
    console.log(res.data)
  })
```

### SuperAgent

```js
let url = 'https://httpbin.org/get'
let params = { id: 30 }

superagent.get(url)
  .query(params)
  .end((err, res) => {
    if(err) {
      console.log(err.status)
      console.log(err.response.error)
    } else {
      console.log(res.statusCode)
      console.log(res.headers)
      console.log(res.body)
    }
})
```

### Node-Fetch

```js
let url = "https://httpbin.org/get?id=30"

fetch(url)
  .then(res => {
	  console.log(res.status)
	  console.log(res.headers)
	  return(res)
  })
  .then(res => res.json())
  .then(res => {
	  console.log(res)
  }).catch(err => {
	  console.log(error)
  })
```

## Custom HTTP verbs

### Request

```js
let params = {
  method: 'move',
  url: 'https://httpbin.org/get',
  qs: { id: 30 }
}

request(params, (error, response, body) => {
 //....
})
```

### Axios

```js
let params = {
  method: 'move',
  url: 'https://httpbin.org/get',
  params: { id: 30 }
}

axios(params)
  .then((res) => {
    //...
  }).catch((err) => {
    //...
  })
```

### SuperAgent

```js
let method = 'move'
let url = 'https://httpbin.org/get'
let params = { id: 30 }

superagent(method, url)
  .query(params)
  .end(function(err, res) {
    //...
})
```

### Node-Fetch

```js
let params = { method: 'move' }
let url = "https://httpbin.org/get?id=30"

fetch(url, params)
  .then(
    //...
  })
```

## Forms

### Request

```js
request
  .post('https://httpbin.org/post', { form: { test: 'test' } })
  .pipe(process.stdout)
```

### Axios

To perform post request with `application/x-www-form-urlencoded` in Axios, we need to use `querystring` or `qs`

```js
const querystring = require('querystring')

axios.post(endPoints.post.url, querystring.stringify({form: {test: 'test'}}))
  .then((res) => {
  response(res)
}).catch((err) => {
error(err)})
```

### Superagent

By default SuperAgent user `json`, and to use `application/x-www-form-urlencoded` we need to invoke `type()`, and pass 'form' to it.

```js
superagent
  .post('https://httpbin.org/post')
  .type('form')
  .send({ test: 'test'})
  .end((err, res) => {
    //....
})
```

### Node-Fetch

To do the same in Node-Fetch we need to set headers manually, or use external libraries

```js
fetch(endPoints.post.url, { method: 'post',
                            headers: {'Content-Type': 'application/x-www-form-urlencoded'},
                            body: 'test=test' })
  .then(
    //....
  )
```
## Redirect 302

### Request

Adds `Referer` header. For example:

```js
"Referer": "http://httpbin.org/redirect-to?url=http://httpbin.org/get"
```

### Axios, Superagent, Node-Fetch

There is no difference between normal get request, and redirected request.

## Basic-auth

Request and Axios have got similar API for BasicAuth:

### Request

```js
let basicAuth = {
  method: 'get',
  uri: 'https://httpbin.org/basic-auth/user/passwd',
  auth: {
    user: 'user',
    pass: 'passwd'
  }
}

request(basicAuth, (error, response, body) => console.log(response.statusCode))
```
### Axios

```js
let basicAuth = {
    method: 'get',
    url: 'https://httpbin.org/basic-auth/user/passwd',
    auth: {
      username: 'user',
      password: 'passwd'
    }
  }

axios(basicAuth).then(res => console.log(res.status))
```

### SuperAgent

```js
superagent.get('https://httpbin.org/basic-auth/user/passwd')
  .auth('user', 'passwd')
  .end((err, res) => console.log(res.statusCode))
```

## Plugins
All these libraries have a sufficient set of plugins created by the community (for example, the implementation of the promise api for the Request). However, from this point of view, it is worth highlighting SuperAgent, which has a specialized interface for connecting plugins (https://github.com/visionmedia/superagent#plugins)

## Conclusion

For these tests, I used [HTTPbin](https://httpbin.org/).

In the process of comparing their libraries, I came to the conclusion that they all have enough positive sides to use them.

`Request` - despite the fact that it is not cross-platform, it undoubtedly has the most opportunities out of the box. Ability to use `node streams` looks attractive.

`Axios` - as for me has the most understandable syntax and follows the idea of 'principle of least surprise'.

`SuperAgent` - also has a fairly understandable api, but much more important is the idea of its extensibility through plugins.

`Node-Fetch` - good old fetch api. A small number of dependencies and a familiar interface.
