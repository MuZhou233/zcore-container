## cgroups

Cgroup 是 control group 的简写，用于限制和隔离一组进程对系统资源的使用，也就是做资源QoS，这些资源主要包括CPU、内存、存储、网络等。通过Cgroups，可以方便地限制某个进程的资源占用，并且可以实时地监控进程的监控和统计信息。  
Cgroups提供了以下四大功能:

- 资源限制（Resource Limitation）：cgroups可以对进程组使用的资源总额进行限制。如设定应用运行时使用内存的上限，一旦超过这个配额就发出OOM（Out of Memory）。
- 优先级分配（Prioritization）：通过分配的CPU时间片数量及硬盘IO带宽大小，实际上就相当于控制了进程运行的优先级。
- 资源统计（Accounting）： cgroups可以统计系统的资源使用量，如CPU使用时长、内存用量等等，这个功能非常适用于计费。
- 进程控制（Control）：cgroups可以对进程组执行挂起、恢复等操作。

### cgroups 中的三个组件

- cgroup 控制组。cgroup 是对进程分组管理的一种机制，一个cgroup包含一组进程，并可以在这个cgroup上增加Linux subsystem的各种参数的配置，将一组进程和一组subsystem的系统参数关联起来。
- hierarchy 层级树。hierarchy 的功能是把一组cgroup串成一个树状的结构，一个这样的树便是一个hierarchy，通过这种树状的结构，Cgroups可以做到继承。比如我的系统对一组定时的任务进程通过cgroup1限制了CPU的使用率，然后其中有一个定时dump日志的进程还需要限制磁盘IO，为了避免限制了影响到其他进程，就可以创建cgroup2继承于cgroup1并限制磁盘的IO，这样cgroup2便继承了cgroup1中的CPU的限制，并且又增加了磁盘IO的限制而不影响到cgroup1中的其他进程。
- subsystem 子系统 / controller (列表不完整)。
    - devices：设备权限控制。
    - cpuset：分配指定的CPU和内存节点。
    - cpu：控制CPU占用率。
    - cpuacct：统计CPU使用情况。
    - memory：限制内存的使用上限。
    - freezer：冻结（暂停）Cgroup中的进程。
    - net_cls：配合tc（traffic controller）限制网络带宽。
    - net_prio：设置进程的网络流量优先级。
    - huge_tlb：限制HugeTLB的使用。
    - perf_event：允许Perf工具基于Cgroup分组做性能监测。

1. 系统在创建了新的hierarchy之后，系统中所有的进程都会加入这个hierarchy的cgroup根节点，这个cgroup根节点是hierarchy默认创建的。
1. 一个subsystem只能附加到一个hierarchy上面。
1. 一个hierarchy可以附加多个subsystem。
1. 一个进程可以作为多个cgroup的成员，但是这些cgroup必须在不同的hierarchy中。
1. 一个进程fork出子进程时，子进程是和父进程在同一个cgroup中的，也可以根据需要将其移动到其他cgroup中。

### cgroups v2

cgroups v2 从 Linux 内核的 4.5 版本开始可用。以下是Cgroups v2的五点改进：

- Cgroups v2 中所有的controller都会被挂载到一个unified hierarchy下，不在存在像v1中允许不同的controller挂载到不同的hierarchy的情况
- Proess只能绑定到cgroup的根(“/“)目录和cgroup目录树中的叶子节点
- 通过cgroup.controllers和cgroup.subtree_control指定哪些controller可以被使用
- v1版本中的task文件和cpuset controller中的cgroup.clone_children文件被移除
- 当cgroup为空时的通知机制得到改进，通过cgroup.events文件通知

### 参考文献

- 自己动手写Docker - 陈显鹭
- https://segmentfault.com/a/1190000016355300
- https://www.lijiaocn.com/%E6%8A%80%E5%B7%A7/2019/01/28/linux-tool-cgroup-detail.html