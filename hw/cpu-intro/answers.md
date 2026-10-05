# OSTEP Ch.4 Homework Answers
## Q1
- Prediction / 预测:
```
Time        PID: 0        PID: 1           CPU           IOs
  1        RUNNING         READY             1          
  2        RUNNING         READY             1          
  3        RUNNING         READY             1          
  4        RUNNING         READY             1          
  5        RUNNING         READY             1          
  6           DONE       RUNNING             1          
  7           DONE       RUNNING             1          
  8           DONE       RUNNING             1          
  9           DONE       RUNNING             1          
 10           DONE       RUNNING             1          
total time:10
CPU utilization:100%
- Reasoning / 理由:两个进程全部为CPU指令，没有I/O操作。调度器在进程执行完CPU指令后切换，CPU全程不会空闲，总耗时等于两个进程CPU指令相加。
- Verified result / 验证结果:
- Analysis / 分析:
## Q2
- Prediction / 预测:
```       
Time        PID: 0        PID: 1           CPU           IOs
  1        RUNNING         READY             1          
  2        RUNNING         READY             1          
  3        RUNNING         READY             1          
  4        RUNNING         READY             1          
  5           DONE        RUN:io             1          
  6           DONE       BLOCKED                           1
  7           DONE       BLOCKED                           1
  8           DONE       BLOCKED                           1
  9           DONE       BLOCKED                           1
 10           DONE       BLOCKED                           1
 11           DONE   RUN:io_done             1          
total time:11
CPU utilization:6/11
- Reasoning / 理由:PID0先执行全部CPU指令，PID1要等PID0结束才获得CPU。PID1执行I/O，一次完整I/O需要7个tick。PID1进入阻塞后没有就绪进程可以运行，CPU空闲等待I/O完成。
- Verified result / 验证结果:
- Analysis / 分析:
## Q3
- Prediction / 预测:
```
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED       RUNNING             1             1
  3        BLOCKED       RUNNING             1             1
  4        BLOCKED       RUNNING             1             1
  5        BLOCKED       RUNNING             1             1
  6        BLOCKED          DONE                           1
  7    RUN:io_done          DONE             1          
total time:7
CPU utilization:6/7
- Reasoning / 理由:PID0发起I/O之后进入阻塞。在I/O阻塞的时间片，CPU可以调度运行PID1的CPU任务。I/O设备工作与CPU计算并行，充分利用CPU资源，缩短整体总时间。
- Verified result / 验证结果:
- Analysis / 分析:
## Q4
- Prediction / 预测:
```
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED         READY                           1
  3        BLOCKED         READY                           1
  4        BLOCKED         READY                           1
  5        BLOCKED         READY                           1
  6        BLOCKED         READY                           1
  7    RUN:io_done         READY             1          
  8           DONE       RUNNING             1          
  9           DONE       RUNNING             1          
 10           DONE       RUNNING             1          
 11           DONE       RUNNING             1             
total time:11
CPU utilization:6/11
- Reasoning / 理由:使用 SWITCH_ON_END 策略，只有进程全部指令结束才切换。PID0发起I/O的时候不会触发上下文切换，PID1保持就绪。CPU空转等待PID0完整完成I/O整套流程，之后才运行PID1。
- Verified result / 验证结果:
- Analysis / 分析:
## Q5
```
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED       RUNNING             1             1
  3        BLOCKED       RUNNING             1             1
  4        BLOCKED       RUNNING             1             1
  5        BLOCKED       RUNNING             1             1
  6        BLOCKED          DONE                           1
  7    RUN:io_done          DONE             1              
total time:7
CPU utilization:6/7
- Prediction / 预测:
- Reasoning / 理由:开启I/O发生时切换策略，进程发起I/O就切换到就绪的其他进程。I/O设备工作期间CPU执行PID1的计算任务，硬件和CPU工作重叠，减少总运行时长，提高CPU利用率。
- Verified result / 验证结果:
- Analysis / 分析:
## Q6
- Prediction / 预测:
```
Time        PID: 0        PID: 1        PID: 2        PID: 3           CPU           IOs
  1         RUN:io         READY         READY         READY             1          
  2        BLOCKED       RUNNING         READY         READY             1             1
  3        BLOCKED       RUNNING         READY         READY             1             1
  4        BLOCKED       RUNNING         READY         READY             1             1
  5        BLOCKED       RUNNING         READY         READY             1             1
  6        BLOCKED       RUNNING         READY         READY             1             1
  7          READY          DONE       RUNNING         READY             1          
  8          READY          DONE       RUNNING         READY             1          
  9          READY          DONE       RUNNING         READY             1          
 10          READY          DONE       RUNNING         READY             1          
 11          READY          DONE       RUNNING         READY             1          
 12          READY          DONE          DONE       RUNNING             1          
 13          READY          DONE          DONE       RUNNING             1          
 14          READY          DONE          DONE       RUNNING             1          
 15          READY          DONE          DONE       RUNNING             1          
 16          READY          DONE          DONE       RUNNING             1          
 17    RUN:io_done          DONE          DONE          DONE             1          
 18         RUN:io          DONE          DONE          DONE             1          
 19        BLOCKED          DONE          DONE          DONE                           1
 20        BLOCKED          DONE          DONE          DONE                           1
 21        BLOCKED          DONE          DONE          DONE                           1
 22        BLOCKED          DONE          DONE          DONE                           1
 23        BLOCKED          DONE          DONE          DONE                           1
 24    RUN:io_done          DONE          DONE          DONE             1          
 25         RUN:io          DONE          DONE          DONE             1          
 26        BLOCKED          DONE          DONE          DONE                           1
 27        BLOCKED          DONE          DONE          DONE                           1
 28        BLOCKED          DONE          DONE          DONE                           1
 29        BLOCKED          DONE          DONE          DONE                           1
 30        BLOCKED          DONE          DONE          DONE                           1
 31    RUN:io_done          DONE          DONE          DONE             1       
