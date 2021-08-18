## mount-fs 及其在内核中的应用

*基于2021-08-15代码*

从底层的实现开始通过函数调用信息了解 mount-fs 在内核中的应用。  
mount-fs 定义在单独的 rcore-fs crate 中，在 [`MountFS`](https://github.com/rcore-os/rcore-fs/blob/6df6cd24d62e7b0b8c2a498d7addce1acb843e57/rcore-fs-mountfs/src/lib.rs#L20-L29) 结构体中实现了挂载点。  

追踪 `MountFS::new` 方法，可以发现在 `linux-object/src/fs/mod.rs` 中的 [`create_root_fs`](https://github.com/rcore-os/zCore/blob/ebd09da782c9474b0e14645cc3dc09d290b5aca8/linux-object/src/fs/mod.rs#L104-L139) 函数中使用该方法创建根文件系统。同时可以发现 MountFS 实例化使用的唯一一个参数在这个函数中也是通过参数传入的，我们需要知道那个参数是如何生成的。  
之后是在 `linux-object/src/process.rs` 中的 [`LinuxProcess::new`](https://github.com/rcore-os/zCore/blob/ebd09da782c9474b0e14645cc3dc09d290b5aca8/linux-object/src/process.rs#L204-L240) 中使用 `crate_root_fs` 创建 LinuxProcess 实例。在 `ProcessExt::create_linux` 中包装为 zircon_object 的接口。  
在 `linux-loader/src/lib.rs` 中 [`run`](https://github.com/rcore-os/zCore/blob/ebd09da782c9474b0e14645cc3dc09d290b5aca8/linux-loader/src/lib.rs#L34) 函数创建并启动了进程。  
在 `linux-loader/src/main.rs` 中可以看到两个调用 run 函数的地方传递的 rootfs 参数都是 HostFS 实例。在 `zCore/src/main.rs` 中的 (feature = "linux")main 函数调用 run 函数的地方传递的 rootfs 参数是 SimpleFileSystem 实例。