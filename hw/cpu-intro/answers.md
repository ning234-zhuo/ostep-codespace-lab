# OSTEP Ch.4 Homework Answers
## Q1
- Prediction / 预测：
总时间一共10个tick，CPU利用率100%。进程0先连续执行5个tick，执行完毕后切换到进程1，再连续执行5个tick。
- Reasoning / 理由：
两个进程全部是CPU指令，没有IO操作。默认规则是进程结束才切换，所以进程0一次性跑完5条指令，再运行进程1。CPU全程不会空闲。
- Verified result / 验证结果：

- Analysis / 分析：
仿真结果和预测完全一致。两个进程都是纯CPU计算任务，没有IO事件。默认策略SWITCH_ON_END，只有进程执行结束才切换。进程0跑完5tick之后，CPU切换给进程1继续跑5tick，总时长10tick，CPU全程处于工作状态，利用率100%，没有空闲时间。
## Q2
- Prediction / 预测：
进程0先连续运行3个tick执行完毕。之后切换到进程1，进程1执行1tick发起I/O，进入阻塞，I/O等待5tick，再用1tick处理io_done。总时间为3+1+5+1 = 10 tick；CPU在I/O阻塞阶段空闲。
- Reasoning / 理由：
进程0是纯CPU任务，3条指令一次性跑完。进程1唯一的指令是I/O操作。当进程1发起I/O后，进程阻塞，因为默认SWITCH_ON_END，**不会切换回进程0（进程0已经跑完）**，CPU只能等待I/O完成。I/O阻塞默认5个tick。
- Verified result / 验证结果：

- Analysis / 分析：
仿真结果和预测一致。进程0先跑完3条CPU指令。切换到进程1执行I/O，发起I/O之后进程1阻塞。由于进程0已经执行完成，没有其他就绪进程，CPU只能空闲等待I/O设备完成。I/O完成后，进程1执行io_done。I/O阻塞期间CPU利用率下降，出现空闲时间。
## Q3
- Prediction / 预测：
进程0执行1tick发起I/O，触发切换。进程0进入I/O阻塞（5tick），此时CPU切换到进程1，进程1在I/O等待的5tick内跑完3条CPU指令。I/O完成后，进程1已经结束，CPU执行io_done。总时间：1+5+1 =7 tick。
- Reasoning / 理由：
进程0第一条指令就发起I/O，触发进程切换。I/O阻塞的5个tick期间，CPU可以运行进程1的3条CPU指令。进程1在I/O等待阶段就全部跑完，不需要等I/O结束。
- Verified result / 验证结果：

- Analysis / 分析：
仿真结果和预测一致。进程0发起I/O时触发切换，在I/O阻塞的5个tick内，CPU并行运行进程1的3条CPU指令。充分利用了I/O等待时间，总时间相比Q2大幅缩短，CPU利用率更高。这就是CPU和I/O重叠执行带来的效率提升。
## Q4
- Prediction / 预测：
进程0执行1tick发起I/O，但是策略SWITCH_ON_END，不会切换进程。CPU原地等待I/O阻塞5tick，I/O完成执行io_done。进程0全部结束后，才切换运行进程1的3条指令。总时间=1+5+1+3 = 10 tick。
- Reasoning / 理由：
SWITCH_ON_END代表**仅进程结束时才切换**，即使进程发起I/O阻塞，CPU也不会切换到别的就绪进程，只能空闲等待I/O。I/O完成，进程0收尾完毕，才会调度进程1。
- Verified result / 验证结果：

- Analysis / 分析：
仿真结果和预测一致。SWITCH_ON_END策略下，只有进程执行完毕才切换。进程0发起I/O阻塞时CPU不会切换到进程1，只能空闲等待I/O。I/O结束、进程0全部执行完成后，CPU才运行进程1。对比Q3，总时间变长，CPU利用率下降，说明了I/O发生时及时切换进程的重要性。
## Q5
- Prediction / 预测：
进程0执行1tick发起I/O，触发切换。I/O阻塞的5tick期间，CPU运行进程1的3条指令。I/O完成后，进程1已经结束，执行io_done。总时间1+5+1 =7 tick，和Q3结果相同。
- Reasoning / 理由：
SWITCH_ON_IO代表进程发起I/O就切换。I/O等待的时间段CPU可以跑就绪的进程1，充分利用I/O等待时间，实现CPU与I/O重叠。
- Verified result / 验证结果：

