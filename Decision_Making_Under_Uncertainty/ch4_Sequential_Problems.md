# ch4 Sequential Problems 完全可观察环境中的概率规划系统
第四五节好难。。。策略和值函数，有时间重看并记笔记
## 0. 背景
经典规划的基本假设
1. 有限系统
2. 完全可观察
3. 确定性，每个行动只会导致一种确定的影响
4. 静态性，环境的改变都来自agent的行动
5. 规划目标，目标是可到达的一些状态
6. 序列规划，规划结果是一个线性行动序列
7. 隐含时间，不考虑时间的连续性
8. 离线规划，规划求解器不考虑执行时的状态

例子有，积木世界，求解方法分为状态空间求解和规划空间求解

这节课讲的主要是概率规划，基于概率模型和效用函数，制定决策

将问题描述为MDP或POMDP

求解方法有离线规划：例如动态规划，在线规划：蒙特卡洛树搜索

书中的4-6章讨论序贯决策问题，第4章为模型已知，模型完全可观察(MDP规划)，第5章为模型未知，环境完全可观察(强化学习)，第6章为模型已知，环境部分可观察(POMDP规划)

## 1. Formulation
这一章的内容关于在模型已知且环境完全可观察时的序贯决策

### 1. Markov Decision Processes
#### 定义
马尔科夫假设：下一状态仅仅与当前观察和行动，与先前的所有观察和行动无关，$P(S_{t+1}=s', R_t=r|S_t=s, A_t=a)$

马尔科夫决策过程MDP中，一个agent在时间t基于当前的观察$o_t$选择行动action$_t$，然后接受奖励$r_t$

![20200409191853](https://raw.githubusercontent.com/s974534426/Img_for_notes/master/20200409191853.png)

#### 轨道
一个时间离散化的agent环境交互过程可以用轨道来表示$S_0, O_0, A_0, R_0, ..., S_n, O_n, A_n, R_n, ...$

无限步数(连续式)决策任务：交互要一直进行下去

有限步数(情节式，回合制)决策任务有一个结束状态s

完全可观察时，$O_t = S_t$，上面的轨道可以省略观察

#### 稳态MDPs
$P(S_{t+1}, R_t| S_t, A_t)$不随时间变化

![20200409194022](https://raw.githubusercontent.com/s974534426/Img_for_notes/master/20200409194022.png)

![20200409194056](https://raw.githubusercontent.com/s974534426/Img_for_notes/master/20200409194056.png)

### 2. Utility and Reward
介绍了定义奖励的一些方法

MDP中的奖赏可视为一个加法效用函数的组件

#### 有限步数
奖赏为$\sum_{t=0}^{n-1}R_t$

#### 无限步数
- 使用折扣因子定义效用，$\sum_{t=0}^{\infin}\gamma^tR_t$
- 使用平均奖赏定义效用，$lim_{n\rightarrow \infin}\frac{1}{n}\sum_{t=0}^{n-1}R_t$

折扣因子的作用
- 使得当前奖励比未来奖赏更有价值
- 只要奖赏有限，效用也是有限数

#### 状态值函数
![20200409202228](https://raw.githubusercontent.com/s974534426/Img_for_notes/master/20200409202228.png)


## 2， Dynamic Programming
MDPs能够用动态规划求解的原因，(最优子结构)Bellman方程提供了递归的分解问题的方法，(重叠子问题)值函数存储的数据可以被重复使用

- 策略迭代，用迭代的方法求解Bellman期望方程
- 值迭代，用迭代的方法求解Bellman最优方程

### 1. Policies and Utilities
MDP中的一个policy指的是在给定历史状态和action时，确定做出什么样的行动。在t时刻选择的行动写做$\pi_t(s_{0:t}, a_{0: t-1})$

在状态s下执行$\pi$的期望效用为$U^{\pi}(s)$，在MDP问题中，$U^{\pi}$通常指的是值函数，一个最优策略是最大化效用的策略$\pi^*(s)=argmax_{\pi}U^{\pi}(s)$

### 2. Policy Evaluation
计算一个policy的期望效用称为policy evaluation

计算执行策略$\pi$的t步效用过程
1. 如果不执行策略$\pi$，则$U_0^{\pi}=0$
2. 如果执行策略$\pi$一步，则$U_1^{\pi}(s) = R(s, \pi(s))$
3. 假设已知t-1步的回报，可以代入下式求解

$$U_t^{\pi} = R(s, \pi(s)) + \gamma \sum_{s'}T(s'|s, \pi(s))U_{t-1}^{\pi}(s')$$

上式去掉下角标后，就是**bellman期望方程**，可以采用迭代的方式求解

**无限步数**时，当$\gamma < 1$且n足够大，用$U_n^{\pi}$近似$U^{\pi}$，证明时，可以向前求极限，看是否为0

![20200416200607](https://raw.githubusercontent.com/s974534426/Img_for_notes/master/20200416200607.png)

另一种方法是用求解线性方程组的方式求解，$U^{\pi}=R^{\pi}+\gamma T^{\pi}U^{\pi}$，直接求解就可以，计算复杂度为n的立方，主要来自求逆

### 3. Policy Iteration
通过不断评估策略，并改进，得到最优策略的过程

策略迭代从任何状态开始，然后迭代下面两步
- 策略估计，计算当前策略的评分$U^{\pi_k}$
- 策略改进，使用$U^{\pi_k}$计算新的策略

### 策略改进
策略改进定理，对两个确定性策略$\pi$和$\pi'$，如果对于任意$s\in S$，有$U^{\pi}(s)\leq Q^{\pi}(s, \pi '(s))$，则$\pi \leq \pi '$，即对任意$s\in S$，有$U^{\pi}\leq U^{\pi'}(s)$

其中的$Q^{\pi}(s, \pi '(s))$表示，在状态s下，使用策略$\pi '$，其他状态下使用策略$\pi$得到的期望回报

这个定理的意思是，如果在任意状态下，使用某一步使用策略$\pi '$，而其他步使用策略$\pi$的期望回报高于全部使用策略$\pi$的期望回报，则全部使用$\pi '$的期望回报也高于全部使用$\pi$的期望回报

在此基础上，说明如果存在状态$s\in S$，有$U^{\pi}(s)<Q^{\pi}(s, \pi '(s))$，则存在状态s，使得$U^{\pi}(s)<U^{\pi '}(s)$

基于策略改进定理，令$\pi ' (s) = \pi_{k+1}(s)=argmax_aQ^{\pi_k}(s, a)$，有$U^{\pi}(s)<U^{\pi '}(s)$之后不断迭代，直到策略保持不变，并且可以证明，一直贪心最后得到的结果是最优的。

### 策略迭代算法

![20200416180625](https://raw.githubusercontent.com/s974534426/Img_for_notes/master/20200416180625.png)

### 4. Value Iteration
更加简单和容易实现

$$U^*_n(s) = max_{a}(R(s, a)+\gamma\sum_{s'}T(s'|s, a)U_{n-1}^*(s'))$$

值迭代通过迭代上面的式子估计$U^*$，一旦知道了$U^*$，就可以得到策略$\pi(s) = argmax_a(R(s, a)+\gamma \sum_{s'}T(s'|s, a)U^*(s'))$

![20200416182401](https://raw.githubusercontent.com/s974534426/Img_for_notes/master/20200416182401.png)

代码第五行的$U$为最优值函数$U^*$的估计，所以是迭代地更新$U^*$的估计来逼近$U^*$

最优值函数初始化时，可以是任一有界的初始值，好的初始值能加速收敛

收敛条件$||U_k-U_{k-1}||_{\infty}<\delta$，前面一项叫做Bellman残差

### 5. Grid World Example

### 6. Asynchronous Value Iteration
值迭代：每一轮迭代，基于$U_k$，对所有状态更新$U_{k+1}$

异步值迭代，在每一轮迭代，仅更新状态空间的一个子集

高斯-赛德尔值迭代，只保留一份状态值，对值就地in place更新，由于in place，所以实际收敛轮数和计算顺序有关

### 7. 闭环规划和开环规划
开环规划不考虑未来状态信息，也就是会提前规划一条路径，然后走，计算开销小，但结果次优

闭环规划会考虑未来信息状态，根据观察到当前的状态来选择行动，计算开销大，能后的近似最优解

## 3. Structured Representations
由于状态空间随着n的增加指数增长，所以大规模求解是困难的

这部分给出了一些求解高维问题的方法
### 1. Factored Markov Decision Processes MPD问题的因子化表示
重点介绍了基于决策树的因子化表示

因子化的MDPs,使用动态决策网络来压缩表示转移函数和奖励函数

### 2. Structured Dynamic Programming
重点介绍基于决策树表示的值迭代方法

决策树还可以压缩表示为决策图

## 4. Linear Representations
这一节处理的问题是状态空间连续，动作空间满足一些特定条件的问题。

1. 状态转移函数是线性的，$T(s'|s, a) = T_ssT_aa+w$，w是均值为0，方差有限的噪声
2. 期望奖赏是二次的，$R(s, a)=s^{\top}R_ss+a^{\top}R_aa, R_s=R_s^{\top}\leq0, R_a=R_a^{\top}<0$

## 5. Approximate Dynamic Programming
### 1. Local Approximation
局部近似算法依赖于邻近的状态有相似的值的假设

这样，我们就可以用$U(s) = \sum_{i=1}^n\lambda_i\beta_i(s)$得到估计值，其中的$\lambda$为每个状态的值，$\beta$为这个状态的权重，这里的$\beta_i$的和为1

![20200424085959](https://raw.githubusercontent.com/s974534426/Img_for_notes/master/20200424085959.png)

![20200423230124](https://raw.githubusercontent.com/s974534426/Img_for_notes/master/20200423230124.png)

这个循环直到$\lambda$收敛时才会停止

#### 最近邻方法
把所有权重赋给与s最接近的状态，得到分段常值函数$U(s)=u_{argmin_{i\in 1:n}d(s_i, s)}$

#### k近邻近似
将与s最接近的k个状态，每个赋予$\frac{1}{k}$的权重

#### 线性插值
使用一个状态的邻域内，多个已知状态的值计算

邻域函数$N(s)$：从$s_{1:n}$中返回一个状态子集

![20200424090717](https://raw.githubusercontent.com/s974534426/Img_for_notes/master/20200424090717.png)

#### 双线性插值
![20200424090813](https://raw.githubusercontent.com/s974534426/Img_for_notes/master/20200424090813.png)

注意$\alpha$是对应状态的对角的面积

#### 多线性插值
处理高维状态空间

在d维网格中，有多达$2^d$个邻居

#### 单纯性插值
高维问题

![20200424091243](https://raw.githubusercontent.com/s974534426/Img_for_notes/master/20200424091243.png)

线性增长，而多线性指数增长

### 2. Global Approximation
全局近似是使用固定的一组$\lambda$近似整个空间的值函数

最常用的一种方法是用线性回归，$U(s) = \sum_{i=1}^n\lambda_i\beta_i(s)$，将其中的$\beta$看作是要学习的特征。虽然表达式和上面相同，但解释完全不同，这里的$\lambda$不和一个离散的状态对应，$\beta$也不与度量相关

![20200423231432](https://raw.githubusercontent.com/s974534426/Img_for_notes/master/20200423231432.png)

第六行中，是使用线性回归得到$\lambda$，一种常见的方法是最小化平方和$\sum_{i=1}^n(\lambda^{\top}\beta(s_i)-u_i)^2$

## 6. Online Methods
离线方法：在按策略执行行动之前，离线计算整个状态空间上的策略

在线方法：将计算限制在当前可达的状态，减少了时间和空间

### 1. Forward Search
前向搜索是能够从当前状态s向前看d步，返回最优actiion和对应的值

![20200424094315](https://raw.githubusercontent.com/s974534426/Img_for_notes/master/20200424094315.png)

复杂度为$O((|S|*|A|)^d)$

### 2. Branch and Bound Search
类似alpha beta剪枝

分支限界法是前向搜索的一种扩展，使用值函数的先验知识的上下界来进行剪枝

但时间复杂度不变

下面的伪代码中，第五行的行动要按照上界降序排列

![20200424094833](https://raw.githubusercontent.com/s974534426/Img_for_notes/master/20200424094833.png)

### 3. Sparse Sampling
采样法可以避免前向搜索和分支限界法的复杂度，但不能保证返回最优解，在大多数情况下可以产生近似最优解。一种重要的方法为稀疏采样

稀疏采样法和前向搜索算法的主要区别是迭代n次采样，而不是在整个状态空间中搜索，并且使用产生式模型G



### 4. Monte Carlo Tree Search
蒙特卡洛树搜索是最成功的基于采样的在线算法之一

使用产生式模型，计算复杂度不随着深度指数增加

分为四个阶段

1. 选择，使用树策略，选择一个需要扩展的节点。从根节点开始，在搜索树中前向选择，直到到达一个不在T中的结点S
2. 扩展
3. Rollout评价，使用默认策略，指导新扩展节点到指定深度的行动选择
4. 反向更新

不断重复就可以用增量式、非对称的方式构建一颗搜索树T

![20200424105045](https://raw.githubusercontent.com/s974534426/Img_for_notes/master/20200424105045.png)

![20200424105138](https://raw.githubusercontent.com/s974534426/Img_for_notes/master/20200424105138.png)

![20200424105504](https://raw.githubusercontent.com/s974534426/Img_for_notes/master/20200424105504.png)

![20200424100532](https://raw.githubusercontent.com/s974534426/Img_for_notes/master/20200424100532.png)

## 7. Direct Policy Search
在策略空间中直接搜索

目标函数，$U^{\pi_{\lambda}}(s) = \frac{1}{n}\sum_{i=1}^nu_i$，其中的$u_i$是使用策略$\pi_{\lambda}$进行第i次rollout评价得到的值

期望回报是$V(\lambda)=\sum_sb(s)U^{\pi_{\lambda}}(s)$，其中的b是状态的分布

使用蒙特卡洛来估计V，注意算法是对$V(\lambda)$的估计，虽然表达形式与U类似，但这里的每一个u的状态不用，状态是从b中采样得到的，所以是对V的估计

![20200507203231](https://raw.githubusercontent.com/s974534426/Img_for_notes/master/20200507203231.png)

如何在策略空间中搜索找到使得$V$最大的$\lambda$
### 1. 局部搜索方法 爬山法 梯度上升法
搜索策略：沿着最大值的邻居方向搜索，直到收敛

### 2. 进化方法
遗传算法

遗传编程：使用树结构表示策略，比使用固定长度字符串更灵活，杂交是交换子树，变异是随机修改子树

与其他方法结合得到遗传局部搜索，文化基因算法等等。

### 3. 交叉熵方法
#### 信息量
事件$X=i$的信息量为$I(x^i) = -logP(x^i)$

#### 熵
熵是信息量的期望$H(x)=\sum_{i=1}^nP(x^i)I(x^i) = -\sum_{i=1}^nP(x^i)logP(x^i)$

用期望符号表示为$H(x) = E_{x\sim P}[I(x)] = -E_{x\sim P}[logP(x)]$

#### 相对熵的概念和性质
也称KL散度，表示同一个随机变量的两个不同分布间的距离，越小两个分布越相似

P相对于Q的相对熵：$D_{KL}(P || Q) = E_{x\sim P}[log\frac{P(x)}{Q(x)}] = E_{x\sim P}[logP(x) - logQ(x)]$

##### 性质
1. $D_{KL}(P || Q) \geq 0$，$D_{KL}(P || Q) = 0$时，有P=Q
2. 不对称性

#### 交叉熵
$H(P, Q)$，用分布$Q(x)$表示真实的分布$P(x)$的差异程度

$$H(P, Q) = -E_{x\sim P}logQ(x)$$

代入相对熵的公式，有$H(P, Q) = H(P) + D_{KL}(P || Q)$

最小化交叉熵等价于最小化相对熵

#### 交叉熵方法
1. 采样：从分布$P(\lambda | \theta)$中采样n次，使用蒙特卡洛策略估计方法评估性能，将样本按降序排列
2. 更新，选择性能最好的m个样本(精英样本)基于交叉熵最小化进行更新，$\theta \leftarrow argmax_{\theta}\sum_{j=1}^mlogP(\lambda_j| \theta)$，因为交叉熵表达式中有负号，这里的表达式其实是负交叉熵，所以是最小化，但式子前面是最大化。

![20200508084203](https://raw.githubusercontent.com/s974534426/Img_for_notes/master/20200508084203.png)
