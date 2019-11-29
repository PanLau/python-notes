# 多任务-线程

## 1.target=函数后写上括号就意味着调用函数
    不写括号代表着只是找它的位置

## 2.只要是一个程序运行起来之后，它一定可以执行代码，那么把这个默认的可以执行代码的东西，称之为主线程

## 3.threading.enumerate返回一个列表
    当调用start方法的时候，才会真正的创建一个线程，并且这个线程立马开始执行，在创建这个t1指向的Thread对象时，target指定的哪个函数名，那么么这个新的线程就会到哪个函数中执行代码

    一个子线程的开始是从调用start之后
    一个子线程的结束时当那个函数执行完之后

## 4.线程的执行是没有顺序的

## 5.操作系统的调度算法：时间片轮转，优先级调度
    到底是哪个线程先执行，由操作系统的调度算法决定
    操作系统的核心是支持多任务

## 6.并发：假的多任务，任务数>cpu核心数
    并行：真的多任务，任务数<cpu核心数