# 树里面的前序，中序，后序遍历的拓展（一）

笔者在写这篇文章之前，就想起了在大学期间学习数据结构树的这一章，当时初学前序，中序和后续遍历，要手写遍历以后的结果。那时笔者较为愚钝，有时候思考良久都没写出来，甚至有时候还怀疑：树的遍历直接按照层去遍历就行了呗，为啥还要搞这些这么复杂的？后来，笔者接触到了leetcode，发现这三种遍历的拓展以及根据这三种遍历的改造简直解决了大量的树的问题，而这三种遍历也是树里面的深度优先搜索。所以，这篇文章也希望能够帮助大家更清楚认识前序，中序和后续遍历。

那么话不多说，先来看看这三种遍历的模板：
```java
void traversal(TreeNode node){
    //空结点想都不要想，直接退
    if(node==null)
        return;
    
    //在这里执行对节点操作的就是前序遍历
    traversal(node.left);
    //在这里执行对节点的操作就是中序遍历
    traversal(node.right);
    //在这里执行对节点的操作就是后续遍历
}
```
那么什么情况需要考虑这三种遍历呢？可以通过模板看出来，**只要有关左右子树作为一个整体去操作，还有对节点进行操作的就可以考虑这三种情况**。像层序遍历，并没有以左右子树作为一个整体去操作，因为在**左子树还没有操作完全时就跨到了右子树**，所以层序遍历就不属于这三种。

那么思考范围缩小到这三个，如何更准确地判断是哪一个呢？再次回到模板。模板里看出，先操作当前节点，再操作左右子树就是前序；先操作当左节点，再操作当前节点，最后操作右子树就是中序；先操作左右子树，再操作当前节点就是后序。

:confused:呃，这么说还是觉得有点抽象。那更直接点，**你不知道左右子树得出的结果，发生的改变，现在的状态就操作当前节点就是先序遍历**，**你已经知道了左右子树得出的结果，发生的改变，现在的状态然后才对当前节点进行操作就是后续**。中序遍历也类似。其实还可以进一步缩小范围，一般都考虑前序和后序，**中序是在说明二叉搜索树时考虑较多**。

