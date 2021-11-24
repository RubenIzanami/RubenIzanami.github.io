## HeapAlloc

###  默认堆

进程初始化的时候，系统会给进程地址空间中创建一个堆。这个堆被称为进程默认堆（default heap）。这个堆的默认大小是1MB，我们也可以使用/HEAP开关在编译的时候调整这个区域的大小。

默认堆是线程安全的，只有当一个线程的分配完成时，才会轮到下一个线程进行分配。这会对性能产生轻微的影响。如果想要有更快的访问速度，我们可以自己申请自己堆而不使用默认堆。

可以使用`GetProcessHeap`来获取默认堆的句柄。

### 私有堆

私有堆有许多好处，如能对组件进行保护、更有效的内存管理、使内存访问局部化、避免线程同步的开销、快速释放。

调用`HeapCreate`创建私有堆。

```c++
HANDLE HeapCreate(
 DWORD fdwOptions,
 SIZE_T dwInitialSize,
 SIZE_T dwMaximumSize
);
```

