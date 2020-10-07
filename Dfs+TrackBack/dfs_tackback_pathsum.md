<!--
 * @Author: your name
 * @Date: 2020-07-15 11:08:06
 * @LastEditTime: 2020-07-15 21:33:30
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \LeetcodeMarkdown\Dfs+TrackBack\dfs_tackback_pathsum.md
--> 
# 深度优先搜索+回溯解决路径问题
在放题目之前，先来看看回溯算法的模板。
来自labuladong大佬的文章：[回溯算法套路详解](https://zhuanlan.zhihu.com/p/93530380)
```python
result=[]
def backtrack(路径，选择列表):
    if 满足结束条件:
        result.add(路径)
        return
    
    for 选择 in 选择列表:
        做选择
        backtrack(路径,选择列表)
        撤销选择
```
这样看来有点抽象，也可以转变一种说法：
**路径**: 可以是人生到你现在目前的所有经历
**选择**: 这个就是字面的意思
**选择列表**: 你可以想到并且可以做到的全部选择
**结束条件**: 走到了生命的尽头
这个过程就是，你成长到现在，在之前肯定做了不少的选择。而现在也可以做出想到并可以做到的选择，比如说你在工作中遇到了坎坷，决定放弃工作。但是，当你走到人生的尽头的时候，确觉得自己的人生并不精彩。加入给你第二次机会，你不会再选择虚度光阴。
人生并没有后悔药，但是算法里面却有。你可以回到曾经做选择的时候，这一段经历除了你知道以外，就当没有发生过。这个“回到曾经”就是回溯。你不会做出第二遍一样的决定，所以回溯也不会作出跟上一步一样的选择。

而以下的题目，也可以说跟上面讲的故事有点类似
先来看一道较为简单的题目：
![797所有可能路径](https://pic.downk.cc/item/5f0ea53a14195aa5942a029d.png)
[797所有可能路径](https://leetcode-cn.com/problems/all-paths-from-source-to-target/)

这道题并没有任何的限制条件，直接套模板即可。对于其中一个节点，假如说值为num，那么：
**路径**：从起点到节点num所经过点的列表。例如num=1，那么路径就为`[0,1]`。
**选择列表**：到了节点num，那么选择列表当然就是`graph[num]`。
**结束条件**：按照题目的要求，到达值为n-1的点就结束。即条件为`num=n-1`。
套入模板，同时如果给出了起点，记得在回溯之前就将起点加入路径：
```java
List<List<Integer>>res=new LinkedList();
/*@param: temp: 路径
 *@param: curNum: 当前的节点值
 *@param: endNum: 终点的节点值
 *@param: graph: 有向图的矩阵 
 */
public void dfs(LinkedList<Integer>temp,int curNum,int endNum,int[][] graph){
    //满足结束条件
    if(curNum==endNum){
        res.add(new LinkedList<>(temp));
        return;
    }
    //回溯过程
    for(int n:graph[curNum]){
        //做选择
        temp.add(n);
        //进入下一层
        dfs(temp,n,endNum,graph);
        //取消选择
        temp.removeLast();
    }
}
public List<List<Integer>>allPathSourceTarget(int[][] graph){
    LinkedList<Integer>temp=new LinkedList<>();
    //要在回溯之前先把起点的值加入路径
    temp.add(0);
    int endNum=graph.length-1;
    dfs(temp,0,endNum,graph);
    return res;
}
```
在看看二叉树里面的一道题：
![113路径总和](https://pic.downk.cc/item/5f0eaf6f14195aa5942db04c.png)
[113路径总和](https://leetcode-cn.com/problems/path-sum-ii/)
跟图在**路径**上是一样的，但是**选择列表**则很特殊。加入对于数据类型为```TreeNode```的节点```temp```，那么选择列表就是**不为空的子树**。例:
* ```temp.left!=null&&temp.right!=null```那么能够进行到下一步的选择就是```temp.left```和```temp.right```。
* ```temp.left!=null&&temp.right==null```那么能够进行到下一步的选择就是```temp.left```
* ```temp.left==null&&temp.right!=null```那么能够进行到下一步的选择就是```temp.right```

```temp.left!=null&&temp.right!=null```说明是**叶子节点**，已经到达的**结束条件之一**，而还要满足的条件就是**路径和达到题目要求**
那么再次套入模板:

```java
List<List<Integer>>res=new LinkedList<>();
/*
 *@param: node: 当前节点值
 *@param: sum: 路径和，这里没经过一个节点就减去相应节点的值，结束条件就是叶子节点的值为这个值
 *@param: temp: 路径
 */
public void dfs(TreeNode node,int sum,LinkedList<Integer>temp){
    //到达结束条件之一，叶子节点
    if(node.left==null&&node.right==null){
        //到达结束条件之二，路径和满足要求
        if(node.val==sum)
            res.add(new LinkedList<>(temp));
        return;
    }
    //这里对sum层层相减
    sum-=node.val;
    //进入下一层之前要记得判断子树是否存在
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
第一题是**没有任何限定条件**，就相当于把你能做到的所有选择，**能经历的所有人生都列出来**。而对于第二题，就是**在终点处有限定条件**，相当于你给自己定了个目标，希望在50岁时有1个亿。于是你现在开始做人生选择，投资，去工作，只要能让自己赚到钱。在你50岁时，**有些选择可以让你达到目标，于是你把它们都列出来。有些却无法达到，你将它们抛弃**。

还有一些题目的限定条件，十分过于明显，以致于**还没达到终点就可以知道这个选择行不通**。这时候就可以**提前将这个选择排除掉，叫做“剪枝”**。回到你希望在50岁时有1个亿的例子，有些人在正值壮年时被金钱的诱惑冲破了头脑，他们选择去赌博。可是经历了一段时间，就是几年，还没到不惑之年时，发现自己并不是赌神，走这条路只会越限越深。如果他们有后悔药，便可以及时止损，回到曾经做出选择的那个阶段，告诉自己不会在做一次。这就是剪枝的过程。

例如题目：
![不同路径III](https://pic.downk.cc/item/5f0eba2514195aa59430f64e.png)
[不同路径III](https://leetcode-cn.com/problems/unique-paths-iii/)
那么对于这道题，选择列表就是上下左右4个方向。结束条件就是到达终点，即值为2的点。**没有到终点限定条件则是：1.不能越过边界；2.不能撞到障碍物，即不能走到值为-1的点**。
那么翻译成代码，就是：
```java
if(i<0||i==grid.length||j<0||j==grid[0].length||grid[i][j]==-1)
    return;
```
而**即使到了终点，也有限定条件: 如果值为0的点不是全部都经历过的话也不满足要求**。所以还要再**加一个值来记录经过的0的数量**。
那么完整的代码如下：
```java
    private int sum=0;
    //方向的集合
    private int [][]direction={{1,0},{-1,0},{0,1},{0,-1}};
    /*@param: grid:矩阵
     *@param: row: 当前点的横坐标
     *@param: col: 当前点的纵坐标
     *@param: step: 要走的步数。这里没经过一个节点就减去1，结束条件为step==0
     */
public void trackBack(int[][]grid,int row,int col,int step){
      //一定不满足条件的点，剪枝
      if(row==-1||row==grid.length||col==-1||col==grid[0].length||grid[row][col]==-1)
        return;
      //到达终点
      if(grid[row][col]==2){
        //记录全部0都经过的路径
        if(step==0)
          sum++;
        return;
      }
      //进入回溯
      for(int i=0;i<direction.length;i++){
        //这点已经经过，标记为障碍物
        grid[row][col]=-1;
        //进入下一层
        int nrow=row+direction[i][0];
        int ncol=col+direction[i][1];
        trackBack(grid,nrow,ncol,step-1);
        //将这点重新标记为可以通过的点。相当于从路径中拿掉
        grid[row][col]=0;
      }
}
public int uniquePathsIII(int[][] grid) {
      if(grid==null) return 0;
      int row=0;
      int col=0;
      int step=0;
      for(int i=0;i<len;i++){
        for(int j=0;j<wid;j++){
          //记录步数
          if(grid[i][j]==0||grid[i][j]==2) step++;
          //找到了起点
          else if(grid[i][j]==1){
            row=i;
            col=j;
          }
        }
      }
      trackBack(grid,row,col,step);
      return sum;
}
```
虽然套入模板可以解出题目，但是还可以用一个小技巧加快速度。比如说在进入下一层递归之前就将一些不符合要求的条件给排除掉。只将符合要求的点进入下一层递归。这就相当于，你确实想过用各种方法达到50岁时能够资产1个亿的目标，但是有前辈告诉你有些事情千万不要去做，或者有些方法你自己也不会去做，例如坑蒙拐骗，贩毒等等，这些在你执行之前就已经排除在选择列表以外了。
那么对于这道题，上面的语言翻译成代码就是
```java
//这里单独把矩阵的长和宽提了出来，就是len和wid
//跟上面一样
grid[row][col]=-1
//判断是否符合条件
if(row-1>=0&&grid[row-1][col]!=-1)
    trackBack(grid,len,wid,row-1,col,step-1);
if(row+1<len&&grid[row+1][col]!=-1)
    trackBack(grid,len,wid,row+1,col,step-1);
if(col-1>=0&&grid[row][col-1]!=-1)
    trackBack(grid,len,wid,row,col-1,step-1);
if(col-1<wid&&grid[row][col+1]!=1)
    trackBack(grid,len,wid,row,col+1,step-1);
//跟上面一样
grid[row][col]=0
```
那么完整的代码为:
```java
private int sum=0;
public void trackBack(int[][] grid,int len,int wid,int row,int col,int step){
    if(grid[row][col]==2){
        if(step==0)
            sum++;
        return;
    }
    //跟上面一样
    grid[row][col]=-1
    //判断是否符合条件
    if(row-1>=0&&grid[row-1][col]!=-1)
        trackBack(grid,len,wid,row-1,col,step-1);
    if(row+1<len&&grid[row+1][col]!=-1)
        trackBack(grid,len,wid,row+1,col,step-1);
    if(col-1>=0&&grid[row][col-1]!=-1)
        trackBack(grid,len,wid,row,col-1,step-1);
    if(col-1<wid&&grid[row][col+1]!=1)
        trackBack(grid,len,wid,row,col+1,step-1);
    //跟上面一样
    grid[row][col]=0
}
public int uniquePathsIII(int[][] grid) {
    if(grid==null) return 0;
    int row=0;
    int col=0;
    int step=0;
    int len=grid.length;
    int wid=grid[0].length;
    for(int i=0;i<len;i++){
        for(int j=0;j<wid;j++){
            //记录步数
            if(grid[i][j]==0||grid[i][j]==2) step++;
            //找到了起点
            else if(grid[i][j]==1){
                row=i;
                col=j;
          }
        }
    }
    trackBack(grid,len,wid,row,col,step);
    return sum;
}
```
同时看看下一道题目
![黄金矿工](https://pic.downk.cc/item/5f0efd1014195aa59446305a.png)
[黄金矿工](https://leetcode-cn.com/problems/path-with-maximum-gold/)
如果是进入下一轮递归之后再剪枝的话，即代码如下：
```java
class Solution {
    private int maxSum=0;
    private int [][]position={{1,0},{-1,0},{0,1},{0,-1}};
    public void trackBack(int [][]grid,int wid,int len,int x,int y,int sum){
      //到达结束条件
      if(x<0||x>=wid||y<0||y>=len||grid[x][y]==0){
        maxSum=Math.max(sum,maxSum);
        return;
      }
      int temp=grid[x][y];
      sum+=grid[x][y];
      grid[x][y]=0;
      for(int i=0;i<4;i++){
        int newX=x+position[i][0];
        int newY=y+position[i][1];
        trackBack(grid,wid,len,newX,newY,sum);
      }
      grid[x][y]=temp;
    }
    public int getMaximumGold(int[][] grid) {
      if(grid.length==0||grid[0].length==0)
        return 0;
      int wid=grid.length;
      int len=grid[0].length;
      for(int i=0;i<wid;i++){
        for(int j=0;j<len;j++){
          if(grid[i][j]!=0)
            trackBack(grid,wid,len,i,j,0);
        }
      }
      return maxSum;
    }
}
```
那么执行的结果为：
![](https://pic.downk.cc/item/5f0efe5b14195aa594468dea.png)
但是如果提前判断不能通过的条件的话，即代码如下：
```java
class Solution {
    private int maxSum=0;
    public void trackBack(int[][] grid,int wid,int len,int x,int y,int sum){
      //如果提前判断的话，就不存在所谓的终点了，就得走一步看一步是不是最大值
      sum+=grid[x][y];
      maxSum=Math.max(maxSum,sum);
      //
      int temp=grid[x][y];
      grid[x][y]=0;
      //
      if(x-1>=0&&grid[x-1][y]!=0)
        trackBack(grid,wid,len,x-1,y,sum);
      //
      if(x+1<wid&&grid[x+1][y]!=0)
        trackBack(grid,wid,len,x+1,y,sum);
      //
      if(y-1>=0&&grid[x][y-1]!=0)
        trackBack(grid,wid,len,x,y-1,sum);
      //
      if(y+1<len&&grid[x][y+1]!=0)
        trackBack(grid,wid,len,x,y+1,sum);
      //
      grid[x][y]=temp;
    }
    public int getMaximumGold(int[][] grid) {
      if(grid.length==0||grid[0].length==0)
        return 0;
      int wid=grid.length;
      int len=grid[0].length;
      for(int i=0;i<wid;i++){
        for(int j=0;j<len;j++){
          if(grid[i][j]!=0)
            trackBack(grid,wid,len,i,j,0);
        }
      }
      return maxSum;
    }
}
```
那么此时的结果为：
![](https://pic.downk.cc/item/5f0eff4c14195aa59446e954.png)
可以看到时间减少一半还多。所以在提高速度方面，一些小技巧还是比较有用处的
那么，我们再看看最后一道题目：
![单词搜索](https://pic.downk.cc/item/5f0f00e114195aa594475d2f.png)
[单词搜索](https://leetcode-cn.com/problems/word-search-ii/)
这道题还用到了前缀树，这个结构后面我会来讲。
跟矿工的题目差不多，如果是套模板加剪枝的话，代码如下：
```java
//前缀树结构
class TrieNode{
  public int end;
  public HashMap<Character,TrieNode>next;
  public TrieNode(){
    end=0;
    next=new HashMap<>();
  }
}
class Trie{
  private TrieNode root;
  public Trie(){
    root=new TrieNode();
  }
  public void insert(String word){
    if(word==null||word.equals(""))
      return;
    TrieNode node=root;
    for(int i=0;i<word.length();i++){
      char ch=word.charAt(i);
      if(!node.next.containsKey(ch))
        node.next.put(ch,new TrieNode());
      node=node.next.get(ch);
    }
    node.end++;
  }
  public boolean search(String word){
    if(word==null||word.equals(""))
      return false;
    TrieNode node=root;
    for(int i=0;i<word.length();i++){
      char ch=word.charAt(i);
      if(!node.next.containsKey(ch))
        return false;
      node=node.next.get(ch);
    }
    return node.end!=0;
  }
  public boolean startWith(String prefix){
    if(prefix==null||prefix.equals(""))
      return false;
    TrieNode node=root;
    for(int i=0;i<prefix.length();i++){
      char ch=prefix.charAt(i);
      if(!node.next.containsKey(ch))
        return false;
      node=node.next.get(ch);
    }
    return true;
  }
  public boolean startWith(char c){
    return root.next.containsKey(c);
  }
}
class Solution {
    Set<String>res=new HashSet<>();
    public void dfs(char[][] board,int col,int row,int i,int j,Trie trie, String temp){
      if(i<0||j<0||i>=col||j>=row||board[i][j]=='$')
        return;
      temp+=board[i][j];
      //剪枝
      if(!trie.startWith(temp))
        return;
      if(trie.search(temp))
        res.add(temp);
      char currentCharacter=board[i][j];
      //进入下一层递归
      board[i][j]='$';
      dfs(board,col,row,i-1,j,trie,temp);
      dfs(board,col,row,i+1,j,trie,temp);
      dfs(board,col,row,i,j-1,trie,temp);
      dfs(board,col,row,i,j+1,trie,temp);
      board[i][j]=currentCharacter;
    }
    public List<String> findWords(char[][] board, String[] words) {
      int col=board.length;
      int row=board[0].length;
      Trie trie=new Trie();
      for(String word:words){
        trie.insert(word);
      }
      for(int i=0;i<col;i++){
        for(int j=0;j<row;j++){
          if(trie.startWith(board[i][j])){
            dfs(board,col,row,i,j,trie,new String());
          }
        }
      }
      return new ArrayList<>(res);
    }
}
```
那么执行结果为：
![](https://pic.downk.cc/item/5f0f021014195aa59447aad6.png)
如果在进入下一层之前就排除一定不通过的条件，代码如下：
```java
class TrieNode{
  public int end;
  public HashMap<Character,TrieNode>next;
  public TrieNode(){
    end=0;
    next=new HashMap<>();
  }
}
class Trie{
  private TrieNode root;
  public Trie(){
    root=new TrieNode();
  }
  public void insert(String word){
    if(word==null||word.equals(""))
      return;
    TrieNode node=root;
    for(int i=0;i<word.length();i++){
      char ch=word.charAt(i);
      if(!node.next.containsKey(ch))
        node.next.put(ch,new TrieNode());
      node=node.next.get(ch);
    }
    node.end++;
  }
  public boolean search(String word){
    if(word==null||word.equals(""))
      return false;
    TrieNode node=root;
    for(int i=0;i<word.length();i++){
      char ch=word.charAt(i);
      if(!node.next.containsKey(ch))
        return false;
      node=node.next.get(ch);
    }
    return node.end!=0;
  }
  public boolean startWith(String prefix){
    if(prefix==null||prefix.equals(""))
      return false;
    TrieNode node=root;
    for(int i=0;i<prefix.length();i++){
      char ch=prefix.charAt(i);
      if(!node.next.containsKey(ch))
        return false;
      node=node.next.get(ch);
    }
    return true;
  }
  public boolean startWith(char c){
    return root.next.containsKey(c);
  }
}
class Solution {
    Set<String>res=new HashSet<>();
    public void dfs(char[][] board,int col,int row,int i,int j,Trie trie, String temp){
      temp+=board[i][j];
      if(!trie.startWith(temp))
        return;
      if(trie.search(temp))
        res.add(temp);
      char currentCharacter=board[i][j];
      board[i][j]='$';
      //
      if(i-1>=0&&board[i-1][j]!='$')
        dfs(board,col,row,i-1,j,trie,temp);
      //
      if(i+1<col&&board[i+1][j]!='$')
      dfs(board,col,row,i+1,j,trie,temp);
      //
      if(j-1>=0&&board[i][j-1]!='$')
        dfs(board,col,row,i,j-1,trie,temp);
      //
      if(j+1<row&&board[i][j+1]!='$')
        dfs(board,col,row,i,j+1,trie,temp);
      //
      board[i][j]=currentCharacter;
    }
    public List<String> findWords(char[][] board, String[] words) {
      if(board.length==0||board[0].length==0||words.length==0)
        return new ArrayList<>();
      int col=board.length;
      int row=board[0].length;
      Trie trie=new Trie();
      for(String word:words){
        trie.insert(word);
      }
      for(int i=0;i<col;i++){
        for(int j=0;j<row;j++){
          if(trie.startWith(board[i][j])){
            dfs(board,col,row,i,j,trie,new String());
          }
        }
      }
      return new ArrayList<>(res);
    }
}
```
那么执行结果如下：
![](https://pic.downk.cc/item/5f0f033014195aa59447fb5f.png)

那么，回溯算法+深度优先搜索解决路径问题就先讲到这里。只要记住在进入完下一步递归之后要撤销选择。同时为了加快速度，可以使用剪枝，如果有些条件一定不能通过，那么在进入下一层之前就可以排除掉。这些道理可能说的比较简单，但是本人为了搞清楚回溯，也是花了快两个星期的时间，看大佬的文章和专业书籍，通过大量的练习才搞懂一二。所以要想提高算法，必须要经过实践。

最后，想说一句，在算法里面确实有回溯和剪枝。**但是人生没有剪枝，人生更没有回溯**，希望每个人都能在做出选择之前都能慎重考虑，为每一步自己的选择负责。引入《钢铁是怎样炼成的》里面的一句话:
>当他回首往事的时候，他不会因虚度年华而悔恨，也不会因碌碌无为而羞耻。

*peace!*