total time:31
CPU utilization:21/31     
- Reasoning / 理由:调度策略 SWITCH_ON_IO 、 IO_RUN_LATER 。PID0每次发起I/O就切换其他CPU‑bound进程；I/O硬件完成后PID0仅进入READY就绪队列，不会抢占CPU，需要等到调度轮到它才可以继续执行。
- Verified result / 验证结果:
- Analysis / 分析:
## Q7
- Prediction / 预测:
```
Time        PID: 0        PID: 1        PID: 2        PID: 3           CPU           IOs
  1         RUN:io         READY         READY         READY             1          
  2        BLOCKED       RUNNING         READY         READY             1             1
  3        BLOCKED       RUNNING         READY         READY             1             1
  4        BLOCKED       RUNNING         READY         READY             1             1
  5        BLOCKED       RUNNING         READY         READY             1             1
  6        BLOCKED       RUNNING         READY         READY             1             1
  7    RUN:io_done          DONE         READY         READY             1          
  8         RUN:io          DONE         READY         READY             1          
  9        BLOCKED          DONE       RUNNING         READY             1             1
 10        BLOCKED          DONE       RUNNING         READY             1             1 
 11        BLOCKED          DONE       RUNNING         READY             1             1
 12        BLOCKED          DONE       RUNNING         READY             1             1
 13        BLOCKED          DONE       RUNNING         READY             1             1
 14    RUN:io_done          DONE          DONE         READY             1          
 15         RUN:io          DONE          DONE         READY             1          
 16        BLOCKED          DONE          DONE       RUNNING             1             1
 17        BLOCKED          DONE          DONE       RUNNING             1             1
 18        BLOCKED          DONE          DONE       RUNNING             1             1
 19        BLOCKED          DONE          DONE       RUNNING             1             1
 20        BLOCKED          DONE          DONE       RUNNING             1             1
 21    RUN:io_done          DONE          DONE          DONE             1          
- Reasoning / 理由:使用 IO_RUN_IMMEDIATE ，一旦I/O硬件完成，PID0立刻抢占CPU处理I/O完成。不用在就绪队列排队，减少I/O进程的等待延迟，能够尽快发起下一轮I/O，提升I/O设备利用效率。
- Verified result / 验证结果:
- Analysis / 分析:
## Q8
- Prediction / 预测:
```
(s1)
```
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED       RUNNING             1             1
  3        BLOCKED        RUN:io             1             1
  4        BLOCKED       BLOCKED                           2
  5        BLOCKED       BLOCKED                           2
  6        BLOCKED       BLOCKED                           2
  7    RUN:io_done       BLOCKED             1             1
  8         RUN:io       BLOCKED             1             1
  9        BLOCKED   RUN:io_done             1             1
 10        BLOCKED        RUN:io             1             1
 11        BLOCKED       BLOCKED                           2
 12        BLOCKED       BLOCKED                           2
 13        BLOCKED       BLOCKED                           2
 14    RUN:io_done       BLOCKED             1             1
 15        RUNNING       BLOCKED             1             1
 16           DONE   RUN:io_done             1            
 total time:16
CPU utilization:10/16
(s2)
```
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED       RUNNING             1             1
  3        BLOCKED        RUN:io             1             1
  4        BLOCKED       BLOCKED                           2
  5        BLOCKED       BLOCKED                           2
  6        BLOCKED       BLOCKED                           2
  7    RUN:io_done       BLOCKED             1             1
  8         RUN:io       BLOCKED             1             1
  9        BLOCKED   RUN:io_done             1             1
 10        BLOCKED        RUN:io             1             1
 11        BLOCKED       BLOCKED                           2
 12        BLOCKED       BLOCKED                           2
 13        BLOCKED       BLOCKED                           2
 14    RUN:io_done       BLOCKED             1             1
 15        RUNNING       BLOCKED             1             1
 16           DONE   RUN:io_done             1          
 total time:16
CPU utilization:10/16
(s3)
```
Time        PID: 0        PID: 1           CPU           IOs
  1        RUNNING         READY             1          
  2         RUN:io         READY             1          
  3        BLOCKED        RUN:io             1             1
  4        BLOCKED       BLOCKED                           2
  5        BLOCKED       BLOCKED                           2
  6        BLOCKED       BLOCKED                           2
  7        BLOCKED       BLOCKED                           2
  8    RUN:io_done       BLOCKED             1             1
  9        RUNNING         READY             1           
 10           DONE   RUN:io_done             1          
 11           DONE        RUN:io             1          
 12           DONE       BLOCKED                           1
 13           DONE       BLOCKED                           1
 14           DONE       BLOCKED                           1
 15           DONE       BLOCKED                           1
 16           DONE       BLOCKED                           1
 17           DONE   RUN:io_done             1          
 18           DONE       RUNNING             1          
 total time:18
CPU utilization:50%
- Reasoning / 理由:不同随机种子生成不一样的CPU、I/O指令序列。指令序列决定什么时候发生I/O；调度策略控制I/O发生、I/O完成时如何切换进程，从而造成总时间、CPU利用率出现差异。预测基于对应种子生成的指令流分析进程状态变化。
- Verified result / 验证结果:
- Analysis / 分析: