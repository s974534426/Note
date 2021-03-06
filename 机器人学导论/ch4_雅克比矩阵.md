# ch4 雅克比矩阵
## 1. 雅克比矩阵
描述关节角速度和操作臂末端直角坐标速度之间的关系 
### 定义
操作空间速度与关节空间速度之间的线性变换
$$\dot{x}=J(q)\dot{q}$$
其中$J_{ij}(q)=\frac{\partial x_i(q)}{\partial q_j}, i=1, ..., 6, j=1, ..., n$
$J$是6*n的矩阵，n取决于机器人的关节数量

计算时，首先计算出操作空间的位置的方程，然后求导就可以了

### 奇异形位
数学上雅克比矩阵的秩减少，物理上是自由度的减少
衡量机构的运动性能，不要达到奇异形位

## 2. 矢量积求解雅克比矩阵
![20200319180645.png](https://raw.githubusercontent.com/s974534426/img_for_notes/master/20200319180645.png)
一列代表一个关节，上半部分表示关节引起线速度的变化，下半部分表示引起的角速度的变化
按行看，可以看出去其实末端速度是各个关节速度的一个线性组合，后面用矢量积法主要也是看如何找到末端速度和各个关节速度之间的关系
![20200319181150.png](https://raw.githubusercontent.com/s974534426/img_for_notes/master/20200319181150.png)
- 对移动关节，这个关节的移动会引起末端点在同样方向上的移动，不会引起转动。对图中的平动关节，其实就是图中标的$d$
- 对转动关节，上边的线速度其实就是$\omega r$，前面的$z_i$是角速度方向的单位向量，然后标量$\dot{q}_i$给出了角速度大小，并且这里的半径需要在全局坐标系下，$^ip_n^0=_i^0R^ip_n$

![20200319182750.png](https://raw.githubusercontent.com/s974534426/img_for_notes/master/20200319182750.png)
## 3. 机器人的力雅克比矩阵
静力学分析
![20200319191104.png](https://raw.githubusercontent.com/s974534426/img_for_notes/master/20200319191104.png)
**这里讲的主要就是在忽略关节摩擦力和杆件重力时，如何根据末端收到的一个外力求出各个关节的受力情况**
末端$\vec{F}=[\vec{f}, \vec{n}]'$里面的f为力，n为力矩，统称为末端广义力矢量
关节力矢量$\tau=[\tau_1, ..., \tau_n]^{\top}$
![20200319192057.png](https://raw.githubusercontent.com/s974534426/img_for_notes/master/20200319192057.png)
这里可以看出操作臂的静力传递关系与速度传递关系紧密相关，具有对偶性
![20200319192549.png](https://raw.githubusercontent.com/s974534426/img_for_notes/master/20200319192549.png)
![20200319192643.png](https://raw.githubusercontent.com/s974534426/img_for_notes/master/20200319192643.png)
## 4. 实例
![20200319192955.png](https://raw.githubusercontent.com/s974534426/img_for_notes/master/20200319192955.png)
这个图写的很详细，首先根据各个杆件坐标系之间的变化关系求出第一行，然后相乘得到第二行，然后根据齐次矩阵中旋转矩阵的位置和坐标系平移的位置得到第三行，最后左边取出z轴的单位矢量，右边利用坐标变化关系求出。然后得到雅克比矩阵