讲了这么久，该放点题目了：
![](https://pic.downk.cc/item/5f6abf81160a154a67a1f3c7.png)
[路径总和](https://leetcode-cn.com/problems/path-sum/)
根据题目的图片，就可以看出是先考虑根节点，然后记录路径和，最后层层遍历到叶子节点，如果到了叶子节点，并且路径和为sum，就说明存在这条路径。起点根节点，终点是叶子节点。那么**很明显，是在不知道左右子树是否存在路径的情况下先对根节点进行操作，所以考虑先序遍历**。

那么先序的模板：
```java
void traversal(TreeNode node){
    if(node==null)
        return;
    //进行与节点有关的操作
    //
    //操作左右子树
    traversal(node.left);
    traversal(node.right);
}
```
根据题目要求，对模板进行改造一下：
```java
boolean traversal(TreeNode node, int sum){
    if(node==null)
        return false;
    //进行与节点有关的操作
    //
    //操作左右子树
    if(traversal(node.left,sum)) return true;
    if(traversal(node.right,sum)) return true;
    return false;

}
```
那么根据题目要求，要找路径和。**那么到了一个节点，就用sum减去这个节点值。只要到了叶子节点，sum为0就说明找到了**。
那么对节点的操作就为：
```java
sum-=node.val;
if(node.left==null&&node.right==null&&sum==0)
    return true;
```
所以完整代码如下:
```java
class Solution{
    boolean traversal(TreeNode node, int sum){
        if(node==null)
            return false;
        //进行与节点有关的操作
        sum-=node.val;
        if(node.left==null&&node.right==null&&sum==0)
            return true;
        //操作左右子树
        if(traversal(node.left,sum)) return true;
        if(traversal(node.right,sum)) return true;
        return false;

    }
    public boolean hasPathSum(TreeNode root, int sum){
        return traversal(root,sum);
    }
}
```
再看看这道题的拓展：
![](https://pic.downk.cc/item/5f6ac482160a154a67a3ab00.png)
[路径总和II](https://leetcode-cn.com/problems/path-sum-ii/)
这不就跟第一题很像吗？只是要吧路径给写出来而已。可以把路径表式出来的方法不就是回溯吗？那就把这两个结合一下：
结合以后的模板：
```java
List<List<Integer>>res=new LinkedList<>();
/*
 *@param: node: 当前节点值
 *@param: sum: 路径和，这里没经过一个节点就减去相应节点的值，结束条件就是叶子节点的值为这个值
 *@param: temp: 路径
 */
public void dfs(TreeNode node,int sum,LinkedList<Integer>temp){
    if(node==null)
        return;
    //对节点进行操作
    //
    //对左子树进行操作
    if(node.left!=null){
        temp.add(node.left.val);
        dfs(node.left,sum,temp);
        temp.removeLast();
    }
    //对右子树进行操作
    if(node.right!=null){
        temp.add(node.right.val);
        dfs(node.right,sum,temp);
        temp.removeLast();
    }
}
```
那么完整代码如下：
```java
List<List<Integer>>res=new LinkedList<>();
/*
 *@param: node: 当前节点值
 *@param: sum: 路径和，这里没经过一个节点就减去相应节点的值，结束条件就是叶子节点的值为这个值
 *@param: temp: 路径
 */
public void dfs(TreeNode node,int sum,LinkedList<Integer>temp){
    if(node==null)
        return;
    //对节点进行操作
    if(node.left==null&&node.right==null){
        if(node.val==sum)
            res.add(new LinkedList<>(temp));
        return;
    }
    sum-=node.val;
    //对左右子树进行操作
    if(node.left!=null){
        temp.add(node.left.val);
        dfs(node.left,sum,temp);
        temp.removeLast();
    }
    if(node.right!=null){
        temp.add(node.right.val);
        dfs(node.right,sum,temp);
        temp.removeLast();
    }
}
public List<List<Integer>>pathSum(TreeNode root,int sum){
    if(root==null)
        return res;
    LinkedList<Integer>temp=new LinkedList<>();
    //还是不要忘记，在回溯之前将起点加入路径
    temp.add(root.val);
    dfs(root,sum,temp);
    return res;
}
```
再看看下一个类型的题：
![](https://pic.downk.cc/item/5f6af832160a154a67b7cce8.png)
[祖父节点值为偶数的节点和](https://leetcode-cn.com/problems/sum-of-nodes-with-even-valued-grandparent/)
根据题目意思，**要先判断其祖父节点的值是否是偶数，是偶数以后才将它的值算进去**，所以这也是一种前序遍历，只不过跨了两层，之前的直接遍历儿子节点，而这次还要判断父亲节点。

那么还是要把模板改一下：
```java
//返回值
private int res=0;
/*
*@param: node: 当前节点值
*@param: father: 父亲节点
*/
public void dfs(TreeNode node,TreeNode father){
    //老规矩
    if(node==null)
        return;
    //对节点进行操作
    if(father!=null&&father.val%2==0){
        //进行相关操作

    }
    //对左右节点进行操作
    dfs(node.left,node);
    dfs(node.right,node);
}
```
再看看题目要求，如果祖父节点的值是偶数，则加入和。所以如果转化成节点的角度，就是说：**如果父亲节点的值是偶数，那么就把该节点的儿子节点算入和里面去**。
所以相关的操作如下：
```java
if(father!=null&&father.val%2==0){
    if(node.left!=null)
        res+=node.left.val;
    if(node.right!=null)
        res+=node.right.val;
}
```
那么完整代码如下：
```java
class Solution{
    private int res=0;
    /*
    *@param: node: 当前节点值
    *@param: father: 父亲节点
    */
    public void dfs(TreeNode node,TreeNode father){
        //老规矩
        if(node==null)
            return;
        //对节点进行操作
        if(father!=null&&father.val%2==0){
            //进行相关操作
             if(node.left!=null)
                res+=node.left.val;
            if(node.right!=null)
                res+=node.right.val;
        }
        //对左右节点进行操作
        dfs(node.left,node);
        dfs(node.right,node);
    }
    public int sumEvenGrandpa(){
        if(root==null)
            return 0;
        dfs(root,null);
        return res;
    }
}
```
那么接下来看看后续遍历(中序遍历下一篇再讨论):

说到后续遍历的拓展，那么数量可就多了。从最简单的计算二叉树的深度，计算二叉树的节点数来说都有后续遍历的影子，不信的话就看看代码。
计算二叉树的深度：
```java
public int height(TreeNode node){
    //空节点深度为0
    if(node==null)
        return 0;
    //左子树，右子树的深度取最大值加1
    int l=height(node.left);
    int r=height(node.right);
    return Math.max(l,r)+1;
}
```

>二叉树的深度定义如下：
1.空二叉树的话深度为0
2.左子树，右子树深度的最大值+1

所以,**已经知道了左右子树的深度了，才可以知道现节点的深度**。这就有后续遍历的影子。
计算二叉树的节点数：
```java
public int count(TreeNode node){
    if(node==null)
        return 0;
    return coumt(node.left)+count(node.right)+1;
}
```
这也是知道了左右子树的节点数才能知道整个树的节点数。

那接着看点比较难办的：
![](https://pic.downk.cc/item/5f6b0102160a154a67bb4b9a.png)
[删除给定节点的值](https://leetcode-cn.com/problems/delete-leaves-with-a-given-value/)
笔者记得第一次写这道题的时候，首先就找叶子节点，然后删除，然后还要判断删除以后的父亲节点是不是又变成了叶子节点。反正代码又长又不好理解。但是如果学会了后续遍历的思想呢？

对于节点node，可以**假定它的左右子树都已经按照题目要求把该删除的节点删除完了，然后再看看node是否也变成了值为target的叶子节点，如果是，就返回空节点；如果不是，就返回node**。
好，现在把代码的模板修改一下：
```java
public TreeNode dfs(TreeNode node,int target){
    if(node==null)
        return null;
    //假定左右节点已经操作完了
    node.left=dfs(node.left,target);
    node.right=dfs(node.right,target);
    //操作node
    //
}
```
那么根据题目的意思，操作node的代码就是
```java
if(node.left==null&&node.right==null&&node.val==target)
    return null;
return node;
```
完整的代码如下：
```java
class Solution{
    public TreeNode dfs(TreeNode node,int target){
        if(node==null)
            return null;
        //假定左右节点已经操作完了
        node.left=dfs(node.left,target);
        node.right=dfs(node.right,target);
        //操作node
        if(node.left==null&&node.right==null&&node.val==target)
            return null;
        return node;
    }
    public TreeNode removeLeafNodes(TreeNode node,int target){
        if(node==null)
            return null;
        return dfs(node);
    }
}
```
再来一个相似的：
![](https://pic.downk.cc/item/5f6b0483160a154a67bcdb52.png)
[删点成林](https://leetcode-cn.com/problems/delete-nodes-and-return-forest/)
跟上面的思考类似，假如左右子树已经删除好了，然后判断该节点的值是否在删除的表里面，如果在的话就把左右子树加入列表。
```java
class Solution {
    private List<TreeNode>res=new ArrayList<>();
    public TreeNode dfs(TreeNode node, Set<Integer>set){
        if(node==null)
            return null;
        //假设左右子树已经删除好了
        node.left=dfs(node.left,set);
        node.right=dfs(node.right,set);
        //对节点进行操作
        if(set.contains(node.val)){
            if(node.left!=null) res.add(node.left);
            if(node.right!=null) res.add(node.right);
            return null;
        }
        return node;
    }
    public List<TreeNode> delNodes(TreeNode root, int[] to_delete) {
        //讲数组转换为set
        Set<Integer>set=new HashSet<>();
        for(int n:to_delete)
            set.add(n);
        root=dfs(root,set);
        //这里比较关键，由于添加进表的原理是如果根节点被删除左右子树加入，如果根节点没删的话还是要把根节点加入表
        if(root!=null)
            res.add(root);
        return res;

    }
}
```
再来看点不一样的：
![](https://pic.downk.cc/item/5f6b0712160a154a67bded6e.png)
[二叉树的直径](https://leetcode-cn.com/problems/binary-tree-tilt/)
虽然这道题标记为简单，但是思想还挺重要的，甚至有些难题的思想都差不多：
根据题目说的，**一个树的结点的坡度的定义即为，该节点左子树结点之和和右子树结点之和差的绝对值，空结点的坡度为0**，便可知道思路肯定是后续遍历。
所以先写一个函数来返回结点之和：
```java
public int dfs(TreeNode node){
    if(node==null)
        return 0;
    //假设知道了左右子树的结点之和
    int left=dfs(node.left);
    int right=dfs(node.right);
    //返回以该结点为根节点的树的节点之和
    return left+right+node.val;
}
```
但是题目中还说了，整个树的坡度就是其所有节点之和。所以还要定义一个全局变量来在遍历中添加每个节点的坡度。
```java
//定义全局变量
private int res=0;
public int dfs(TreeNode node){
    if(node==null)
        return 0;
    //假设知道了左右子树的结点之和
    int left=dfs(node.left);
    int right=dfs(node.right);
    //添加每个节点的坡度
    res+=Math.abs(left-right);
    //返回以该结点为根节点的树的节点之和
    return left+right+node.val;
}
```
那么完整的代码如下:
```java
class Solution {
    int res=0;
    public int dfs(TreeNode node){
      if(node==null)
        return 0;
      int left=dfs(node.left);
      int right=dfs(node.right);
      int sum=left+right+node.val;
      res+=Math.abs(left-right);
      return sum;
    }
    public int findTilt(TreeNode root) {
      dfs(root);
      return res;
    }
}
```

后续遍历加全局变量的组合在很多中等，难题里面都可以看到：
![](https://pic.downk.cc/item/5f6b0a4b160a154a67bf3cbd.png)
[二叉树的直径](https://leetcode-cn.com/problems/diameter-of-binary-tree/)
先写一个函数来返回以节点node为根节点的路径的最大节点数。这个函数得到的结果要返回给上面组成路径，所以只能选择左右子树的一个。如果都选就有分差，不满足题目的要求：
```java
public int dfs(TreeNode node){
    if(node==null)
        return 0;
    int left=dfs(node.left);
    int right=dfs(node.right);
    //左右子树选一个
    return Math.max(left,right)+1;
}
```
但是题目要求中写道，这条路径可能不穿过根节点，**所以还是要设一个全局变量在遍历整棵树的过程中来得到最大路径结点数**。
```java
//增加的全局变量来保存最大节点数
private int ans=0;
public int dfs(TreeNode node){
    if(node==null)
        return 0;
    int left=dfs(node.left);
    int right=dfs(node.right);
    //遍历过程中得到最大值
    ans=Math.max(ans,left+right+1);
    //左右子树选一个
    return Math.max(left,right)+1;
}
```
完整的代码如下：
 ```java
class Solution {
    //增加的全局变量来保存最大节点数
    int ans=0;
    public int dfs(TreeNode node){
      if(node==null)
        return 0;
      int left=dfs(node.left);
      int right=dfs(node.right);
      //遍历过程中得到最大值
      ans=Math.max(ans,left+right+1);
      //左右子树选一个
      return Math.max(left,right)+1;
    }
    public int diameterOfBinaryTree(TreeNode root) {
      if(root==null||(root.left==null&&root.right==null))
        return 0;
      dfs(root);
      //由于ans是节点数，所以不要忘记-1
      return ans-1;
    }
}
```
再看最后一个困难的题：
![](https://pic.downk.cc/item/5f6b0dcb160a154a67c0ac39.png)
[二叉树中的最大路径和](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)
虽然是难题，但是跟上面两道题真的很像啊。
唯一的区别就是在左右子树选择的时候要考虑对**路径和是否有增加的作用，即是否大于0，如果小于0就不如不选。**
```java
//增加的全局变量来保存最大节点数
private int ans=Integer.MIN_VALUE;
public int dfs(TreeNode node){
    if(node==null)
        return 0;
    //如果得出是负数，那还不如不选
    int left=Math.max(dfs(node.left),0);
    int right=Math.max(dfs(node.right),0);
    //遍历过程中得到最大值
    ans=Math.max(ans,left+right+node.val);
    //左右子树选一个
    return Math.max(left,right)+node.val;
}
```
那么完整的代码如下：
```java
class Solution {
    int res=Integer.MIN_VALUE;
    public int dfs(TreeNode node){
      if(node==null)
        return 0;
      int left=Math.max(dfs(node.left),0);
      int right=Math.max(dfs(node.right),0);
      res=Math.max(left+right+node.val,res);
      return Math.max(left,right)+node.val;
    }
    public int maxPathSum(TreeNode root) {
      dfs(root);
      return res;
    }
}
```
看了这么多例子，应该知道了前序，中序，后续遍历不单单只是为了遍历输出节点，而是一种递归的思想，是先处理节点，还是先处理左右子树的思路。所以，在树里面，基础知识也十分重要，只有把最基本的遍历思路学清楚才可以解决更难的问题。而二叉树的遍历思路某种程度上也是图甚至是数组的遍历的基础。

希望在有了想法之后也可以多多练习，让它真正成为属于自己的知识。

$Peace!!$