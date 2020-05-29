# Storage performance test:

1. fio:
vmstat 2 check cpu info
用法

      fio分顺序读  -rw=read ，随机读 -rw=randread，顺序写 -rw=write，随机写-rw=randwrite ，混合随机读写模式  -rw=randrw

参数：

       filename: 指定文件(设备)的名称。可以通过冒号分割同时指定多个文件，如filename=/dev/sda:/dev/sdb。
    
       directory: 设置filename的路径前缀。在后面的基准测试中，采用这种方式来指定设备。
    
       name: 指定job的名字，在命令行中表示新启动一个job。
    
       direct: bool类型，如果设置成true (1)，表示不使用io buffer。
    
        ioengine: I/O引擎，现在fio支持19种ioengine。默认值是sync同步阻塞I/O，libaio是Linux的native异步I/O。
    
       iodepth: 如果ioengine采用异步方式，该参数表示一批提交保持的io单元数。该参数可参考文章“Fio压测工具和io队列深度理解和误区”。
    
       rw: I/O模式，随机读写，顺序读写等等。
    
       bs: I/O block大小，默认是4k。
    
       size: 指定job处理的文件的大小。
    
       numjobs: 指定job的克隆数(线程)。
    
       time_based: 如果在runtime指定的时间还没到时文件就被读写完成，将继续重复知道runtime时间结束。
    
       runtime: 指定在多少秒后停止进程。如果未指定该参数，fio将执行至指定的文件读写完全完成。
    
       group_reporting: 当同时指定了numjobs了时，输出结果按组显示。

下面是带数字的解释：
```
   **direct=1             #测试过程绕过机器自带的buffer。使测试结果更真实**
   rw=randwrite         #测试随机写的I/O
   rw=randrw            #测试随机写和读的I/O
   **bs=16k               #单次io的块文件大小为16k**
   bsrange=512-2048     #同上，提定数据块的大小范围
   **size=5G              #本次的测试文件大小为5g，以每次4k的io进行测试**
   numjobs=30           #本次的测试线程为30个
   runtime=1000         #测试时间1000秒，如果不写则一直将5g文件分4k每次写完为止
   ioengine=psync       #io引擎使用psync方式
   rwmixwrite=30        #在混合读写的模式下，写占30%
   group_reporting      #关于显示结果的，汇总每个进程的信息
   lockmem=1G           #只使用1g内存进行测试
   zero_buffers         #用0初始化系统buffer
   nrfiles=8            #每个进程生成文件的数量
```

dd命令测试的硬盘性能

•使用dd命令来测量服务器的吞吐率（写速度) dd if=/dev/zero of=/tmp/test1.img bs=1G count=1 oflag=dsync

•使用dd命令测量服务器延迟 dd if=/dev/zero of=/tmp/test2.img bs=512 count=1000 oflag=dsync

•if=/dev/zero (if=/dev/input.file) ：用来设置dd命令读取的输入文件名。 

•of=/tmp/test1.img (of=/path/to/output.file)：dd命令将input.file写入的输出文件的名字。 

•bs=1G (bs=block-size) ：设置dd命令读取的块的大小。例子中为1个G。 

•count=1 (count=number-of-blocks)：dd命令读取的块的个数。 

•oflag=dsync (oflag=dsync) ：使用同步I/O。不要省略这个选项。这个选项能够帮助你去除caching的影响，以便呈现给你精准的结果。 

•conv=fdatasyn: 这个选项和oflag=dsync含义一样
