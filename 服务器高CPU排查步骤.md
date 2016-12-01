第一步
------------
* 利用 top -c 命令

  > 此命令可以查询出当前服务器上 CPU 占用率从高到低的进程信息包含以及进程对应的路径

第二步
------------
* 利用 ps 命令

  > ps -mp PID(进程ID) -o THREAD,tid,time 
  >
  > 此命令可以查询 PID 对应的进程中的线程 CPU 占用率情况，线程ID等，找到高CPU的线程ID

第三步
-------------
* 利用 printf "%x\n" tid 将线程ID 转换为 16 进制

第四步
-------------
* 利用 jstack 命令查看进程内线程信息

  > sudo -s  切换到超级用户
  >
  > sudo -u www-data jstack <pid> | grep <tid> -A 30
  >
  > 切换到超级用户 在 www-data 用户状态利用 jstack 命令查询线程情况。
  >
  > 切记不可直接用 jstack -F 打印，因为 jstack -F 命令会导致系统的所有线程停止而进行 threaddump .
  >
  >
  >
 
