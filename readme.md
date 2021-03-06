# extend-ajax
[![Build Status](https://travis-ci.org/YuChenLi923/extend-ajax.svg?branch=master)](https://travis-ci.org/YuChenLi923/extend-ajax)
![NPM version](https://badge.fury.io/js/extend-ajax.svg)
![Downloads](http://img.shields.io/npm/dm/extend-ajax.svg?style=flat)
[![Coverage Status](https://coveralls.io/repos/github/YuChenLi923/extend-ajax/badge.svg?branch=master)](https://coveralls.io/github/YuChenLi923/extend-ajax?branch=master)

[中文文档](./README_zh-CN.md)

## Features

- ability to specify request headers
- ability to get response headers
- if it isn't jsonp,it can cache response data
- pre-treat response received
- support jsonp
- support IE10+


## Install

```
npm i extend-ajax --save
```

## Examples

send a simple post request

```
ajax('test/post', 'post').send({
   text: 'hello'
}).then(({data})=> {
  console.log(data);
})
```

send a post request and specify global options

```
var ajax = require('extend-ajax');
ajax.config({
   host: 'http://xxx.com/',
   convert: (data) => {
     return JSON.parse(data);
   },
   cacheSize: 2
});
ajax('test/post', 'post', {
     header: {
       'Content-Type': 'json',
       'Accept': 'json'
     }
}).send().then(({data})=> {
      console.log(data);
})
```

file upload(IE10+)
```
html:

<input name = "file" type = "file" id = "file">

js:
var ajax = require('extend-ajax');
var files = document.getElementById('file').files;
var fileUpload =  ajax('/upload', 'post', {
  header: {
    'Content-Type': 'formData'
  }
}).send({
  file: files
}).then(() => {
  alert('file is successfully uploaded');
});

```

jsonp

```
 var JSONP = ajax('/test/jsonp', 'jsonp', {
     jsonpParam: 'callback',
     jsonpName: 'getDataByJsonp'
 });
 JSONP.on('success', function (data) {
     console.log('get data:' + data + ' with jsonp');
     done();
 });
 JSONP.send();
```

## API

### ajax(url[,method,options])

create one ajax object, but it can't send a request immediately, you need use ajax.send() to send a request.

- url \<string> a reequest url

- method \<string> request method。default: 'post'.

- options \<object>

  - isAsync \<boolean> default: true
  - cacheSize \<number> set size of cache, default: 0
  - cacheExp \<number> set the cache expiration time relative to the present, default: 300, unit: s.
  - charset \<stirng> set http charset,default: 'utf-8'
  - convert \<function>  pre-treat response data received, the function have one argument:response data.if set it, it must return a data handled,or when success event happen,we can't get data from res.
  - host \<string> host url,default: ''.
  - jsonpName \<String> name of the callback functions that handle jsonp response,default: 'jsonpCallback'
  - jsonpParma \<String> name of the query string parameter to specify the callback,default: 'callback'
  - header \<object> set http header.default: {'Content-Type': 'form', 'Accept': 'json'}
      - Content-Type \<string> you can set: 'text', 'json', 'form'(default), 'formData', 'html' and standard content-type value
      - Accept \<string> you can set: 'text', 'json', 'html' and standard Accept value
      - ...
  - scope \<object> default null, define the scope of callback function.
  - timeout \<number> the number of milliseconds a request can take before automatically being terminated
  - withCredentials \<Boolean> default: false, whether or not cross-site Access-Control requests should be made using credentials such as cookies, authorization headers or TLS client certificates
  - autoAbort \<Boolean> default: false, Whether to abort requests where from the previousroute page when the page url changed and does not refresh(For single page application).

### ajax.send(data)

send the ajax request, if browser support Promise, it will return Promise.If not or jsonp, it will return itself.

- data \<string>|\<object>  if the request method is 'get', it's appended to query string of the URL, or it's sended to remote of body.

### ajax.abort()

abort the ajax request

### ajax.then(cb)

when browser can't support Promise,ajax.send() will return the ajax object, so you can use ajax.then() to get response.But if type is 'jsonp',ajax.then() will don't work.

- cb \<function> the function have one argument res object include header, status, data.

### ajax.on(event, cb)

add event listener for the ajax object.if async is false, ajax.on() must be called before send,else it will not work.
- cb \<funtction>  the cb is called when event happen
- event \<string>
  - success  cb is fired when getting the response successfully
  - fail  cb is fired when  failing to get the response(eg:http status: 404, 400, 500..., or jsonp fail)
  - abort  cb is fired when request is aborted
  - timeout  cb is fired when request timeout
  - start cb is fired when start send the request
  - end cb is fired when request is finished whether it's a success or a failure

### ajax.config(options)

specify global options, each ajax object will apply the options and own options.

- poolSize \<number> xhr pool's size



## How to use in IE8-9

Please use version 1. X and 0. X.
