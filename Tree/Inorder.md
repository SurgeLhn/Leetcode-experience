# 中序遍历及其拓展

中序遍历，就是先去遍历左子树，然后操作根节点，最后再操作右子树，将根节点放在中间的部分。其实，中序遍历因为这个特点，出现频率并没有先序遍历和后续遍历那么高。但是在特殊的树里面，就是二叉搜索树，很多情况会优先考虑中序遍历的思想，因为**二叉搜索树的中序遍历就是递增的**。

那么在看题目之前，还是先将中序遍历的模板看一下:
```java
public void inorderTraversal(TreeNode node){
    //空结点直接退，没什么好商量的
    if(node==null)
        return;
    inorderTraversal(node.left);
    //对节点进行操作
    //
    inorderTraversal(node.right);
}
```
---
好，现在来看看第一道题目：
![](https://pic.downk.cc/item/5f7d252a1cd1bbb86b4d3919.png)
[501.二叉搜索树的众数](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-di-kda-jie-dian-lcof/)

如果没有方法的话，再简单的题一样会一头雾水。那么加入将题目中的数据结构换一个，换成一个递增的数组，在`[1,2,2]`里面找众数，这样子的话不用思考都知道，**不就是遍历数组，然后记录每个数出现的次数，找最大出现的次数嘛**。
翻译成代码，就是：
```java
/*
 *@param:arr: 一个单调递增的数组
 *@return: 数组arr的众数的list
*/
public List<Integer> find Mode(int[] arr){
    //出现次数
    int time=0;
    //最大出现次数
    int maxTime=0;
    //正在遍历的值
    int curr=0;
    //众数
    List<Integer>list=new ArrayList<>();
    //遍历数组
    for(int num:arr){
        //以下为核心代码
        //如果出现重复遍历的值，那么次数加1
        if(num==curr){
            time++;
        }
        //如果出现了新的值，那么将正在遍历的值换成新的值
        else{
            curr=num;
            time=1;
        }
        //如果出现的次数大于最大出现次数，那么先将最大值置为出现的次数，清空列表，将访问的值当作众数加入列表
        if(time>maxTime){
            maxTime=time;
            list.clear();
            list.add(curr);
        }
        //出现与最大次数相同次数的值，同样加入列表
        else if(time==maxTime){
            list.add(curr);
        }
    }
    return list;

}
```
如果是递增数组找众数，当然特别简单。那么为什么要提出在递增数组里面找众数呢？记得之前提过一句话吗？**二叉搜索树的中序遍历就是单调递增的**。所以，这道题的方向就很轻易确定了？使用中序遍历的方法！
那么就将模板和在数组中找众数的方法合并一下，**合并就是节点操作部分对照数组循环部分**，就差不多完成了。但是还有一个细节记得注意一下：
1. 数组是顺式结构，遍历的方式较多为循环，所以像`time, maxTime, curr`这些变量可以写在函数里面。
2. 而树是链式结构，除了广度优先遍历以外，遍历的方法多为递归，所以要将上面这些变量写在函数外面作为全局变量。

好，那么代码如下：
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    //存放众数的列表，作为全局变量
    List<Integer>mList=new ArrayList<>();
    //正在遍历的值，作为全局变量
    int current=0;
    //出现的次数，作为全局变量
    int count=0;
    //最大出现次数，作为全局变量
    int maxCount=0;
    public void traversal(TreeNode node){
        //空结点直接退
        if(node==null)
            return;
        //遍历左子树
        traversal(node.left);
        //操作节点
        int nodeVal=node.val;
        //这里可以对照数组遍历那一块
        if(nodeVal==current){
            count++;
        }
        else{
            current=nodeVal;
            count=1;
        }
        if(count==maxCount){
            mList.add(nodeVal);
        }
        else if(count>maxCount){
            maxCount=count;
            mList.clear();
            mList.add(nodeVal);
        }
        //遍历右子树
        traversal(node.right);
    }
    public int[] findMode(TreeNode root) {
        traversal(root);
        //将list变为数组
        int[] res=new int[mList.size()];
        for(int i=0;i<mList.size();i++){
            res[i]=mList.get(i);
        }
        return res;
    }
}
```
第一道题完成，大家应该对中序遍历的思想记得比较清楚了，那废话不多说，我们进入第二道题，同时第二道题跟第一道题还有个一样的思想，就是定义全局变量来记录：
![](https://pic.downk.cc/item/5f7d2ea61cd1bbb86b4f7d91.png)
[剑指Offer54.二叉搜索树的第k大节点](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-di-kda-jie-dian-lcof/)


那么还是跟第一道题一样，想想在**递增的数组中找第k大的数怎么找**。
```java
/*
 *@param:arr:递增数组
 *@param:k:位置
 @return:第k大的数
 */
public int findKth(int[] arr,int k){
    int len=arr.length;
    //k不能比数组的长度还大
    if(k>len)
        return -1;
    //第k大的数
    return arr[len-k];
}
```
就这样，但是数组顺式结构，可以一下子定位，而树是链式结构，只能一个个找。那么就将看看如何在数组里面一个个的找第k大的数：
```java
/*
 *@param:arr:递增数组
 *@param:k:位置
 @return:第k大的数
 */
