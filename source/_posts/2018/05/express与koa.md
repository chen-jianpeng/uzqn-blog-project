---
title: express与koa
date: 2018-05-10 11:14:26
tags: express koa es6
header-img: "head.jpg"
---
express
```
var express = require('express')
var app = express()

var asyncIO = function(cb){
  setTimeout(function(){
    cb()
  },500)
}

var mid = function(req, res, next){
  req.body = 'mark'
  next()
}

app.use(mid)
app.use(function(req, res, next){
  asyncIO(function() {
    req.body += ' saved'
    res.send(req.body + ' done')
  })
})

app.listen(3000)
```

koa
```
var koa = require('koa')
var app = koa()

var asyncIO = function() {
  return new Promise (function(resolve){
    setTimeout(function(){
      resolve()
  },500)
  })
}

var mid = function() {
  return function *(next) {
    this.body += ' mark'
    yield next
    this.body += ' done'
  }
}

app.use(mid())
app.use(function *(next){
  yield asyncIO()
  this.body = 'saved'
  yield next
})
```

koa2
```
var Koa = require('koa')
var app = new Koa()

var asyncIO = () => {
  return new Promise(rersolve => {settimeout(resolve,500)})
}

var mid = () => { async(next) => {
    this.body += ' mark'
    await next
    this.body += ' done'
  }
}

app.use(mid())
app.use(async(next) => {
  await asyncIO()
  this.body = 'saved'
  await next
})
```
