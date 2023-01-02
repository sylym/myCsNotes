# stl
# 容器
## set
### 使用
- （技巧）用set实现自定义比较
    - 打包为自定义数据结构，自定义比较即可
- set的迭代器不支持许多操作，可以用以下方法操作
    - \*begin获取第一个元素，\*rbegin获取最后一个元素
    - 用prev(),next()实现++--等操作
        - 如prev(it,2)可以指定移动的数目
```
struct Node {
    int cnt, time;
    bool operator < (const Node& rhs) const {
        return cnt == rhs.cnt ? time < rhs.time : cnt < rhs.cnt;
    }
};
set<Node> S;//就会按照自定义比较规则进行排序。
```
## map
- 自定义比较：
- 二分成员函数：
    - [1146. 快照数组](https://leetcode.cn/problems/snapshot-array/submissions/)
### bitset
- 是一个多位二进制数，每8位占用一个字节，常用于状态压缩（比int更自由，比vector<bool>效率更高）
- bitset<n>s表示定义一个n位长的二进制数
- ~s: 返回对s每一位取反后的结果；
    - &，|，^：返回对两个位数相同的bitset执行按位与、或、异或运算的结果；
    - <<, >>:返回把一个bitset左移，右移若干位的结果.（补零）；
- s[k] :表示s的第k位，即可取值也可赋值，编号从0开始；
- s.count() 返回二进制串中有多少个1；
- 若s所有位都为0，则s.any()返回false，s.none()返回true；
    - 若s至少有一位为1，则s.any()返回true，s.none()返回false；
- s.set()把s所有位变为1；
    - s.set(k,v)把s的第k位改为v,即s[k]=v；
    - s.reset()把s的所有位变为0.
    - s.reset(k)把s的第k位改为0,即s[k]=0；
    - s.flip()把s所有位取反.即s=~s；
    - s.flip(k)把s的第k位取反，即s[k]^=1；
### priority_queue
#### 自定义比较函数
- 传入仿函数
```c++
struct cmp{
   bool operator()(vector<int>&a,vector<int>&b){
       return a[0]>b[0]; 
   }
};
priority_queue<vector<int>,vector<vector<int>>,cmp> q;//小顶堆
```
- 传入函数指针(可以是lambda表达式的指针)
```c++
priority_queue<vector<int>,vector<vector<int>>,decltype(&cmp)> q(cmp);
```
- function包装
```c++
function<bool(vector<int>&,vector<int>&)> cmp=[](vector<int>&a,vector<int>&b)->bool{
            return a[0]>b[0];
        };
priority_queue<vector<int>,vector<vector<int>>,decltype(cmp)> q(cmp);//小顶堆
```
# 模板（泛型）

### function
- function是一个通用的多态函数包装器，可以调用普通函数、Lambda函数、仿函数、bind对象、类的成员函数和指向数据成员的指针，function定义在名为function.h头文件中。
- 定义方式function<返回值类型（参数列表）>
`function<int(int, int)>`
- 赋值可以是仿函数对象或函数指针
-需要注意的是，当函数发生重载时不能直接使用函数名来让function保留可调用对象。而要使用函数指针。
#### 应用
```c++
/*  使用map映射字符到可调用对象。
	但需要注意，映射的前提是function对不同类型的可调用对象
进行了类型统一。 */
map<char, function<int(int, int)>> cacul{
    {'+', add},
    {'-', sub},
    {'*', prod()}
};
cout << cacul['*'](5, 4) << endl;
```
- 函数内递归
```c++
function<void(int)> dfs = [&](int i) {
    if(i == v.size()) return;
    dfs(i + 1);
    cout << v[i] << endl;
};
dfs(0);
```
# 算法
## 二分搜索