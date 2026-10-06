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
 ```       
total time:10
CPU utilization:100%
- Reasoning / 理由:两个进程全部为CPU指令，没有I/O操作。调度器在进程执行完CPU指令后切换，CPU全程不会空闲，总耗时等于两个进程CPU指令相加。
- Verified result / 验证结果:
Stats: Total Time 10
Stats: CPU Busy 10 (100.00%)
Stats: IO Busy  0 (0.00%)
- Analysis / 分析:两个进程都只用cpu时，cpu不会空闲
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
 ```
total time:11
CPU utilization:6/11
- Reasoning / 理由:PID0先执行全部CPU指令，PID1要等PID0结束才获得CPU。PID1执行I/O，一次完整I/O需要7个tick。PID1进入阻塞后没有就绪进程可以运行，CPU空闲等待I/O完成。
- Verified result / 验证结果:Stats: Total Time 11
Stats: CPU Busy 6 (54.55%)
Stats: IO Busy  5 (45.45%)
- Analysis / 分析:进程执行过程中会触发I/O操作。当进程发起I/O请求时，它会立刻进入阻塞状态，CPU就调度给其他就绪进程；等I/O操作完成后，原来的进程回到就绪队列，等待再次获得CPU。因此I/O等待期间CPU可以执行别的任务，提升CPU利用率，整体总运行时间会缩短。
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
  ```
total time:7
CPU utilization:6/7
- Reasoning / 理由:PID0发起I/O之后进入阻塞。在I/O阻塞的时间片，CPU可以调度运行PID1的CPU任务。I/O设备工作与CPU计算并行，充分利用CPU资源，缩短整体总时间。
- Verified result / 验证结果:Stats: Total Time 7
Stats: CPU Busy 6 (85.71%)
Stats: IO Busy  5 (71.43%)
- Analysis / 分析:采用轮转调度（Round-Robin），设置固定时间片。每当进程用完分配的时间片，无论任务有没有做完，都会被调度器换下CPU，就绪队列里下一个进程获得CPU。频繁的进程切换会带来额外开销，会让整体完成时间变长，CPU利用率相比连续执行会下降。
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
 ```           
total time:11
CPU utilization:6/11
- Reasoning / 理由:使用 SWITCH_ON_END 策略，只有进程全部指令结束才切换。PID0发起I/O的时候不会触发上下文切换，PID1保持就绪。CPU空转等待PID0完整完成I/O整套流程，之后才运行PID1。
- Verified result / 验证结果:Stats: Total Time 11
Stats: CPU Busy 6 (54.55%)
Stats: IO Busy  5 (45.45%)
- Analysis / 分析:进程包含CPU计算与I/O交替执行的行为。每当进程发起I/O，进程阻塞，CPU交给其他就绪进程；I/O结束后进程重新回到就绪队列等待。多个进程的I/O等待时间可以相互重叠，这是提升CPU利用率的核心。
## Q5
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
  ```
total time:7
CPU utilization:6/7
- Reasoning / 理由:开启I/O发生时切换策略，进程发起I/O就切换到就绪的其他进程。I/O设备工作期间CPU执行PID1的计算任务，硬件和CPU工作重叠，减少总运行时长，提高CPU利用率。
- Verified result / 验证结果:Stats: Total Time 7
Stats: CPU Busy 6 (85.71%)
Stats: IO Busy  5 (71.43%)
- Analysis / 分析:同样是轮转调度，缩短时间片。时间片越小，进程切换就会越频繁。上下文切换带来的损耗占比上升，会拉高整体总完成时间，CPU利用率进一步降低。时间片长短直接影响切换次数。
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
 31    RUN:io_done          DONE          DONE          DONE              1 
```
total time:31
CPU utilization:21/31     
- Reasoning / 理由:调度策略 SWITCH_ON_IO 、 IO_RUN_LATER 。PID0每次发起I/O就切换其他CPU‑bound进程；I/O硬件完成后PID0仅进入READY就绪队列，不会抢占CPU，需要等到调度轮到它才可以继续执行。
- Verified result / 验证结果:Stats: Total Time 31
Stats: CPU Busy 21 (67.74%)
Stats: IO Busy  15 (48.39%)
- Analysis / 分析:这里采用的调度策略是一旦进程开始运行，就持续执行直到进程主动放弃CPU（进程结束或者发起I/O），不会因为时间片被抢占。只有进程触发I/O或者运行结束，才会发生调度切换，属于非抢占式调度。
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
 ```     
total time:21
CPU utilization:100%
- Reasoning / 理由:使用 IO_RUN_IMMEDIATE ，一旦I/O硬件完成，PID0立刻抢占CPU处理I/O完成。不用在就绪队列排队，减少I/O进程的等待延迟，能够尽快发起下一轮I/O，提升I/O设备利用效率。
- Verified result / 验证结果:Stats: Total Time 21
Stats: CPU Busy 21 (100.00%)
Stats: IO Busy  15 (71.43%)
- Analysis / 分析:当进程完成I/O之后，需要等到当前正在CPU上运行的进程主动让出CPU，才能得到调度。即便I/O已经完成、进程变为就绪状态，也不能打断当前正在运行的进程。非抢占特性导致就绪进程需要等待，会拉长响应时间。
## Q8
- Prediction / 预测:
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
 ```
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
 ```
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
 ``` 
 total time:18
CPU utilization:50%
- Reasoning / 理由:不同随机种子生成不一样的CPU、I/O指令序列。指令序列决定什么时候发生I/O；调度策略控制I/O发生、I/O完成时如何切换进程，从而造成总时间、CPU利用率出现差异。预测基于对应种子生成的指令流分析进程状态变化。
- Verified result / 验证结果:
s1:
Stats: Total Time 15
Stats: CPU Busy 8 (53.33%)
Stats: IO Busy  10 (66.67%)
s2：
Stats: Total Time 16
Stats: CPU Busy 10 (62.50%)
Stats: IO Busy  14 (87.50%)
s3：
Stats: Total Time 18
Stats: CPU Busy 9 (50.00%)
- Analysis / 分析: ‑s 随机种子决定CPU、I/O的指令序列； ‑S 与 ‑I 仅改变调度规则，不会修改指令流。 SWITCH_ON_END 不在I/O发生时切换进程，容易产生CPU空闲； IO_RUN_IMMEDIATE 更适合I/O密集的任务。不同种子产生不同指令，因此总运行时间会发生变化。