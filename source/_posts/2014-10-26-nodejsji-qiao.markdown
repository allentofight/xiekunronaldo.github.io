---
layout: post
title: "NodeJS技巧"
date: 2014-10-26 12:29:55 +0800
comments: true
categories: NodeJS
---
1. 用Curl测试NodeJS
Node启动后,可以用`curl -i http://localhost:8080`来测试返回结果,`-i`表示打印出header和返回结果,假设我们要运行的程序如下

```javascript
var http = require("http");

function process_request(req, res) {
    var body = 'Thanks for calling!\n';
    var content_length = body.length;
    res.writeHead(200, {
        'Content-Length': content_length,
        'Content-Type': 'text/plain'
    });
    res.end(body);
}

var s = http.createServer(process_request);
s.listen(8080);
```
用curl后返回的结果为

```javascript
HTTP/1.1 200 OKContent-Length: 20Content-Type: text/plainDate: Tue, 15 Feb 2013 03:05:08 GMT 
Connection: keep-alive
Thanks for calling!
```
注意到以上的keep-alive,表示在同一次网络链接上允许有多个request

2. Node的调试
在node后加上`debug`即可

```javascript
node debug debugging.js
```

Node debugger的几个键命令

* `cont`—Continue running.* `next`—Step over the next statement.* `step`—Step into the next statement (if possible; otherwise, just step over).* `out`—Step out of the current executing function.* `backtrace`—Show me the current call execution frame or call stack.* `repl`—Start the Node REPL to allow you to view variable values and execute code.* `watch(expr)`—Add the given expression to the watch list, which is shown whenever you step or move through anything in the debugger.* `list(n)`—List the n lines of source code before and after the currently stopped line in the debugger.
用[node-inspector](https://github.com/node-inspector/node-inspector)
3.删除Array的元素
注意 `delete arr[0]`只是将arr的首个元素替换为undefine，实际并未真正删除,若要真正删除可用`arr.splice(index, length)`
4. 将匿名函数赋给变量后,由于匿名函数没有名字，不好调试，可以改成以下形式
```javascript
var x = function bad_apple() { 
	throw new Error("whoopsie");}
> x();Error: whoopsieat bad_apple (repl:2:7)at repl:1:1at REPLServer.self.eval (repl.js:109:21) 
at rli.on.self.bufferedCmd (repl.js:258:20)```
5. 有时会碰到req.session保存的变量不起作用的情况,按以下方式解决

```javascript
app.use(express.cookieParser("kitten on  keyboard"));
// app.use(express.cookieSession({
//     secret: "FLUFFY BUNNIES",
//     maxAge: 86400000
// }));

// app.use(express.session());
app.use(express.session({
    secret: '1234567890QWERTY'
}));
```

`注意`,要注释掉`express.cookieParser`