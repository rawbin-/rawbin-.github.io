# NodeJS HTTP
## http.METHODS
## http.STATUS_CODES
## http.createServer([options]\[,requestListener\])
## http.get(options[,callback])
## http.get(url[,options]\[,callback\])
## http.globalAgent
## http.maxHeaderSize
## http.request(options[,callback])
## http.request(url[,options]\[,callback]\)
## http.validateHeaderName(name)
## http.validateHeaderValue(name,value)
## http.IncomingMessage
### Event `aborted`
### Event `close`
### message.aborted
### message.complete
### message.connection
### message.destory([error])
### message.headers
### message.httpVersion
### message.method
### message.rawHeaders
### message.rawTrailers
### message.setTimeout(msecs[,callback])
### message.socket
### message.statusCode
### message.statusMessage
### message.trailers
### message.url
## http.serverResponse
### Event `close`
### Event `finish`
### response.addTrailers(headers)
### response.connection
### response.cork()
### response.end([data[,encoding]]\[,callback]\)
### response.finished
### response.flushHeaders()
### response.getHeader(name)
### response.getHeaderNames()
### response.getHeaders()
### response.hasHeader(name)
### response.headersSent
### rsponse.removeHeader(name)
### response.req
### response.sendDate
### response.setHeader(name,value)
### response.setTimeout(msecs[,callback])
### response.socket
### response.statusCode
### response.statusMessage
### response.uncork()
### response.writableEnded
### response.writableFinished
### response.write(chunk[,encoding]\[,callback\])
### response.writeContinue()
### response.writeHead(statusCode[,statusMessage]\[,headers]\)
### response.writeProcessing()

## http.OutgoingMessage
### Event `drain`
### Event `finish`
### Event `prefinish`
### outgoingMessage.addTrailers(headers)
### outgoingMessage.connection
### outgoingMessage.cork()
### outgoingMessage.destory([error])
### outgoingMessage.end(chunk[,encoding]\[,callback]\)
### outgoingMessage.flushHeaders()
### outgoingMessage.getHeader(name)
### outgoingMessage.getHeaderNames()
### outgoingMessage.getHeaders()
### outgoingMessage.headersSent
### outgoiongMessage.pipe()
### outgoingMessage.removeHeader()
### outgoingMessage.setHeader(name,value)
### outgoingMessage.setTimeout(msesc[,callback])
### outgoingMessage.socket
### outgoingMessage.uncork()
### outgoingMessage.writableCorked
### outgoingMessage.writableEnded
### outgoingMessage.writableFinished
### outgoingMessage.writableHighWaterMark
### outgoingMessage.writableLength
### outgoingMessage.writableObjectMode
### outgoingMessage.write(chunk[,encoding]\[,callback]\)


## http.Agent
管理连接持久化和复用，维护了待处理请求的队列
### new Agent([options])
- keepAlive
- keepAliveMsecs
- maxSockets
- maxTotalSockets
- scheduling
- timeout
### agent.createConnection(options,[,callback])
### agent.keepSocketAlive(socket)
### agent.reuseSocket(socket,request)
### agent.destory()
### agent.freeSockets
### agent.getName(options)
### agent.maxFreeSockets
### agent.maxSockets
### agent.maxTotalSockets
### agent.requests
### agent.sockets
## http.ClientRequest
- http.request返回的对象
### Event `abort`
### Event `connect`
### Event `continue`
### Event `information`
### Event `response`
### Event `socket`
### Event `timeout`
### Event `upgrade`
### request.abort()
### request.aborted
### request.connection
### request.end([data,[,encoding]][] \[, callback \])
### request.destory([error])
### request.destoryed
### request.finished
### request.flushHeaders()
### request.getHeader(name)
### request.getRawHeaderNames()
### request.maxHeadersCount
### request.path
### request.method
### request.host
### request.protocol
### request.removeHeader(name)
### request.reusedSocket
### request.setHeader(name,value)
### request.setNoDelay([noDelay])
### request.setSocketKeepAlive([enable]\[,initialDelay\])
### request.setTimeout(timeout,\[,callback\])
### request.socket
### request.writableEnded
### request.writableFinished
### request.write(chunk[,encoding]\[,callback\])
## http.Server
### Event `checkContinue`
### Event `checkExpectation`
### Event `clientError`
### Event `close`
### Event `connect`
### Event `connection`
### Event `request`
### Event `upgrade`
### server.close([callback])
### server.headersTimeout
### server.listen
### server.listening
### server.maxHeadersCount
### server.requestTimeout
### server.setTimeout([msecs]\[,callback\])
### server.maxRequestsPerSocket
### server.timeout
### server.keepAliveTimeout





## 常用中间件
### [chimurai](https://github.com/chimurai)/**[http-proxy-middleware](https://github.com/chimurai/http-proxy-middleware)**
