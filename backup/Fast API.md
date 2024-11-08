# 之前web开发框架的体验
之前主要用django/flask做web开发，他们是同步web框架。之前也体验过twisted/klein，是异步开发框架，但开发起来比较反人类。
如果想吃到 异步/协程 的好处，也可以用gunicorn来做部署容器。但实际情况是，我后来做的很多服务是计算密集型的，还不需要web框架来提升性能的程度，所以也就没有继续研究。
之前做IO密集型服务时，用过协程，但发现一旦某个第三方库不支持异步，就成为了整个服务的瓶颈，因为其他的协程都需要来等它。

# 为什么又注意到Fast API
FastAPI号称开发到运行都很fast。

## 快速开发
主要得益于pydantic。它结合python最近几个版本的type hint，把python从动态语言从编辑层面变得更加静态。是的，只是编辑层面，可以让你更好跳转和代码不全、语法检查，因为python运行时根本不会检查这些标注。
当你看代码时，不知道参数是多复杂的数据结构，只能通过运行调试才能分析问题时；当你发现变量/函数在编辑器里面是白色的，无法跳转时，就会发现标注功能太棒了。这是一个小功能，却是python工程化的一大步。
fastapi结合pydantic，实现了运行时的数据检查，以及API文档的自动生成，这就更棒了。
话说，python最近几个版本的各种协程实现，以及python3.8 之后的async/await关键字等知识，也可以借此机会梳理调研一下了。

## 快速运行
快速运行主要得益于starlette，[性能对比](https://www.techempower.com/benchmarks/#section=data-r17&hw=ph&test=query&l=zijmkf-1)。这个我了解不是很深入。先在这里挖点坑，后续看文档注意一下：
1. 它是通过什么快速运行的？协程，异步，还是核心代码使用C？
2. 横向扩展如何？生产使用还需要gunicorn这种web容器吗？
3. 对机器学习服务的部署也友好？[部署文档](https://fastapi.tiangolo.com/deployment/)

## 其他收获
在读文档时，发现fastapi有好些有意思的技术科普文档。比如 [python的类型](https://fastapi.tiangolo.com/python-types/) [并发和并行](https://fastapi.tiangolo.com/async/#is-concurrency-better-than-parallelism) 都非常棒。

# 同步和异步
关于快速运行，有必要了解一些基础的信息和注意事项，确保我们知道什么时候异步框架才能快速运行。
1. 所有异步函数定义需要使用async关键词，调用的时候都需要使用await关键词。这使得编码变得更加复杂。如果忘加了，会怎样？
2. 异步函数可以在asyncio.get_event_loop得到的loop中运行。实际上是一个线程，里面可以运行很多协程。其他实现如 uvloop。
3. 如果在异步函数中调用了同步函数，会阻塞loop中的所有协程。比如time.sleep是同步函数，asyncio.sleep是异步函数。
4. 如果为了避免同步函数对异步框架造成的性能损失，可能把同步函数放在线程池中执行，也可以换用其他第三方库，用异步库替代同步库。这使得在引入第三方库时，都需要评估它是同步还是异步，否则可能对性能造成大的影响。
> 这和使用协程时的注意事项是一个原理。在几年前我们团队尝试将gunicorn中的worker从gthread改为gevent的时候就发现了。因为我们无法完全改造使用的第三方库，我们最终还是使用了gthread。
