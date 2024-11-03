# 乐水私募

# 二分查找

## 1

[668. 乘法表中第k小的数 - 力扣（LeetCode）](https://leetcode.cn/problems/kth-smallest-number-in-multiplication-table/description/)

<img src="1.png" style="zoom:33%;" />

```c++
class Solution {
public:
    int findKthNumber(int m, int n, int k) {
        int left = 1, right =m*n;
        while(left<=right){
            int count= 0;
            int mid = (left+right)/2;
            for(int i=1;i<=m;i++){//遍历每一行
                count+=std::min(mid/i,n);//根据第一列计算每一行大于mid的数目
            }
            if(count>=k){//符合条件的mid尽可能小，巧妙处理了那些不在乘法表中的数（质数）
                right = mid-1;
            } 
            else{
                left = mid+1;
            }
        }
        return left;
    }
};
```

# 搜索

## 2

[85. 最大矩形 - 力扣（LeetCode）](https://leetcode.cn/problems/maximal-rectangle/description/)

<img src="2.png" style="zoom:33%;" />

```c++
class Solution {
public:
    int maximalRectangle(vector<vector<char>>& matrix) {
        if (matrix.empty() || matrix[0].empty()) return 0;
        int rows = matrix.size();
        int cols = matrix[0].size();
        std::vector<int> histogram(cols, 0);
        int maxArea = 0;
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (matrix[i][j] == '1') {
                    histogram[j]++;
                } else {
                    histogram[j] = 0;
                }
            }
            maxArea = std::max(maxArea, largestRectangleArea(histogram));
        }
        return maxArea;
    }
    int largestRectangleArea(std::vector<int>& heights) {
        int n = heights.size();
        std::stack<int> st;
        int maxArea = 0;
        for (int i = 0; i <= n; i++) {
            while (!st.empty() && (i == n || heights[st.top()] >= heights[i])) {
                int height = heights[st.top()];
                st.pop();
                int width = st.empty()? i : i - st.top() - 1;
                maxArea = std::max(maxArea, height * width);
            }
            st.push(i);
        }
        return maxArea;
    }
};
```

## 3

[407. 接雨水 II - 力扣（LeetCode）](https://leetcode.cn/problems/trapping-rain-water-ii/description/)

<img src="3.png" style="zoom:33%;" />

```c++
class Solution {
public:
    int trapRainWater(std::vector<std::vector<int>>& heightMap) {
        int m = heightMap.size();
        if (m == 0) return 0;
        int n = heightMap[0].size();
        if (n == 0) return 0;

        std::vector<std::vector<bool>> visited(m, std::vector<bool>(n, false));
        std::priority_queue<std::pair<int, std::pair<int, int>>, std::vector<std::pair<int, std::pair<int, int>>>, std::greater<std::pair<int, std::pair<int, int>>>> pq;

        // 将边界加入优先队列
        for (int i = 0; i < m; i++) {
            pq.push({heightMap[i][0], {i, 0}});
            visited[i][0] = true;
            pq.push({heightMap[i][n - 1], {i, n - 1}});
            visited[i][n - 1] = true;
        }
        for (int j = 1; j < n - 1; j++) {
            pq.push({heightMap[0][j], {0, j}});
            visited[0][j] = true;
            pq.push({heightMap[m - 1][j], {m - 1, j}});
            visited[m - 1][j] = true;
        }

        int ans = 0;
        int dirs[4][2] = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}};
        while (!pq.empty()) {
            auto top = pq.top();
            pq.pop();
            for (auto dir : dirs) {
                int newX = top.second.first + dir[0];
                int newY = top.second.second + dir[1];
                if (newX >= 0 && newX < m && newY >= 0 && newY < n &&!visited[newX][newY]) {
                    visited[newX][newY] = true;
                    ans += std::max(0, top.first - heightMap[newX][newY]);
                    pq.push({std::max(top.first, heightMap[newX][newY]), {newX, newY}});
                }
            }
        }

        return ans;
    }
};
```

# 动态规划

## 4

[188. 买卖股票的最佳时机 IV - 力扣（LeetCode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iv/description/)

<img src="4.png" style="zoom:33%;" />

```c++
#include <iostream>
#include <vector>
#include <climits>

class Solution {
public:
    int maxProfit(int k, std::vector<int>& prices) {
        int n = prices.size();
        if (n <= 1) return 0;

        // 如果交易次数足够多，可以视为不限制交易次数的情况，使用贪心算法求解
        if (k >= n / 2) {
            int profit = 0;
            for (int i = 1; i < n; i++) {
                if (prices[i] > prices[i - 1]) {
                    profit += prices[i] - prices[i - 1];
                }
            }
            return profit;
        }

        std::vector<std::vector<int>> dp(k + 1, std::vector<int>(n, 0));
        for (int t = 1; t <= k; t++) {
            int maxDiff = -prices[0];
            for (int i = 1; i < n; i++) {
                dp[t][i] = std::max(dp[t][i - 1], prices[i] + maxDiff);
                maxDiff = std::max(maxDiff, dp[t - 1][i - 1] - prices[i]);
            }
        }
        return dp[k][n - 1];
    }
};
```

# 其他

## 5

两列火车同时被运输机空投到一条南北向铁路上的不同位置（降落后自动卸下降落伞），火车上分别装载了一颗炸弹，但是只有两列火车相撞才能引爆炸弹，用以下指令为两列火车编写同一套自动驾驶程序，让它们完成相撞炸毁铁路（假设火车移动速度1m/s，执行c、d指令不需耗时）（只能用以下4行指令，不能使用if...else，while，for循环等等其他高级语言）（GOTO是指令跳转到某一行代码继续执行）

a) GO NORTH 1m

b) GO SOUTH 1m

c) GOTO line xxx

