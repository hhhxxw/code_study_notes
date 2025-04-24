动态传递参数

什么叫做发送异步请求？
>异步请求是指客户端（如浏览器或应用程序）向服务器发送请求后，不需要等待服务器的响应，而是继续执行其他任务。当服务器返回响应时，客户端再处理这个响应。这种方式可以提高程序的效率，避免阻塞主线程。

发送异步请求的场景
前端发送异步请求
在前端开发中，通常会使用 JavaScript 的 `fetch` 或 `XMLHttpRequest` 来发送异步请求,例如
```javascript
  sendRequest(){

    wx.request({

      url: 'http://localhost:8080/user/shop/status',

      method: "GET",

      success: (res)=>{

          console.log(res.data)

      }

    })

  }
```
后端发送异步请求



