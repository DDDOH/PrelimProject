[TOC]

# Partition VRP for Online Order Delivery

## An Overview of WEEE!'s Problem

### About WEEE!

WEEE! is a e-commerce located in the San Francisco Bay Area.

Its main products are fresh food, like fruits, vegetables, meats, etc.

Figure[]: Supply chain of WEEE: Supplier --> warehouse + distribution center --> customers

To deliver goods from DC to customers, they hired a fleed that distrubutes goods to customer's homes.

### Their problem

The customer places an order online and will get delievered tommorrow.

Figure: Normal time, 前一天下单，然后从早到晚的一个list，所有订单都接.旁边是一个地图，都接

Due to COVID-19, people stays at home, the number of orders surged a lot in these days. Since there's limited number of drivers and cars available, this caused a big pressure on the distribution team. 

### Current Status

They set a maximum number of orders servered each day. Only the first 500 orders can be accepted, for example.



Because of the accumulation of distribution tasks, even if the order is successfully placed, It will take one to two weeks for the delivery team to complete the earlier order.

Figure: 前一天下单，然后从早到晚的一个list，只选中最早的一部分.旁边是一个地图，接受的订单和没接受的订单

1. 



Drawback: 

1. For customer, service level is low: They need to get up early in the moring to place an order successfully, and they have to wait for a long time to receive the goods.
2. For company, delivery work load isn't reduced too much: When we select the orders randomly, the distrubution of nodes can still be very sparse, which means drivers still need to drive around the whole city

