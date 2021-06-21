# Mondrian 一个贪心算法以实现K匿名

## 概况

Title：Mondrian Multidimensional K-Anonymity

### 贡献

一个实现K匿名的新的多维模型和贪心算法

- 比已经提出的单维模型大幅提高效率，时间复杂度是$O(nlogn)$，相比于单维的指数级好得多
- 结果的数据质量比最优一维算法（以及现有的一维启发式、随机算法）好

一个更复杂的质量评判，基于聚合搜索的工作负载（workload of aggregate queries）

## 多维模型

