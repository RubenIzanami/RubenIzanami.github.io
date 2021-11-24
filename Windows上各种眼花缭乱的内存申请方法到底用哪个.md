## Windows上各种眼花缭乱的内存申请方法到底用哪个

> GlobalAlloc vs LocalAlloc vs HeapAlloc vs VirtualAlloc vs malloc vs new

**结论：MSDN推荐在Windows上使用HeapAlloc申请内存。**

GlobalAlloc、LocalAlloc、HeapAlloc、VirtualAlloc都是Windows API，只能够在Windows平台上使用。而malloc和new是C标准库中的函数，可以在任何平台上使用。

虽然GlobalAlloc、LocalAlloc和HeapAlloc函数最终从同一个堆分配内存，但每个函数提供的功能略有不同。例如，当无法分配内存时，可以指示HeapAlloc抛出异常，但LocalAlloc就不具备这个功能。同样的，LocalAlloc支持重新分配内存而不更改句柄的值，而HeapAlloc就不支持。

从32位系统开始，global和local实现为对应的heap函数的封装，内部则通过这些heap函数调用进程的堆句柄。因此，global函数和local函数比其他内存管理函数有更大的内存开销。

所以heap函数相比global函数和local函数具有更多的特性，新的应用最好还是使用heap函数，除非旧代码中包含有使用global和local函数申请的内存空间。

VirtualAlloc申请的内存是页面粒度的，常用于申请可执行代码的运行空间（比如从内存加载某个PE文件），因此申请的空间通常比较大。

malloc的缺点是依赖于C运行库，new的缺点是依赖编译器和语言。这导致不同模块（比如DLL）因为依赖不同的C运行库，而不能跨模块申请和销毁内存。例如在DLL中申请的内存就无法在调用它的EXE中销毁。

自32位后，Windows内存模型发生了改变，以前16位的时候可以使用global函数申请到far指针以和其他进程共享内存，但在32位任何内存申请都只是在该进程的虚拟地址空间上进行操作，无法影响其他进程。不过想要与其他进程进行内存共享，可以采用File Mapping函数。

### 参考链接

https://docs.microsoft.com/en-us/windows/win32/memory/comparing-memory-allocation-methods?redirectedfrom=MSDN

https://docs.microsoft.com/en-us/windows/win32/memory/global-and-local-functions

https://stackoverflow.com/questions/34326835/localalloc-vs-globalalloc-vs-malloc-vs-new