d) if find a 降落伞，GOTO line xxx

思想：快慢指针

| line index | instruction                   |
| ---------- | ----------------------------- |
| 1          | GO SOUTH 1m                   |
| 2          | GO NORTH 1m                   |
| 3          | GO NORTH 1m                   |
| 4          | GO SOUTH 1m                   |
| 5          | GO NORTH 1m                   |
| 6          | GO NORTH 1m                   |
| 7          | if find a 降落伞，GOTO line 9 |
| 8          | GOTO line 1                   |
| 9          | GO NORTH 1m                   |
| 10         | GOTO line 9                   |

## 6

$$
\begin{align}
&给定两个等长的\text{double}数组，X_n，Y_n，对任意i=1,2,3,...,n，满足x,y在[-1,1]范围内，我们已知相关系数公式，给定服务器的计算资源，\\&主频\text{3G}的\text{cpu}，256核，内存1\text{T}。请编写一个高性能的函数，计算相关系数，要求尽可能高并发的打满全部计算资源，写明优化思路，编写计算\\
&函数\text{double corr}(X_n, Y_n)。n等于10的9次方\\
&注意:没有并发，没有性能优化的代码不得分，手动写计算思路和伪代码可以得分。
\end{align}
$$

优化思路：

1. 利用多线程充分发挥多核CPU的优势，将数据分成多个块，每个线程处理一个块，从而实现并行计算。
2. 合理分配内存，避免内存不足的情况。由于数据量较大，可以考虑使用内存映射文件等技术来减少内存压力。
3. 对于相关系数的计算，可以将计算过程分解为多个子任务，每个子任务计算一部分数据的中间结果，最后再合并这些中间结果得到最终的相关系数。

伪代码：

```
function corr(Xn,Yn)
	//确定线程数量和数据块大小
	n=length(Xn)
	numThreads=256
	chunkSize=n/numThreads
	//初始化用于存储部分结果的变量
	sumX=0
	sumY=0
	sumSqX=0
	sumSqY=0
	productSumXY=0
	//创建线程列表
	threads=[]
	//启动多个线程进行并行计算
	for i=0 to numThreads-1
		start = i*chunkSize
		end = if i==numThreads-1 then n else start+chunkSize
		create a new thread with function:
			//取数据块
			subX=slice(Xn,start,end)
			subY=slice(Yn,start,end)
			//计算部分和、平方和、乘积和
			localSumX=sum(subX)
			localSumY=sum(subY)
			localSumSqX=sumOfSquares(subX)
			localSumSqY=sumOfSquares(subY)
			localProductSumXY=productSum(subX,subY)
			//将部分结果累加到全局变量
			add localSumX to sumX
			add localSumY to sumY
			add localSumSqX to sumSqX
			add localSumSqY to sumSqY
			add localProductSumXY to productSumXY
        add the thread to threads list
    
    //等待所有线程完成
    for each thread in threads
    	if thread is joinable
    		join thread
    
    //计算平均值和相关系数分子分母
    meanX=sumX/n
    meanY=sumY/n
    numerator=productSumXY-n*meanX*meanY
    denominatorPart1=sumSqX-n*meanX*meanX
    denominatorPart2=sumSqY-n*meanY*meanY
    if denominatorPart1*denominatorPart2==0
    	return 0
    else
    	return numerator/sqrt(denominatorPart1*denominatorPart2)

//求和函数
function sum(arr)
	//遍历数组求和
	s=0
	for each value in arr
		s=s+value
    return s

//求平方和函数
function sumOfSquares(arr)
	//遍历数组求平方和
	s=0
	for each value in arr
		s=s+value*value
    return s
    
//求乘积和函数
function productSum(arr1,arr2)
	//遍历两个等长数组求对应元素乘积和
	s=0
	for i=0 to length(arr1)-1
		s=s+arr1[i]*arr2[i]
	return s
```

内存映射文件技术是一种将文件或设备映射到进程地址空间的技术，允许应用程序像访问内存一样访问文件。通过这种方式，操作系统将文件内容映射到内存中，使得应用程序可以直接读写内存，而不需要通过常规的文件输入/输出（I/O）操作来处理。

主要特点和优点：
高效性：内存映射文件可以减少文件操作的开销，因为它允许直接在内存中操作文件内容，从而提高了访问速度。

共享内存：多个进程可以映射同一个文件，这使得它们可以轻松共享数据。

自动管理：操作系统会在需要时自动加载文件内容到内存中，用户不需要手动管理缓冲区。

简化编程：开发者可以通过指针直接操作文件内容，避免了复杂的读写操作。

应用场景：
大型文件的处理，如数据库、图像处理等。
需要共享内存的多进程应用。
实现文件的随机访问。
实现方式：
在不同的操作系统中，内存映射文件的实现细节可能有所不同。例如，在Windows中，可以使用CreateFileMapping和MapViewOfFile等API，而在Unix/Linux中，通常使用mmap系统调用。

总的来说，内存映射文件技术是一种高效的文件处理方式，广泛应用于需要高性能和大规模数据处理的场景。



