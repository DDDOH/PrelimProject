Slides

Background

问题（订单激增，但是现在随机筛选，服务不保证）

假设：司机分两种

旧的方案：给定DC，

把地图分成若干块，每天只server一部分块的订单，并且完全serve，司机不够就用临时工

在给定需要serve的块以及DC的时候，最小化司机的成本等价于一个VRP

multi depot，然后相当于single depot，一个起点，这个起点到multi real depot的距离都是0（这样的话可能车从real depot A出发最终回到real depot B，但这个无所谓其实）

实验做小规模的（5辆车 + 100个node差不多），

不用地图，距离不一定是euclidean distance，但是距离矩阵对称

---

实验1:正常情况，小订单，司机够，全能cover

100个订单，735

---

实验2:订单多了（300个），设定一数字，只接100个订单，随机从300个里选100个，然后司机也够（超出全职司机的部分用兼职司机填）

300个里抽100个，699米（每天1/3的概率买到，平均下来需要3天买到）（E = 1/p, Var也可以算，二项分布吧大概（但300这个也是随机的，所以更复杂一点））

---

实验3: 提前确定订单：车辆数量无限制，然后用普通车去跑，然后再把这些路径分到每天去

300个订单全跑，1628米，

每天能力上限700米(比实验2更少的工作量取得更好的效果），哪几辆车放到同一天里就是一个bin packing problem了

需要两天能送完这些订单

这样相当于提前确定订单，两天之内肯定能serve到一次，这个相对于实验2在服务水平上提升就非常多了

---

实验4: 订单多了，然后订单服从xx分布，sample 多次node，看看分车有没有规律

这个要求的计算量特别大，再看看吧





---



实验5:订单多了（200个），设定范围，这个范围内的订单都cover（地图分块，分几块还没想好），然后一个周期是X天（7最好，但实际可能7太大了，设个3或者4之类的），一个周期之内至少serve一次，就相当于决策变量：x_ij，i是地图块的index，j是周期内第几天，等于1表示这个地图块在这一天serve了，约束是x_ij 对j求和必须大于等于1，相当于一个周期内必须serve一次，然后司机也够（超出全职司机的部分用兼职司机填），如果3*3的划分，然后周期是7的话，相当于2^9 * 7 大约 3600种方案，可以接受



实验6: 随机订单：对density分类（随机产生很多点），用K-means（balance K-means？），用x loc，y loc，与DC1的距离，与DC2的距离，然后与DC的距离可能要乘一个权重系数（这样不光参考绝对位置，也参考与dc的相对位置）（这个系数巨大的时候，就相当于分类只考虑与DC的距离不考虑相对位置了），





report：总成本（根据总距离算），服务availability（期望和方差），





然后最后提一下有estimation+ML的方法

先看下LRP estimation的论文 





这个time limit或者node limit能不能说，达到之后依然输出一个解？而不是failed？这个是有可能的，因为这个链接：

https://developers.google.com/optimization/routing/tsp#search_strategy

<img src="/Users/shuffle_new/Library/Application Support/typora-user-images/image-20200430175042201.png" alt="image-20200430175042201" style="zoom:33%;" />

