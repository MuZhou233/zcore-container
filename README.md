## zCore 的容器功能设计思路

### 21-08-11

目前进程只能通过创建时的channel通信所以 PID namespace 和 IPC namespace 暂时不实现  
目前[mountfs](https://github.com/rcore-os/rcore-fs/tree/master/rcore-fs-mountfs)实现了挂载功能，可以侧重这一点分析如何实现Mount namespace

### 21-08-09

Mount namespace 是六个 Linux namespace 中最早实现的，我们可以也从文件隔离开始尝试。要了解的是zCore中挂载点是怎么实现的，目录结构是怎样的，如何实现目录映射。  
关于PID和IPC的隔离首先要分析zCore中进程能够通过哪些方式进行通信，这方面似乎已经有了一定程度的隔离。  
关于网络方面的隔离要了解zCore中网络部分的实现情况，感觉会比较难。  

**如何进行验证？**

一个比较理想的状态是不同容器中的应用既可以正常工作又完全无法互相影响，容器间的通信仅限网络的形式。  
验证方式可以是编写三个应用，分别位于宿主和两个容器中，应用尝试使用各种方式互相通信，以成功通信的方式、次数及数据完整度判断容器的健全程度。