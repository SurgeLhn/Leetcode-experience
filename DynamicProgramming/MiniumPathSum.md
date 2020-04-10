# leetcode里面下降路径最小和问题

首先，放一道leetcode的题目：

![下降路径最小值](https://pic.downk.cc/item/5e8fe2f5504f4bcb042cd051.png)


[下降路径最小值](https://leetcode-cn.com/problems/minimum-falling-path-sum/)


可能在没有学过算法之前，大部分想到的就是暴力算法。如果这个矩阵数组是m*n<font color="grey">(m行n列)</font>，可以算算此时暴力算法的时间复杂度：
>暴力算法时间复杂度：
因为第1列和第n列的元素只有两个选择，所以如果前<font color='grey'>m-1</font>行的元素，每个元素都只有两个选择，都有**2^n(m-1)^** 种可能性。即此时的时间复杂度为：**O(2^nm^)**。

可以看出，此时暴力算法的时间复杂度为指数级别，是要尽力去避免的。
而要解决这类问题，**动态规划是一个不错的选择**。
## 什么是动态规划
这个名词，也许都听过很多遍。但是在这篇文章，我们只用了解一点思想。关于系统学习动态规划，知乎上这里([动态规划](
https://www.zhihu.com/topic/19660018))有许多高赞回复，就不一一举例了。

>**quora**上就有这样的问题：
**How should I explain dynamic programming to a 4-year-old?**
底下有个高赞回答：
*writes down "1+1+1+1+1+1+1+1 =" on a sheet of paper*
"What's that equal to?"
*counting* "Eight!"
*writes down another "1+" on the left*
"What about that?"
*quickly* "Nine!"
"How'd you know it was nine so fast?"
"You just added one more"
"So you didn't need to recount because you remembered there were eight! Dynamic Programming is just a fancy way to say 'remembering stuff to save time later'"

其中思想就是最后一句话**remembering stuff to save time later**。
假如把这个问题考虑成动态规划数组：
<font color='grey'>(以下动态规划数组的简写为dp[])</font>
```c
//已知
dp[8]=1+1+1+1+1+1+1+1
//求dp[9]
```
小孩的回答就是：
```c
dp[9]=dp[8]+1
```
写到这个程度，估计也知道我想说什么了。所谓的思想就是：
```c
//从某一项开始
dp[n]=(操作){dp[0],dp[1],...,dp[n-1]}
```
理解到这个程度，就足够看懂下面的文章和解决这类问题了。
## 如何运用动态规划解决上述问题
说到底，学这个思想也是为了解决上面的问题的，如果解决不了，那么上面的文字就是废话。

回到题目:
首先，**建立一个跟矩阵数组一样规模的动态规划数组**。<font color='grey'>即：矩阵数组是m\*n的话，那么动态规划数组的规模也是m*n。</font>
<font color="grey"></font>
```java
int row=A.length;//矩阵数组的行数
int col=A[0].length;//矩阵数组的列数
int [][]dp=new int[row][col]
```
然后，思考一下动态规划数组代表什么。因为题目要求下降路径最小值的问题，所以**动态数组某个值意味着到这个下标位置最短路径值。**这话这么拗口，那么结果到底要求啥。其实就是求**动态规划数组最后一行所有元素的最小值。**
即：
```java
int res=java.lang.Integer.MAX_VALUE;
for(int n:dp[row-1])//定位到最后一行
    res=Math.min(n,res);
return res;
```
所以，要思考的问题**就是如何确定最后一行的元素**。

看到上面写了这样一句话：
```c
//从某一项开始
dp[n]=(操作){dp[0],dp[1],dp[2],...,dp[n-1]}
```
如果要从某一项开始才要从前面推，意思就是前面要有初始项。那么思考这个动态规划的数组的初始项是啥？因为在第一行任何数都可以作为路径的开始，所以**动态规划数组初始项就是矩阵数组第一行，即动态规划数组的第一行应该复制矩阵数组的第一行**。
```java
for(int j=0;j<col;j++)
    dp[0][j]=A[0][j];
```
那么从第二行开始，**dp[i][j]意思就是从第一行到位置(i,j)的最小路径值。** 这样讲也比较抽象，回到矩阵数组，对于A[1][1]，即数字5，按照题目要求，可以从第一行选择的路径有三个，dp[0][0]\(A[0][0])，dp[0][1]\(A[0][1])，dp[0][2]\(A[0][2])，即：1，2，3，那么与数字5组成的三条路径和就分别是6，7，8，选出最小值，为6。那么:
```c
dp[1][1]=min{dp[0][0],dp[0][1],dp[0][2]}+A[1][1];
```
这样，从第二行到第m行的元素值的求法都清楚了。估计会有这样的初步想法：
```
for(int i=1;i<row;i++){
    for(int j=0;j<col;j++){
        //求三个数最小值
        int temp=Math.min(dp[i-1][j-1],dp[i-1][j+1]); 
        temp=Math.min(dp[i-1][j],Min);
        //再加上矩阵数组原来位置的数
        dp[i][j]=temp+A[i][j];
    }
}
//ps:这是错的
```
但是还要注意一个细节问题，**对于第一列元素，没有左边的位置**，<font color='grey'>即没有dp[-1]的元素</font>。**对于最后一列的元素，没有右边的位置**，<font color='grey'>即没有dp[n]的元素</font>。总的来说，**对于第一列和最后一列，只有两个位置可供选择**。

最终，动态规划数组的构造如下：
```java
for(int i=1;i<row;i++){
    for(int j=0;j<col;j++){
        if(j==0){
            //对于第一列的元素
            dp[i][j]=
            Math.min(dp[i-1][j],dp[i-1][j+1])+A[i][j];
        }else if(j==col-1){
            //对于最后一列的元素
            dp[i][j]=
            Math.min(dp[i-1][j-1],dp[i-1][j])+A[i][j];
        }else{
            //对于中间的元素
            //求三个数最小值
            int temp=Math.min(dp[i-1][j-1],dp[i-1][j+1]);
            temp=Math.min(dp[i-1][j],temp);
            dp[i][j]=temp+A[i][j];
        }
    }
}
```
到这里，推导就完成了：

来看看这道题的完整代码：
```java
class Solution{
    public int minFallingPathSum(int [][]A){
        //矩阵数组A为空数组
        if(A.length==0||A[0].length==0)
            return 0;
        int row=A.length;//矩阵数组的行数
        int col=A[0].length;//矩阵数组的列数
        int [][]dp=new int[row][col];
        //开始构建动态规划数组
        for(int i=0;i<row;i++){
            for(int j=0;j<col;j++){
                if(i==0){
                    //复制矩阵数组第一行
                    dp[i][j]=A[i][j];
                }else if(j==0){
                    //对于第一列的元素
                    dp[i][j]=
                    Math.min(dp[i-1][j],dp[i-1][j+1])+A[i][j];
                }else if(j==col-1){
                    //对于最后一列的元素
                    dp[i][j]=
                    Math.min(dp[i-1][j-1],dp[i-1][j])+A[i][j];
                }else{
                    //对于中间的元素
                    //求三个数最小值
                    int temp=Math.min(dp[i-1][j-1],dp[i-1][j+1]);
                    temp=Math.min(dp[i-1][j],temp);
                    dp[i][j]=temp+A[i][j];
                }

            }
        }
        //取动态规划数组最后一行元素的最小值
        int res=java.lang.Integer.MAX_VALUE;
        //定位到最后一行
        for(int n:dp[row-1])
            res=Math.min(res,n);
        return res;
    }
}
```
该算法的时间复杂度为：O(mn)。<font color='grey'>m为矩阵行数，n为列数</font>
该算法的空间复杂度为：O(mn)。<font color='grey'>创建一样规模的动态规划数组</font>
## 解决其他类似问题
+ 
![下降路径最小和II](https://pic.downk.cc/item/5e90332f504f4bcb047e8bf3.png)
[下降路径最小和II](https://leetcode-cn.com/problems/minimum-falling-path-sum-ii/)
这道题的框架跟上面差不多，只不过细节问题有点不一样。
根据题意，按顺序选出来的数字中，相邻数字不在原数组同一列。**意思就是，如果选了A[i][j]，那么就不能选A[i+1][j]**。
即：
```
dp[i][j]=min{dp[i-1][0],dp[i-1][1],...,dp[i-1][j-1],dp[i-1][j+1],...,dp[i-1][n-1]}
```
则这道题完整代码：
```java
class Solution{
    public int minFallingPathSum(int[][] arr){
        //矩阵数组为空数组，直接返回0
        if(arr==null||arr[0]==null)
            return 0;
        //新建动态规划数组
        int row=arr.length;
        int col=arr[0].length;
        int[][] dp=new int[row][col];
        //定义一个返回值，这次不单独做比较的步骤，再最后一步求最后一行数组元素时同时进行比较
        int res=java.lang.Integer.MAX_VALUE;
        //开始求动态规划数组
        for(int i=0;i<row;i++){
            for(int j=0;j<col;j++){
                if(i==0)
                    //复制第一行
                    dp[i][j]=arr[i][j];
                else{
                    int temp=java.lang.Integer.MAX_VALUE;
                    //取上一行元素最小值
                    for(int k=0;k<col;k++){
                        //回避同一列元素
                        if(k!=j)
                            temp=Math.min(dp[i-1][k],temp);
                    }
                    dp[i][j]=temp+arr[i][j];
                    //赋值最后一行同时比较大小
                    if(i==row-1){
                        res=Math.min(dp[i][j],res);
                    }
                }
            }
        }
        return res;
    }
}
```
但这样，时间复杂度为O(mn^2^)，立方级别，等待后续优化。

+  
![三角形最小路径和](https://pic.downk.cc/item/5e90629f504f4bcb04a6290a.png)
[三角形最小路径和](https://leetcode-cn.com/problems/triangle/)
框架相同，也是细节问题要处理好：
```c
class Solution{
public:
    int minimunTotal(vector<vector<int>>&triangle){
        if(!triangle.size())
            return 0;
        int wid=triangle.size();
        //新建一个规模一样的三角形矩阵
        int **dp=new int*[wid];
        for(int i=0;i<wid;i++){
            dp[i]=new int[i+1];
        }
        dp[0][0]=triangle[0][0];
        //第一列和斜边单独处理
        int sumHead=0;
        int sumTail=0;
        for(int i=0;i<wid;i++){
            sumHead+=triangle[i][0];
            sumTail+=triangle[i][i];
            dp[i][0]=sumHead;
            dp[i][i]=sumTail;
        }
        for(int i=1;i<wid;i++){
            for(int j=1;j<i;j++){
                dp[i][j]=min(dp[i-1][j-1],dp[i-1][j])+triangle[i][j];
            }
        }
        int res=INT_MAX;
        for(int i=0;i<wid;i++){
            res=min(dp[wid-1][i],res);
        }
        return res;
    }
}
```

+
![最小路径和](https://pic.downk.cc/item/5e9066b2504f4bcb04a9f8fe.png)
[最小路径和](https://leetcode-cn.com/problems/minimum-path-sum/)
这道题中，起点和终点已经确定，而题目要求只能向下和向右，那么第一行和第一列的元素就需要单独处理。
即：
```
dp[0][i]=arr[0][0]+arr[0][1]+...+arr[0][i-1];
dp[i][0]=arr[0][0]+arr[1][0]+...+arr[i-1][0];
```
而因为方向是向右或者向下，那么除了第一行，第一列，其他部分这样操作
```
dp[i][j]=min{dp[i-1][j],dp[i][j-1]}+arr[i][j];
```
那么完整代码如下：
```java
class Solution{
    public int minPathSum(int[][] arr){
        if(arr==null||arr[0]==null)
            return 0;
        int row=arr.length;
        int col=arr[0].length;
        int[][] dp=new int[row][col];
        //对第一行进行处理
        int sum=0;
        for(int i=0;i<col;i++){
            sum+=arr[0][i];
            dp[0][i]=sum;
        }
        //对第一列进行处理
        sum=0;
        for(int i=0;i<row;i++){
            sum+=arr[i][0];
            dp[i][0]=sum;
        }
        for(int i=1;i<row;i++){
            for(int j=1;i<col;j++){
                dp[i][j]=Math.min(dp[i-1][j],dp[i][j-1])+arr[i][j];
            }
        }
        return dp[row-1][col-1];
    }
}
```
题目就先举例那么多。而思想也再重复一遍：**找到初始项，并从某一位置开始充分利用前面的数据**。
```c
//从某一项开始
dp[n]=(操作){dp[0],dp[1],...,dp[n-1]}
```
光有想法，少了练习，那么能力也是很难提高的。所以看完文章也能多多进行练习，祝刷题愉快。
*peace!*