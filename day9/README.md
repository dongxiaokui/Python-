多线程并发
    
    基于threading模块
        1. 创建套接字，绑定监听
	2. 接收客户端连接
	3. 创建新的线程处理客户端请求
	4. 主线程继续等待其他客户端连接
	5. 当客户端退出则线程退出

cookie： 
    信号处理僵尸：
    1. 导入信号处理模块  import signal 
    2. 在父进程中运行      
       signal.signal(signal.SIGCHLD,signal.SIG_IGN)

    这样该父进程再有子进程退出则交由系统回收


集成模块完成多进程多线程socket并发

import socketserver

功能：通过模块提供的不同的类的组合完成多进程/多线程，tcp/udp的并发程序

DatagramRequestHandler  处理数据报套接字请求
StreamRequestHandler  处理流式套接字请求

UDPServer  构建udp服务端程序
TCPServer  构建tcp服务端程序

ForkingMixIn   创建多进程并发
ForkingTCPServer    ForkingMixIn + TCPServer   
ForkingUDPServer    ForkingMixIn + UDPServer

ThreadingMixIn  创建多线程并发
ThreadingTCPServer  ThreadingMixIn + TCPServer 
ThreadingUDPServer  ThreadingMixIn + UDPServer

使用步骤： 
    1. 创建服务器类，通过选择TCPServer或者UDPServer确定服务器类型，多进程或者多线程确定并发类型
    2. 创建请求处理类，根据服务器类型选择继承流式套接字处理类还是数据报套接字处理类
    3. 通过服务器类创建服务器对象，并绑定请求处理类
    4. 通过serve_forever()接口启动服务

HTTPServer  v2.0

功能：1. 接收客户端http请求
      2. 解析客户端请求
      3. 组织数据，以http响应的格式返回
      4. 将数据发送给浏览器

升级： 1. 采用多线程并发，满足多个客户端同时请求
       2. 基本的请求解析，根据具体请求返回内容
       3. 可以为前端提供一些数据
       4. 将整体功能封装为一个类，提供给用户使用

技术点：1. 使用socket  tcp
	2. 使用多线程作为并发
	3. http协议的请求响应格式
	    http请求： 
	        请求行  GET  /abc.html  HTTP/1.1
		请求头
		空行
		请求体
	    http响应：
	        响应行  200   404  500
		响应头
		空行
		响应体  具体内容（网页或者数据）


协程基础

定义： 纤程，微线程。是为非抢占式多任务产生子程序的计算机程序组件。协程允许不同入口点在不同个位置暂停或者开始，简单来说，协程就是可以暂停执行的函数。

* yield即python实现协程的基本关键字

协程原理： 记录一个函数的上下文栈区，协程调度时，将记录的上下文栈保存起来，在切换回来时进行调取，恢复原有的执行内容，从上一次执行的位置继续执行。

协程优点：
    1. 可以同时处理多个任务
    2. 协程是一个单线程程序，消耗资源很少
    3. 协程无序进行切换的开销，也无序同步互斥操作
协程缺点：
    1. 无法利用计算机多核资源并行处理


greenlet

    安装： sudo pip3 install greenlet

    greenlet.greenlet(func)
    功能： 创建协程对象
    参数： 协程函数
    返回： 协程对象

    g.switch()
    功能： 选择要执行的协程函数

gevent

    安装： sudo pip3 install gevent

    使用步骤：
    1. 将协程封装为函数
    2. 生成协程对象
       gr = gevent.spawn(func,argv)
       功能： 生成协程对象
       参数： func 协程函数
              argv 不定参 给func按照位置传参
       返回值： 协程对象
    
    3. 协程回收
       gevent.joinall(list,[timeout])
       功能：阻塞等待回收协程
       参数： list 列表  放入要回收的协程对象
              timeout 超时时间

    gevent.sleep(): gevent阻塞，可以使协程跳转
	
    特点：
    1. 无论有多少协程运行，同一时刻只会运行一个
    2. 协程不会阻碍主进程的持续运行除非遇到gevent阻塞
