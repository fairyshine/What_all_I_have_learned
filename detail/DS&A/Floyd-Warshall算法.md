动态规划

# 思想

![Floyd算法](pic/Floyd算法.png)

# 复杂度

| 平均时间复杂度 | $\Theta(|V|^3)$ |
| :------------- | --------------- |
| 最坏时间复杂度 | $O(|V|^3)$      |
| 最优时间复杂度 | $\Omega(|V|^3)$ |
| 空间复杂度     | $\Theta(|V|^3)$ |



# 伪代码

![Floyd算法伪代码](pic/Floyd算法伪代码.png)

其中`dist[i][j]`表示由点i![i](https://wikimedia.org/api/rest_v1/media/math/render/svg/add78d8608ad86e54951b8c8bd6c8d8416533d20)到点j![j](https://wikimedia.org/api/rest_v1/media/math/render/svg/2f461e54f5c093e92a55547b9764291390f0b5d0)的代价，当其为 ∞ 表示两点之间没有任何连接。