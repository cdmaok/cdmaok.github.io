---
layout: post
title: RPC-example
category: Python
description: 远程代码调用备忘
---

RPC 远程代码调用，和RMI类似，具体看code


```Python
## 这里使用xml + rpc进行封装，得到的东西是个xml，也有类似的jsonrpc，不建议做复杂的服务
## Server code
from SimpleXMLRPCServer import SimpleXMLRPCServer

def add(x,y):
        return x+y

server = SimpleXMLRPCServer(("localhost",8099))
server.register_multicall_functions()
server.register_function(add,"add")
server.serve_forever()

##Client code
proxy= xmlrpclib.ServerProxy("http://localhost:8099/")
multicall = xmlrpclib.MultiCall(proxy)
multicall.add(3,5)
result = multicall()
print tuple(result) #use tuple here

```

```Python
## supervisord rpc 服务 example
import xmlrpclib

server = xmlrpclib.Server("http://user:123@9.186.107.28:9001/RPC2")

print server.supervisor.getState()
print server.supervisor.getProcessInfo('kafka')
print server.supervisor.getAllProcessInfo()

```