public findKth(int[] arr,int k){
    int len=arr.length;
    //在循环过程中找
    for(int i=len-1;i>=0;i--){
        if(--k==0){
            return arr[i];
        }
    }
    //k比数组长达还要大的情况
    return -1;
}
```
可以看到，就是**在倒序遍历数组的时候同时让k递减，直到k为0，就找到了**。
那么就还有一个小问题，二叉搜索树中序遍历都是递增啊，怎么递减遍历呢？之前说过，中序遍历就是**先去遍历左子树，然后操作根节点，最后再遍历右子树**。这样就相当于数组的顺式遍历。那么，这道题可以**先遍历右子树，然后操作根节点，最后遍历左子树**，这样就先当与数组的倒序遍历。
即：
```java
public void reverseTraversal(TreeNode node){
    if(node==null)
        return;
    reverseTraversal(node.right);
    //对节点进行操作
    //
    reverseTraversal(node.left);
}
```
那么像第一题一样，合并一下，**合并就是节点操作部分对照数组循环部分**。
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    //将一些变量设置为全局变量
    int k;
    int ans=-1;
    //遍历过程
    public void dfs(TreeNode node){
      if(node==null)
        return;
      dfs(node.right);
      //对照数组循环部分的操作
      if(--k==0){
        ans=node.val;
        return;
      }
      dfs(node.left);
    }
    public int kthLargest(TreeNode root, int k) {
      this.k=k;
      dfs(root);
      return ans;
    }
}
```

好，继续乘胜追击：
![](https://pic.downk.cc/item/5f7d37721cd1bbb86b51bb7e.png)
[783.二叉搜索树节点最小距离](https://leetcode-cn.com/problems/minimum-distance-between-bst-nodes/)
都看到第三题了，那么第一步大家肯定了解了吧：想想如果是递增数组的话，怎么找任意两个节点的差的最小值。首先，确定的是，**递增数组任意两个节点差的最小值肯定是某两个相邻节点的差**。有了这个就可以直到，依次比较两个相邻节点的差，然后记下最小值。
即：
```java
//这里假设arr数组最小长度为2
public int minDiff(int[] arr){
    int len=arr.length;
    int min=arr[len-1];
    for(int i=1;i<len;i++){
        int diff=arr[i]-arr[i-1];
        if(diff<min)
            min=diff;
    }
    return min;
}
```
可以看到，在遍历的过程中还要记住上一个位置的数，这样才可以得到差。那么为了能够实现树的遍历，我们再换种写法：
```java
//这里假设arr数组最小长度为2
public int minDiff(int[] arr){
    int len=arr.length;
    //记录上一个位置的数
    Integer last=null;
    int min=arr[len-1];
    for(int n:arr){
        if(last!=null){
            int diff=n-last;
            min=Math.min(diff,min);
        }
        last=n;
    }
    return min;

}
```
这里初始设置`last`为`null`，就是因为第一个数不能作为被减数。
好，剩下的事情就是将中序遍历和数组循环合并一下：
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public int min=Integer.MAX_VALUE;
    public Integer last=null;
    public void dfs(TreeNode node){
      if(node==null)
        return;
      dfs(node.left);
      if(last!=null)
        min=Math.min(node.val-last,min);
      last=node.val;
      dfs(node.right);
    }
    public int minDiffInBST(TreeNode root) {
      dfs(root);
      return min;
    }
}
```

那么就到最后一题了，稍微有点难度哈：
![](https://pic.downk.cc/item/5f7d3c121cd1bbb86b52ba0f.png)
![](https://pic.downk.cc/item/5f7d3c211cd1bbb86b52c01c.png)
[538.将二叉搜索树转换为累加树](https://leetcode-cn.com/problems/convert-bst-to-greater-tree/)
题目中这句话就是唯一的难点：
>使每个节点 `node` 的新值等于原树中大于或等于 `node.val` 的值之和。

那如果放在递增数组`arr`里面，对于索引为`index`的数`arr[index]`，那么就要变为`arr[index]+=arr[index+1]+···+arr[n-1]`;
所以，如果是递增数组，那么操作如下:
```java
public void convertArr(int[] arr){
    int len=arr.length;
    int sum=0;
    for(int i=len-1;i>=0;i--){
        arr[i]+=sum;
        sum=arr[i];
    }
    return;
}
```
还记得怎么样在二叉搜索树里面实现倒序吗？先遍历右子树，再操作节点，最后遍历左子树。
同时，这里操作了左右子树，所以再把模板改一下：
```java
public TreeNode inorderTraversal(TreeNode node){
    if(node==null)
        return null;
    node.right=inorderTraversal(node.right);
    //操作节点
    //
    node.left=inorderTraversal(node.left);
    return node;
}
```
之后就是合并工作了：
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    int sum=0;
    public TreeNode dfs(TreeNode node){
      if(node==null)
        return null;
      node.right=dfs(node.right);
      //
      node.val+=sum;
      sum=node.val;
      //
      node.left=dfs(node.left);
      return node;
    }
    public TreeNode bstToGst(TreeNode root) {
      if(root==null)
        return null;
      dfs(root);
      return root;
    }
}
```
中序遍历先讲到这里，但是二叉搜索树的故事还没有结束。后面还会有文章关于二叉搜索树的一些拓展。

最后，还是总结一下，遇到二叉搜索树的一些问题，可以**想想如果是单调递增的数组会是怎么样操作**，然后就可以**写出中序遍历的模型**，**遍历过程中对节点的操作就可以类比递增数组中循环的操作**。

题目都不难，思想很重要。希望阅读本文后可以多多练习，真正将方法掌握好。

$Peace！$