> ### Questions
>
> 1. How to select orders to be served? ---- Partition (Herein, partition means how to select orders to be served from all orders received.  The company's current partition plan is to keep some of the earliest orders placed each day.)
> 2. How to measure the performance?
>



### How to optimize?

The intuition is we want to be more focus. Instead of make the drivers hang around the whole area, we want to make them being more focus on a certain region each day. In a service cycle, each region will be served in turn.



[figure] 一张图 分成几块，然后标Day1 day2 day3





### Our results

Two/Three models:

1. Periodic Partition VRP(Vehicle Routing Problem)
2. Density based Partition VRP

## Periodic Partition VRP

### Assumptions

(Before we go futher, let's cleartify some assumptions.)

1. Distribution Center (Depot): DC is also called depot in VRP literature. The company have several DC, and each DC stores all kinds of goods. No capacity constraint.

2. Orders: 

   1. Weight, Can be delivered the whole day. Destination. We can calculate distance between different orders or distance between orders and DC easily. (This distance can be real driving distance or simply euclidean distance)
   2. No backlog: If the order is rejected, it will disapper and will not backlog into the future.

3. Vehicle: Capacity. (There's a maximum weight that a vehicle can contains at a same time.)

4. Driver

   1. Type 1: Full time driver. Work 8 hours everyday. Whether the driver works eight hours or not, we have to pay for the whole day. Denote salary per driver per day as $S_F$, number of full time driver hired as $N_F$.$N_F$ is fixed in a short time. because we need time to train a driver, and sign contract and so on.
   2. Type 2: Part time driver. Availble whenever needed. Higher hourly salary than full time driver. Salary grows linearly with the working hours. Denote salary per driver per hour as $S_P$.
   3. All drivers drive at the same speed $v$. [So driving time is proportional to total driving distance]

5. Cost: Driver's salary and fuel cost. Fuel cost each mile is $c_f$.

   When driving distance one day is $D$, total cost = $N_FS+(\frac{D}{v}-8N_F)^+S_P+Dc_f$.

6. Objective: 

   1. Serve more orders
   2. provide more specific delivery time commitment
   3. saving cost

$$
\begin{align*}
\text{OPT}(A)\min &\sum_{t=1}^{T_1+T_2}C_t
\\\text{s.t.}\quad& C_t=N_FS+(\frac{D_t}{v}-8N_F)^+S_P+D_tc_f&t=1,\ldots,T_1+T_2
\\&D_t=\sum_{i=1}^n\sum_{j=1}^n\sum_{k=1}^{NV}d_{ij}x_{ijk}^t&t=1,\ldots,T_1+T_2
\\&\sum_{i=1}^n\sum_{k=1}^{NV}\sum_{t=1}^{T_1+T_2}x_{ijk}^t=1 &j=M+1,\ldots,n
\\&\sum_{j=1}^n\sum_{k=1}^{NV}\sum_{t=1}^{T_1+T_2}x_{ijk}^t=1 &i=M+1,\ldots,n

\\&\sum_{i=1}^nx_{ipt}^k - \sum_{j=1}^nx_{pjt}^k=0 &k=1,\ldots,n, p=1,\ldots,n

\\&&t=1,\ldots,T_1+T_2
\\&\sum_{i=1}^n Q_i(\sum_{i=1}^n x_{ijt}^k)\le p_k &k=1,\ldots,NV, t=1,\ldots,T_1+T_2
\\&\sum_{i=1}^{M} \sum_{j=M+1}^{n} x_{i jt}^{k} \leq 1&k=1,\ldots,NV, t=1,\ldots,T_1+T_2
\\&\sum_{p=1}^{M} \sum_{i=M+1}^{n} x_{i 1t}^{k} \leq 1&k=1,\ldots,NV, t=1,\ldots,T_1+T_2
\\&x_{ijk}^t\in\{0,1\} \text{ for all } i,j,k
\end{align*}
$$



### Operation Step

（把current 的图也拿出来做个比较）

<img src="/Users/shuffle_new/Library/Application Support/typora-user-images/image-20200503043836929.png" alt="image-20200503043836929" style="zoom:30%;" />

The placform is Operated in a periodic manner. 

[这个地方感觉得加更多的解释，intuition啥的]

For example in the figure, T_1 is  2, and T_2 is 1. Deliver of orders will start right after opened days, and last for $T_1+T_2$ days. We need to decide how to deliver the orders received during the opened days. 

### Solving the model

because the new time dimension, the problem is more complicated than the original VRP problem.

1. First, without considering the problem of multiple days, solve the standard multi-depot VRP problem and deliver all orders with the shortest distance.
2. Multiple routes can be obtained in step 1, allocate these routes into $T_1+T_2$ days.

得加图，两部，两张图

To be more specific, in step 1 we solve the following model:
$$
\begin{align*}
\text{OPT}(A)\min &\sum_{i=1}^n\sum_{j=1}^n\sum_{k=1}^{NV(T_1+T_2)}d_{ij}x_{ij}^k
\\\text{s.t.}\quad&\sum_{i=1}^n\sum_{k=1}^{NV(T_1+T_2)}x_{ij}^k=1 &j=M+1,\ldots,n
\\&\sum_{j=1}^n\sum_{k=1}^{NV(T_1+T_2)}x_{ij}^k=1 &i=M+1,\ldots,n
\\&\sum_{i=1}^nx_{ip}^k - \sum_{j=1}^nx_{pj}^k=0 &k=1,\ldots,n,
\\&&p=1,\ldots,n
\\&\sum_{i=1}^n Q_i(\sum_{i=1}^n x_{ij}^k)\le P &k=1,\ldots,NV(T_1+T_2)
\\&\sum_{i=1}^{M} \sum_{j=M+1}^{n} x_{i j}^k \leq 1&k=1,\ldots,NV
\\&\sum_{i=1}^{M} \sum_{j=M+1}^{n} x_{ji}^{k} \leq 1&k=1,\ldots,NV
\\&x_{ij}^k\in\{0,1\} \text{ for all } i,j,k
\end{align*}
$$


Then we get several routes, denote as $R_1,\ldots,R_m$, and their length $L_1,\ldots,L_m$.

Solve the following model to allocate these route into $T_1+T_2$ days.


$$
\begin{align*}
\min &\sum_{t=1}^{T_1+T_2}C_t
\\\text{s.t.}\quad& \sum_{t=1}^{T_1+T_2}y_{it}=1&i=1,\ldots,m
\\&\sum_{i=1}^my_{it}L_i=D_t&t=1,\ldots,T_1+T_2
\\&C_t=N_FS+(\frac{D_t}{v}-8N_F)^+S_P+D_tc_f&t=1,\ldots,T_1+T_2
\\&y_{it}\in\{0,1\}&i=1,\ldots,m, t=1,\ldots,T_1+T_2
\end{align*}
$$




### Numerical Experiment



流程图：人口密度 然后生成订单 然后规划路线



Company's Current Strategy:

num of total orders per day: N(100,15)

num of accepted orders: 50



---

Set T_1 = 2, T_2 =1，fix full time worker的数量

Three depot

 

Number of orders in normal days: 50左右 成本是多少 加上那个流程图





Number of orders during Shelter-in-place: 100左右

正常情况下50个order

现在每天100个order

疫情时期，这个流程图也加上，现有策略：Keep 50





疫情时期，新的策略：

循环多次：

​	生成100个订单的位置。生成三天的数据

​	新的方案：1. Set T_1 = 2, T_2 =1，然后计算这个总成本，比那个现有方案稍微高一点的成本，但是服务的比率大幅提高



然后旧方案不同的keep数量，新方案T_1 T_2的不同组合，成本+订单保持比率的图，（帕累托散点图）



现有方案 60,70,80,90,100，成本和保持比率的帕累托图





### How to select T_1 and T_2

图：每个cycle的成本OPT_1 OPT_2

$\mathbb{E}(\text{OPT}_{T_1T_2})$

When demand is unknown, OPT_T_1T_2 is a random variable. 

Expected cost per day = $\frac{\mathbb{E}(\text{OPT}_{T_1T_2})}{T_1+T_2}$

然后加个图 T_1 T_2组合下的cost

但是T_2越大，被拒绝订单就越多

T_1+T_2越大，周期越长，一个订单要等待的时间就越久



## Prepare for Future

1. Opening new DC
2. Hiring more Full time driver



## Summary



## Future Research

## Q&A

