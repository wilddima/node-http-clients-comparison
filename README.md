# Comparison of HTTP Clients for NodeJS

For these tests, i used this api: https://httpbin.org/

<table>
<tr>
  <th></th>
  <th>Request</th>
  <th>axios</th>
  <th>SuperAgent</th>
  <th>node-fetch</th>
</tr>
<tr>
  <td>Cross platform</td>
  <td>NodeJS</td>
  <td>Browser<br/>NodeJS</td>
  <td>Browser<br/>NodeJS</td>
  <td>Browser(*)<br/>NodeJS</td>
 </tr>
<tr>
  <td>Custom headers</td>
  <td>+</td>
  <td>+</td>
  <td>+</td>
  <td>+</td>
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
  <td><code>in object</code>,<br/><code>in url</code></td>
  <td><code>in object</code>,<br/><code>in url</code></td>
  <td><code>in object</code>,<br/><code>in url</code></td>
  <td><code>in url</code></td>
</tr>
<tr>
  <td>Custom HTTP verbs</td>
  <td>+</td>
  <td>+</td>
  <td>+</td>
  <td>+</td>
</tr>
<tr>
  <td>API type</td>
  <td>Callbacks<br/>Streaming</td>
  <td>Promises</td>
  <td>Promise-compatible</td>
  <td>Promises</td>
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
  <td>Basic auth(out of box)</td>
  <td>+</td>
  <td>+</td>
  <td>+</td>
  <td>-</td>
</tr>
<tr>
  <td>Default headers</td>
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
</table>
* - Node-fetch was developed with an eye to support they same api, as browsers native 'fetch'.

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

### node-fetch

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

## Custom http verbs

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

### node-fetch
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

### node-fetch
To do the same in `node-fetch` we need to set headers manually, or use external libraries

```js
fetch(endPoints.post.url, { method: 'post',
                            headers: {'Content-Type': 'application/x-www-form-urlencoded'},
                            body: 'test=test' })
  .then(
    //....
  )
```
## Redirect 302

### request
Add `Referer` header. For example:

```js
"Referer": "http://httpbin.org/redirect-to?url=http://httpbin.org/get"
```

### Axios, Superagent, node-fetch

There is no difference between normal get request, and redirected request.

## Basic-auth

Request and Axios has got similar API for BasicAuth:

### request

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
### axios

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

In the process of comparing their libraries, I came to the conclusion that they all have enough positive sides to use them.

`Request` - despite the fact that it is not cross-platform, it undoubtedly has the most opportunities out of the box. Ability to use `node streams` is look attractive.

`Axios` - as for me has the most understandable syntax and follows the idea of 'principle of least surprise'.

`SuperAgent` - also has a fairly understandable api, but much more important is the idea of its extensibility through plugins.

`node-fetch` - good old fetch api. A small number of dependencies and a familiar interface.