- Analysis / 分析：
仿真结果和预测一致。SWITCH_ON_IO策略，进程发起I/O就立刻切换。I/O阻塞期间CPU可以执行进程1的任务，实现CPU与I/O并行。总耗时和Q3完全相同，该策略可以有效利用I/O等待时间，提升CPU利用率。
## Q6
- Prediction / 预测：
进程0执行1tick发起I/O，触发切换，CPU运行进程1。I/O在第6tick完成，IO_RUN_IMMEDIATE会立刻调度进程0执行io_done。之后再继续运行进程1剩下的指令。总时间依然是7 tick。
- Reasoning / 理由：
SWITCH_ON_IO：发起I/O就切换；IO_RUN_IMMEDIATE：I/O一完成，马上运行这个I/O进程，抢占CPU。本案例中进程1还没跑完，会被打断，但不改变总时长。
- Verified result / 验证结果：

- Analysis / 分析：
仿真结果和预测一致。SWITCH_ON_IO在发起I/O时切换进程；IO_RUN_IMMEDIATE表示I/O完成后立刻调度该进程。I/O完成后抢占CPU执行io_done，之后再继续执行进程1剩余指令。虽然进程1被中途打断，但总执行时间不变，体现了I/O完成抢占调度的特点。
## Q7
- Prediction / 预测：
进程0执行1tick发起I/O，切换到进程1运行。I/O在第6tick完成，IO_RUN_LATER不会抢占CPU，继续让进程1跑完剩下指令。进程1全部结束后，再执行进程0的io_done。总时间依旧7 tick。
- Reasoning / 理由：
SWITCH_ON_IO：发起I/O就切换。IO_RUN_LATER：I/O完成后，进程0进入就绪队列，不会抢占正在运行的进程1，等进程1执行完毕才调度进程0收尾。
- Verified result / 验证结果：

- Analysis / 分析：
仿真结果和预测一致。SWITCH_ON_IO策略在发起I/O时切换进程。IO_RUN_LATER代表I/O完成后不会抢占CPU，进程0加入就绪队列，等待当前进程1执行完毕后，才调度进程0执行io_done。对比Q6，可以看出IO_RUN_IMMEDIATE和IO_RUN_LATER两种I/O完成调度策略的区别。
## Q8
- Prediction / 预测：
使用不同随机种子(-s 1,2,3)，会生成不一样的指令序列，每条指令有50%概率是CPU指令或者I/O指令。
IO_RUN_IMMEDIATE：I/O完成立刻抢占CPU；IO_RUN_LATER：I/O完成不抢占，放到就绪队列末尾。
不同种子下，两种调度策略的总耗时、CPU利用率会产生差别。
- Reasoning / 理由：
参数3:50代表每个进程有3条指令，每条指令50%概率是I/O。随机种子决定随机序列。IO_RUN_IMMEDIATE会在I/O结束抢占CPU，IO_RUN_LATER不会抢占，等待当前进程执行完。
- Verified result / 验证结果：

- Analysis / 分析：
不同随机种子(-s 1,2,3)会生成不同的指令序列。50%概率代表每条指令随机为CPU指令或I/O指令。IO_RUN_IMMEDIATE在I/O完成后立刻抢占CPU，可能中断正在运行的进程；IO_RUN_LATER不会抢占，等待当前进程执行完毕再调度。
可以看到，随机生成的指令序列下，两种策略的CPU利用率、总执行时间会出现差异。当I/O指令比较多时，IO_RUN_IMMEDIATE可以更快响应I/O完成事件；IO_RUN_LATER实现简单，不会发生抢占。