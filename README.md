# Golang
出于兴趣，开启Golang超神之路
# Golang天生支持高并发，就连一个简单的helloworld接口也特娘的支持高并发，代码撸起来。
go搭建web服务器两大优势： <br /> 
1.不需要服务器承载，go直接监听tcp端口。 <br /> 
2.web服务内部有支持高并发的特性<br /> 

毫无疑问，Go的web请求的过程，主要httprequest+httphandle+httpresponse<br /> 
Request：用户请求的信息，用来解析用户的请求信息，包括post、get、cookie、url等信息<br /> 
Response：服务器需要反馈给客户端的信息<br /> 
Conn：用户的每次请求链接<br /> 
Handler：处理请求和生成返回信息的处理逻辑<br /> 
## 1.如何监听端口？
Go是通过一个函数ListenAndServe来处理这些事情的，这个底层其实这样处理的：<br /> 
初始化一个server对象，然后调用了net.Listen("tcp", addr)，也就是底层用TCP协议搭建了一个服务，然后监控我们设置的端口。<br /> 
## 2.如何接收客户端请求？<br /> 
调用了srv.Serve(net.Listener)函数，这个函数就是处理接收客户端的请求信 息。这个函数里面起了一个for{}，首先通过Listener接收请求，其次创建一个 Conn，最后单独开了一个 goroutine，把这个请求的数据当做参数扔给这个conn去服务：go c.serve()。这 个就是高并发体现了， 用户的每一次请求都是在一个新的goroutine去服务，相互不影响。
## 3.如何分配handler？<br /> 
conn首先会解析request:c.readRequest(),然后获取相应的handler:handler := c.server.Handler，也就是我们刚才在调用函数ListenAndServe时候的第二个参数，我们前面例子传递的是nil，也就是为空，那么默认获取handler = DefaultServeMux,那么这个变量用来做什么的呢？对，这个变量就是一个路由器，它用来匹配url跳转到其相应的handle函数，那么这个我们有设置过吗?有，我们调用的代码里面第一句不是调用了http.HandleFunc("/", sayhelloName)嘛。这个作用就是注册了请求/的路由规则，当请求uri为"/"，路由就会转到函数sayhelloName，DefaultServeMux会调用ServeHTTP方法，这个方法内部其实就是调用sayhelloName本身，最后通过写入response的信息反馈到客户端。
