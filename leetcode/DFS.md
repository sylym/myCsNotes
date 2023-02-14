# DFS

### 例题

- [863. 二叉树中所有距离为 K 的结点](https://leetcode.cn/problems/all-nodes-distance-k-in-binary-tree/)
  - 一次dfs构图，一次bfs找到结果

## 回溯

- [489. 扫地机器人](https://leetcode.cn/problems/robot-room-cleaner/)

## 记忆化搜索

### 思想

动态规划是一种自底向上的思想，而记忆化搜索是一种自顶向下的思想。
当便利顺序难以确认时，使用记忆化搜索由dfs自动决定
逻辑上相比动态规划更为清晰简单。

### 剪枝

### 例题

- [698.h划分为k个相同的子集](https://leetcode.cn/problems/partition-to-k-equal-sum-subsets/)
- [638. 大礼包 ](https://leetcode.cn/problems/shopping-offers/solution/)
  - 多元组用背包动态规划不如记忆化搜索简单（直接map）
- [691. 贴纸拼词 ](https://leetcode.cn/problems/stickers-to-spell-word/)
- [1301. 最大得分的路径数目](https://leetcode.cn/problems/number-of-paths-with-max-score/)
- [241. 为运算表达式设计优先级](https://leetcode.cn/problems/different-ways-to-add-parentheses/)
  - 递归分治（枚举最后一个完成的运算符）+记忆化搜索
- [332. 重新安排行程](https://leetcode.cn/problems/reconstruct-itinerary/submissions/)
  - 对机票计数，而不是通过删除set中的元素，避免迭代器发生问题
  - 也可以使用欧拉回路解决
- [1815. 得到新鲜甜甜圈的最多组数](https://leetcode.cn/problems/maximum-number-of-groups-getting-fresh-donuts/)