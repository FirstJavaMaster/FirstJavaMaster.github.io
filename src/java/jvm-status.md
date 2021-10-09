## JVM运行状态检查

### 其他

#### jps -l

查看本地运行的java进程

#### jps | awk '{print $1}' | xargs -i sh -c 'echo -e "{}\t\c" && pstree -p {} | wc -l'

检查线程数

#### jinfo -flags pid

查询虚拟机运行参数信息

### jmap 相关

#### jmap -heap pid

输出堆内存设置和使用情况

#### jmap -histo pid | head -n 50

输出heap的直方图，包括类名，对象数量，对象占用大小

#### jmap -histo:live pid | head -n 50

同上, 但是只会显示存活的对象信息

### jstat 相关

#### jstat -class pid

输出加载类的数量及所占空间信息. 比较简略.

#### jstat -gc pid

输出gc信息, 包括gc次数和时间, 内存使用状况. 比较简略.

### CPU占用过高问题排查

1. top命令查看是java进程的pid
2. 查看具体的线程状态: `ps -mp pid -o THREAD,tid,time`
3. 打印堆栈信息: `jstack pid |grep tid`. tid是16进制的线程号. 实际执行的命令则常见为: `jstack pid |grep $(printf "%x\n" tid)`
4. 查看内存使用的情况: `jstat -gcutil pid 2000 10`. 能看到堆内存中的新生代和老生代情况









