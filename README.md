# swift_rpc

swift_rpc是用tornado实现的rpc服务,现在开放了四个调度接口:  

[更多的关于swift的开发信息](http://xiaorui.cc)

1. register 普通接口调用模式,最纯粹最简单  
2. register_async 借助于tornado gen.coroutine实现的非堵塞调用  
3. register_pool  借助于futures.ThreadPoolExecutor实现线程池  
4. register_mq 通过mq异步调用方法，适合后端长时间运算或耗时的调用

Change Log:  

Version: 2.1
1. 解决Nginx针对RPC负载均衡时，无法正常获取remote_ip

Version: 2.2
1. rq enqueue塞入任务队列时的一个bug,已经绕过解决

Version: 2.3
1. 解决了curl调用rq的get_result时出现的0x80 code异常

Future:  
1. 使用rsa保证rpc通信安全  
2. swift_rpc完善RQ异步任务队列  
3. 增加request json body的识别,在这基础上做安全的封装

Tornado RPC Server Usage:

```
#coding:utf-8
import time
from tornado import gen
from swift_rpc.server import RPCServer
from config import *


def test(args,**kwargs):
    return "You said %s" % args

def test_block(args):
    time.sleep(5)
    return "You said "

@gen.coroutine
def test_async(arg):
    raise gen.Return("You said async %s" % arg)

def test_mq(arg):
    print 'mq...'
    return "You said "

server = RPCServer()
server.register(test)
server.register_async(test_async)
server.register_pool(test_block)
server.register_mq(test_mq)
server.start(RPC_HOST,RPC_PORT)
```

swift_rpc client Usage:

```
#coding:utf-8
import time
from tornado import gen
from swift_rpc.server import RPCServer
from config import *


def test(args,**kwargs):
    return "You said %s" % args

def test_block(args):
    time.sleep(5)
    return "You said "

@gen.coroutine
def test_async(arg):
    raise gen.Return("You said async %s" % arg)

def test_mq(arg):
    print 'mq...'
    return "You said "

server = RPCServer()
server.register(test)
server.register_async(test_async)
server.register_pool(test_block)
server.register_mq(test_mq)
server.start(RPC_HOST,RPC_PORT)
```
