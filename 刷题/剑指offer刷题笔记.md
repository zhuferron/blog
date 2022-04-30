---
title: 剑指offer刷题笔记
tags: 数据结构与算法
categories: 剑指offer
description: 这是笔者在力扣上刷剑指offer的题目的刷题笔记，持续更新!!!

---

## 3、数组中重复的数字

**题目描述**

找出数组中重复的数字。


在一个长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。

**示例**

输入：

```
[2, 3, 1, 0, 2, 5, 3]
```

输出：

```
2或3
```

**思路分析**

思路一：题目说要寻找一个重复的元素，因此可以考虑使用`HashSet`来解决该问题。集合中不能存在重复的元素，将数组中每个元素都添加到`Set`中，如果添加失败，则代表该元素是重复元素。使用这种方法相当于以空间换时间，**时间复杂度会最小**。

思路二：原地交换法，题目隐藏了条件就是`所有数字都在 0～n-1 的范围内`，因此我们可以考虑使用原地交换的方法。遍历数组，如果数组索引等于对应的元素，则跳过，否则；则进行交换，即把数值为n的元素放在数组的第n个索引，当发现第n个索引已经有相同的元素时，则返回重复的元素。

**代码**

思路一：

```java
class Solution {
    public int findRepeatNumber(int[] nums) {
        Set<Integer> set = new HashSet<>();
        int result = -1;

        for(int num:nums){
            if(!set.add(num)){
                result=num;
            }
        }
        return result;


    }
}
```

思路二：

```java
class Solution {
    public int findRepeatNumber(int[] nums) {

        for(int i=0; i<nums.length; i++){
            if(i == nums[i]){
                continue;
            }else{
                if(nums[i] == nums[nums[i]]) return nums[i];
                int temp = nums[i];
                nums[i]=nums[nums[i]];
                nums[temp]=temp;      
            }
        }
        return -1;
    }
}
```



## 4、二维数组中的查找

**题目描述**

在一个二维数组中（每个一维数组的长度相同），每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

**示例**

输入：

```
[[1,2,8,9],[2,4,9,12],[4,7,10,13],[6,8,11,15]],7
```

输出：

```
true
```

**思路分析**

因为这个一个排好序的二维数组，查找时考虑使用二分查找。然而由题目可知，这个二维数组的排序有一定规律，按照二分查找的思路就是选定一个mid点，然而进行查找。因此考虑选择二维数组右上方的元素作为mid，如果target等于mid，则找到；如果target大于mid，就说明target不在第一行，此时接着判断第二行和最后一列，循环即可；如歌targte小于mid，就说明target不在最后一列，此时接着判断第一行和倒数第二列，循环即可。

**代码**

```java
class Solution {
    public boolean findNumberIn2DArray(int[][] matrix, int target) {
        if(matrix.length == 0 || matrix[0].length == 0) return false;
        int i = 0;
        int j = matrix[0].length-1;
        boolean flag = false;
        while(i<=matrix.length-1 && j>=0){
            if(target == matrix[i][j]){
                flag=true;
                break;
            }
            else if(target > matrix[i][j]){
                i++;
            }
            else{
                j--;
            }
        }
        return flag;

    }
}
```

## 5、替换空格

**题目描述**

请实现一个函数，将一个字符串中的每个空格替换成“%20”。

**示例**

输入：

```
"We Are Happy"
```

输出：

```
"We%20Are%20Happy"
```

**思路分析**

题目需要替换字符串，因此考虑使用StringBuffer类来实现。新建一个StringBuffer，遍历原字符串，如果字符为空格，则在sb中添加一个%20，如果字符不为空格，则添加该字符。

**代码**

```java
public String replaceSpace (String s) {

    StringBuffer sb = new StringBuffer();

    for(int i=0; i<s.length(); i++){
        if(s.charAt(i)==' '){
            sb.append("%20");
        }
        else{
            sb.append(s.charAt(i));
        }
    }
    return sb.toString();     
}
```

## 6、从尾到头打印链表

**题目描述**

输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

**示例**

输入：

```
head = [1,3,2]
```

输出：

```
[2,3,1]
```

**思路分析**

思路一：从尾到头打印这个链表，会考虑到使用先进后出的栈来解决。

思路二：另外一个思路也可以首先遍历一遍链表，记录下其长度，然后建立一个相同长度的数组，从后往前赋值即可。

**代码**

思路一：

```java
class Solution {
    public int[] reversePrint(ListNode head) {
        Stack<ListNode> stack = new Stack<ListNode>();

        for(ListNode node=head; node!=null; node=node.next){
            stack.push(node);
        }
        int size = stack.size();  //记录栈长度，否则随着出栈栈长度会变小
        int[] result = new int[size];
        
        for(int i=0; i<size; i++){
            result[i]=stack.pop().val;
        }

        return result;

    }
}
```

思路二：

```java
class Solution {
    public int[] reversePrint(ListNode head) {
        
        ListNode tmp = head;
        int len = 0;
        while(tmp!=null){
            tmp=tmp.next;
            len++;
        }

        int[] array = new int[len];
        for(int j=len-1; j>=0; j--){
            array[j] = head.val;
            head=head.next;
        }
        return array;
    }
}
```

## 7、重建二叉树

**题目描述**

输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。

**示例**

输入：

```
前序遍历 preorder = [3,9,20,15,7]
中序遍历 inorder = [9,3,15,20,7]
```

输出：

```
    3
   / \
  9  20
    /  \
   15   7
```

**思路分析**

思路一：知道二叉树的前序遍历和中序遍历，可以考虑使用递归来解决。首先通过前序遍历得到该二叉树的root结点，示例中root结点为1。知道root结点后，通过中序遍历知道了root的左子树的前序遍历为`[9]`，中序遍历为`[9]`，右子树前序遍历为`[20,15,7]`，`[15,20,7]`，接着分别对root的左子树和右子树进行同样的递归操作即可。

思路二：整个思路跟思路一类似，不同的是为了提高效率，加入`HashMap`存储中序遍历的值与索引的映射

**代码**

思路一：

```java
class Solution {
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        if(preorder.length==0 | inorder.length==0){
                return null;
        }
            TreeNode root = new TreeNode(preorder[0]);

        for(int i=0; i<inorder.length; i++){
            //从前序遍历中找到中序遍历中的二叉树的根结点
            if(inorder[i] == preorder[0]){
                root.left=buildTree(Arrays.copyOfRange(preorder,1,i+1),Arrays.copyOfRange(inorder,0,i));
                root.right=buildTree(Arrays.copyOfRange(preorder,i+1,preorder.length),Arrays.copyOfRange(inorder,i+1,inorder.length));
                break;
            }
        }
        return root;
    }
}
```

思路二：

```java
class Solution {

    int[] preorder;
    HashMap<Integer,Integer> map = new HashMap<Integer, Integer>();

    public TreeNode buildTree(int[] preorder, int[] inorder) {

        this.preorder = preorder;
        for(int i=0; i<inorder.length; i++){
            map.put(inorder[i], i);
        }
        return recur(0,0,preorder.length-1);

        
    }
    TreeNode recur(int root, int left, int right) {
        //递归终止条件
        if(left>right) return null;
        //建立新结点
        TreeNode node = new TreeNode(preorder[root]);
        //查找出preorder[root]在inorder中的索引
        int i = map.get(preorder[root]);
        //左子树:跟结点为root+1，范围为preorder[left]-preorder[i-1]
        node.left=recur(root+1,left,i-1);
        //右子树:跟结点i-left+root+1(根结点索引+左子树长度+1)，范围为preorder[i+1]-preorder[right]
        node.right=recur(i-left+root+1,i+1,right);
        return node;
    }
        
        
}
```

## 9、两个栈来实现一个队列

**题目描述**

用两个栈来实现一个队列，完成队列的Push和Pop操作。 队列中的元素为int类型。

**思路分析**

考虑使用两个栈来实现一个队列，首先考虑的是该队列只进不出，这样就可以直接使用stack1来存储该“队列”的所有元素。一旦考虑到出列的情况，就相当于要把stack1的所有元素先倒置，即把stack1的元素全部装进stack2，这样stack2中的元素就是stack1中所有元素的倒置，这样出列操作就可以通过stack2的pop方法来完成。还有一些细节就是因为队列是先进先出，因为当需要出列的时候应该先检查stack2是否为空，如果为空就将stack1的元素放入，如果不为空则直接出列即可。

**代码**

```java
class CQueue {

    Stack<Integer> stack1;
    Stack<Integer> stack2;

    public CQueue() {
        stack1 = new Stack<Integer>();
        stack2 = new Stack<Integer>();
    }
    
    public void appendTail(int value) {
        stack1.push(value);
    }
    
    public int deleteHead() {
        if(stack2.isEmpty()){
            while(!stack1.isEmpty()){
                stack2.push(stack1.pop());
            }
        }
        if(stack2.isEmpty()){
            return -1;
        }else{
            return stack2.pop();
        }
 
    }
}

```

## 10-1、斐波那契数列

**题目描述**

写一个函数，输入 n ，求斐波那契（Fibonacci）数列的第 n 项（即 F(N)）。斐波那契数列的定义如下：

F(0) = 0,   F(1) = 1
F(N) = F(N - 1) + F(N - 2), 其中 N > 1.

斐波那契数列由 0 和 1 开始，之后的斐波那契数就是由之前的两数相加而得出。

答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。

**示例**

输入：

```
4
```

输出：

```
3
```

**思路分析**

这题最简单的思路便是递归，但是递归会产生许多的重复计算，因此可以考虑使用动态规划来优化。动态规划的状态转移方程便为斐波那契的通项公式`dp[n]=dp[n-1]+dp[n-2]`

**代码**

```java
class Solution {
    public int fib(int n) {
        if(n<=1){
            return n;
        }
        int a=0;
        int b=1;
        int sum;
        for(int i=0; i<n; i++){
            sum=(a+b)%1000000007;
            a=b;
            b=sum;
        }
        return a;
    }
}
```

## 10-2、跳台阶

**题目描述**

一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法（先后次序不同算不同的结果）。

**思路分析**

这道题跟前面的斐波那契数列很类似。我们可以用反向思维来理解，假设青蛙跳到第n阶台阶，那么它的上一步会在哪呢？要么在n-1阶台阶，要么在n-2阶台阶，这样我们就可以得到青蛙跳到第n阶台阶的跳法等于青蛙跳到第n-1阶台阶的跳法与跳到第n-2阶台阶的跳法之和。同样使用动态规划求解。

**代码**

```java
class Solution {
    public int numWays(int n) {
        int a=1;
        int b=1;
        int sum;
        for(int i=0; i<n; i++){
            sum=(a+b)%1000000007;
            a=b;
            b=sum;
        }
        return a;
    }
}
```

## 11、旋转数组的最小数字

**题目描述**

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。
 输入一个非递减排序的数组的一个旋转，输出旋转数组的最小元素。
 NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。

**示例**

输入：

```
[3,4,5,1,2]
```

输出：

```
1
```

**思路分析**

这题最开始没有思路，网上比较流行的就是使用二分搜索来完成，但是实现的过程也有一些细微的差异，下面列了一个我觉得较为理想的答案。首先mid大于high时，更新左边界值，而当mid小于high时，则更新右边界值，当两者相等时，很难判断，则让high自减1继续判断。

**代码**

```java
class Solution {
    public int minArray(int[] numbers) {
        if(numbers.length == 0){
            return 0;
        }
        
        int low = 0;
        int high = numbers.length-1;
        while(low<high){
            int mid=(low+high)/2;
            if(numbers[mid]>numbers[high]){
                low=mid+1;
            }else if(numbers[mid]==numbers[high]){
                high=high-1;
            }else{
                high=mid;
            }
        }
        return numbers[high];    
    }
}
```

## 12、矩阵中的路径

**题目描述**

给定一个 m x n 二维字符网格 board 和一个字符串单词 word 。如果 word 存在于网格中，返回 true ；否则，返回 false 。

单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。

**示例**

输入：

```
board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
```

输出：

```
true
```

**思路分析**

这道题目用的思想是回溯算法，典型的就是深度优先遍历+剪枝。逐个单元格进行深度优先遍历，一旦判断存在即返回true，否则返回false。

**代码**

```java
class Solution {
    public boolean exist(char[][] board, String word) {
        char[] array = word.toCharArray();
        for(int i=0; i<board.length; i++){
            for(int j=0; j<board[0].length; j++){
                if(dfs(board,array,i,j,0)) return true;
            }
        }
        return false;
    }

    public boolean dfs(char[][] board, char[] array, int i, int j, int k){
        //上下左右的元素越界或者不等于array[k]时，返回false
        if(i<0 || i>=board.length || j<0 || j>=board[0].length || board[i][j] != array[k]){
            return false;
        }
        //当k==array.length-1时，还未返回false则返回true
        if(k==array.length-1){
            return true;
        }
        board[i][j]='\0';
        boolean res = dfs(board, array, i+1, j, k+1) || dfs(board, array, i-1, j, k+1) || dfs(board, array, i, j+1, k+1) || dfs(board, array, i, j-1, k+1);
        board[i][j]=array[k];
        return res;
    }
}
```

## 13、机器人的运动范围

**题目描述**

地上有一个m行n列的方格，从坐标 [0,0] 到坐标 [m-1,n-1] 。一个机器人从坐标 [0, 0] 的格子开始移动，它每次可以向左、右、上、下移动一格（不能移动到方格外），也不能进入行坐标和列坐标的数位之和大于k的格子。例如，当k为18时，机器人能够进入方格 [35, 37] ，因为3+5+3+7=18。但它不能进入方格 [35, 38]，因为3+5+3+8=19。请问该机器人能够到达多少个格子？

**示例**

输入：

```
m = 2, n = 3, k = 1
```

输出：

```
3
```

**思路分析**

思路一：使用回溯算法，即深度优先算法，先定义一个计算数位和的函数，然后进行深度优先遍历。

思路二：使用广度优先算法，引入队列来辅助进行。

**代码**

思路一：

```java
class Solution {
    //计算数位和方法
    public int sums(int k){
        int s=0;
        while(k!=0){
            s+=k%10;
            k=k/10;
        }
        return s;
    }
    //全局变量
    int m,n,k;
    boolean[][] visited;
    public int movingCount(int m, int n, int k) {
        this.m=m; this.n=n; this.k=k;
        this.visited=new boolean[m][n];
        return dfs(0,0,0,0);
    }

    public int dfs(int i, int j, int xi, int xj){
        if(i>=m || j>=n || xi+xj>k || visited[i][j]) return 0;
        visited[i][j]=true;
        return 1+dfs(i+1,j,sums(i+1),sums(j))+dfs(i,j+1,sums(i),sums(j+1));
    }

}
```

思路二：

```java
class Solution {
    //计算数位和方法
    public int sums(int k){
        int s=0;
        while(k!=0){
            s+=k%10;
            k=k/10;
        }
        return s;
    }

    public int movingCount(int m, int n, int k) {
        boolean[][] visited = new boolean[m][n];
        Queue<int[]> queue = new LinkedList<int[]>();
        int sum=0;

        queue.add(new int[]{0,0,0,0});
        while(!queue.isEmpty()){
            int[] x = queue.poll();
            int i=x[0]; int j=x[1]; int xi=x[2]; int xj=x[3];
            if(i>=m || j>=n || xi+xj>k || visited[i][j]) continue;
            visited[i][j]=true;
            sum++;
            queue.add(new int[]{i+1,j,sums(i+1),sums(j)});
            queue.add(new int[]{i,j+1,sums(i),sums(j+1)});

        }
        return sum;
        
    }


}
```

## 14-1、剪绳子

**题目描述**

给你一根长度为 n 的绳子，请把绳子剪成整数长度的 m 段（m、n都是整数，n>1并且m>1），每段绳子的长度记为 k[0],k[1]...k[m-1] 。请问 k[0]*k[1]*...*k[m-1] 可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

**示例**

输入：

```
2
```

输出：

```
1
```

**思路分析**

思路一：使用动态规划算法，分析如下：首先dp[2]=1，然后当绳长i>=3时，我们可以假设剪掉一段长度为j的绳子，然后剩下的长度就是i-j，此时我们可以选择继续剪或者不剪。如果不剪的话乘积就为i×j，剪的话乘积就为i×dp[i-j]，取最大者。然后2<=j<i开始循环

思路二：使用贪心算法，先计算出n=2，3，4的情况，我们可以看出当n=2，3时，max分别等于1，2；n=4时，max=2×2=4，当n>4时，可以对n-=3，此时乘积是最大的。

**代码**

思路一：

```java
class Solution {
    public int cuttingRope(int n) {
        int[] dp = new int[n+1];
        dp[2] = 1;
        for(int i=3; i<n+1; i++){
            for(int j=2; j<i; j++){
                int temp = dp[i];
                int max = Math.max(j*(i-j),j*dp[i-j]);
                dp[i] = Math.max(temp,max);
            }
        }
        return dp[n];
    }
}
```

思路二：

```java
class Solution {
    public int cuttingRope(int n) {
        if(n==2) return 1;
        if(n==3) return 2;
        int res=1;
        while(n>4){
            n-=3;
            res*=3;
        }
        return res*n;
    }
}
```

## 14-2、剪绳子2

**题目描述**

给你一根长度为 n 的绳子，请把绳子剪成整数长度的 m 段（m、n都是整数，n>1并且m>1），每段绳子的长度记为 k[0],k[1]...k[m-1] 。请问 k[0]*k[1]*...*k[m-1] 可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。

**示例**

输入：

```
2
```

输出：

```
1
```

**思路分析**

这道题只能使用贪心算法，不能使用动态规划，因为结果需要取模返回不能使用max来找到上一步的最大乘积。

**代码**

```java
class Solution {
    public int cuttingRope(int n) {
        if(n==2) return 1;
        if(n==3) return 2;
        long res=1;  //注意使用long，用int会溢出
        while(n>4){
            res=res*3%1000000007;
            n-=3;
        }
        return (int)(res * n % 1000000007);

    }
}
```

## 15、二进制中1的个数

**题目描述**
输入一个整数，输出该数32位二进制表示中1的个数。其中负数用补码表示。
输入：

```
10
```

输出：

```
2
```

**思路分析**
本题运用的知识是按位与(&)运算和无符号右移(>>>)，当一个数的二进制进行无符号右移n位后，与1进行&运算，当其结果等于1时，则代表这个数二进制的第n位为1，反之为0。依据这个方法可以计算出一个二进制数的1的个数。
**代码**

```java
public class Solution {
    public int NumberOf1(int n) {
        int count = 0;
        for(int i = 0; i < 32; i++){
            if((n >>> i & 1) == 1)
                count++;
        }
        return count;
    }
}
```

## 16、数值的整数次方

**题目描述**

 给定一个double类型的浮点数base和int类型的整数exponent。求base的exponent次方。 

 保证base和exponent不同时为0 

输入：

```
2,3
```

输出：

```
8.00000
```

**思路分析**

思路一：无论使用哪种思路都需要考虑进行n的判断，当n小于0时，将x取分数并且n取反，当n等于0时，直接返回1。使用递归的快速幂求解，当我们要求一个数的8次方时，我们只需要将这个的4次方再平方即可，奇数次方则将其再乘多一个base即可，可构造递归。

思路二：仍然是使用快速幂求解，不同的是使用二进制的快速幂，用的是迭代而不是递归。

**代码**

```java
class Solution {
    public double q_power(double x, long b){
        if(b ==0){
            return 1;
        }
        double result = q_power(x,b/2);
        if(b%2 == 1){
            return result*result*x;
        }else{
            return result * result;
        }
    }

    public double myPow(double x, int n) {
        long b = n;
        if(n<0){
            x = 1 / x;
            b = -b;
        }
        return q_power(x,b);
    }
}
```

思路二：

```java
class Solution {
    public double myPow(double x, int n) {

        if(n==0) return 1;
        long b = n;  //使用long存储n，防止溢出
        double res = 1.0;
        if(n<0){
            x=1/x;
            b=-b;
        }
        while(b>0){
            if((b&1)==1) res*=x;
            x*=x;
            b>>=1;
        }
        return res;
    }
}
```

## 17、打印从1到最大的n位数

**题目描述**

输入数字 `n`，按顺序打印出从 1 到最大的 n 位十进制数。比如输入 3，则打印出 1、2、3 一直到最大的 3 位数 999。

输入：

```
1
```

输出：

```
[1,2,3,4,5,6,7,8,9]
```

**思路分析**

思路一：因为本题需要返回的是int[]数组，所有不存在大数越界的问题，思路一是一个很容易想到的思路

思路二：考虑大数越界的问题。本题的考点在于大数越界，因为无论使用int，float，double都会大数越界的问题，因此必须得使用String类型。而大数问题下，可以考虑使用全排列的思想来做。则该题转化成求出n个0-9的全排列。

**代码**

思路一：

```java
class Solution {
    public int[] printNumbers(int n) {
        int max=(int)Math.pow(10, n) - 1;
        int[] x = new int[max];
        for(int i=0; i<x.length; i++){
            x[i]=i+1;
        }
        return x;

    }
}
```

思路二：

```java
class Solution {
    int[] res;
    int count=0,nine=0, start,n;
    char[] num, loop={'0','1','2','3','4','5','6','7','8','9'};

    public int[] printNumbers(int n) {
        this.n=n;
        res = new int[(int)Math.pow(10, n) - 1];
        num = new char[n];
        start=n-1;
        dfs(0);
        return res;

    }

    void dfs(int x){
        if(x == n){  //当所有位都固定好时，终止
            String s = String.valueOf(num).substring(start); //去除高位0
            if(!s.equals("0")) res[count++] = Integer.parseInt(s);
            if(n - start == nine) start--;
            return;
        }
        for(char i :loop){
            if(i=='9') nine++;
            num[x]=i;
            dfs(x+1);
        }
        nine--;

    }


}
```

## 18、删除链表的节点

**题目描述**

给定单向链表的头指针和一个要删除的节点的值，定义一个函数删除该节点。

返回删除后的链表的头节点。
输入：

```
head = [4,5,1,9], val = 5
```

输出：

```
[4,1,9]
```

**思路分析**
本题的思路就是使用双指针，首先对特殊情况进行判断，当head的值为val时，直接返回head.next。然后令慢指针为head，快指针为head.next，然后当慢指针等于null或快指针的值等于val时跳出循环，令`low.next=fast.next`，就可以删除val所在的节点。
**代码**

```java
class Solution {
    public ListNode deleteNode(ListNode head, int val) {

        if(head.val == val) return head.next;

        ListNode low = head;
        ListNode fast = head.next;

        while(fast!=null && fast.val != val){
            low = fast;
            fast = fast.next;
        }
        if(fast != null) low.next=fast.next;
        return head;

    }
}
```

## 19、正则表达式匹配

**题目描述**

请实现一个函数用来匹配包含'. '和'*'的正则表达式。模式中的字符'.'表示任意一个字符，而'*'表示它前面的字符可以出现任意次（含0次）。在本题中，匹配是指字符串的所有字符匹配整个模式。例如，字符串"aaa"与模式"a.a"和"ab*ac*a"匹配，但与"aa.a"和"ab*a"均不匹配。
输入：

```
s = "aab"
p = "c*a*b"
```

输出：

```
true
```

**思路分析**
思路解析：https://leetcode-cn.com/problems/zheng-ze-biao-da-shi-pi-pei-lcof/solution/zhu-xing-xiang-xi-jiang-jie-you-qian-ru-shen-by-je/
**代码**

```java
class Solution {
    public boolean isMatch(String s, String p) {
        
        int n = s.length();
        int m = p.length();
        boolean[][] dp = new boolean[n+1][m+1];  //+1是为了判断空串

        for(int i=0; i<=n; i++){
            for(int j=0; j<=m; j++){
                //判断空正则与非空正则
                if(j == 0){
                    dp[i][j] = i ==0;
                }else{
                    //判断是否为*
                    if(p.charAt(j-1) != '*'){
                        if(i>0 &&(s.charAt(i-1) == p.charAt(j-1) || p.charAt(j-1) == '.')){
                            dp[i][j] = dp[i-1][j-1];
                        }
                    }else{
                        //不看*
                        if(j>=2){
                            dp[i][j] |= dp[i][j-2];
                        }
                        //看*
                        if(i>=1 && j>=2 && (s.charAt(i-1) == p.charAt(j-2) || p.charAt(j-2) == '.')){
                            dp[i][j] |= dp[i-1][j];
                        }
                    }
                }
            }
        }
        return dp[n][m];
    }

}
```

## 20、表示数值的字符串

**题目描述**

请实现一个函数用来判断字符串是否表示数值（包括整数和小数）。

数值（按顺序）可以分成以下几个部分：

    若干空格
    一个 小数 或者 整数
    （可选）一个 'e' 或 'E' ，后面跟着一个 整数
    若干空格

小数（按顺序）可以分成以下几个部分：

    （可选）一个符号字符（'+' 或 '-'）
    下述格式之一：
        至少一位数字，后面跟着一个点 '.'
        至少一位数字，后面跟着一个点 '.' ，后面再跟着至少一位数字
        一个点 '.' ，后面跟着至少一位数字

整数（按顺序）可以分成以下几个部分：

    （可选）一个符号字符（'+' 或 '-'）
    至少一位数字

部分数值列举如下：

    ["+100", "5e2", "-123", "3.1416", "-1E-16", "0123"]

部分非数值列举如下：

    ["12e", "1a3.14", "1.2.3", "+-5", "12e+5.4"]

输入：

```
s = "    .1  "
```

输出：

```
true
```

**思路分析**
思路解析：https://leetcode-cn.com/problems/biao-shi-shu-zhi-de-zi-fu-chuan-lcof/solution/mian-shi-ti-20-biao-shi-shu-zhi-de-zi-fu-chuan-y-2/

```java
class Solution {
    public boolean isNumber(String s) {
        Map[] states = {
            new HashMap<>() {{ put(' ', 0); put('s', 1); put('d', 2); put('.', 4); }}, // 0.
            new HashMap<>() {{ put('d', 2); put('.', 4); }},                           // 1.
            new HashMap<>() {{ put('d', 2); put('.', 3); put('e', 5); put(' ', 8); }}, // 2.
            new HashMap<>() {{ put('d', 3); put('e', 5); put(' ', 8); }},              // 3.
            new HashMap<>() {{ put('d', 3); }},                                        // 4.
            new HashMap<>() {{ put('s', 6); put('d', 7); }},                           // 5.
            new HashMap<>() {{ put('d', 7); }},                                        // 6.
            new HashMap<>() {{ put('d', 7); put(' ', 8); }},                           // 7.
            new HashMap<>() {{ put(' ', 8); }}                                         // 8.
        };
        int p = 0;
        char t;
        for(char c : s.toCharArray()) {
            if(c >= '0' && c <= '9') t = 'd';
            else if(c == '+' || c == '-') t = 's';
            else if(c == 'e' || c == 'E') t = 'e';
            else if(c == '.' || c == ' ') t = c;
            else t = '?';
            if(!states[p].containsKey(t)) return false;
            p = (int)states[p].get(t);
        }
        return p == 2 || p == 3 || p == 7 || p == 8;

    }
}
```

## 21、调整数组顺序使奇数位于偶数前面

输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有的奇数位于数组的前半部分，所有的偶数位于数组的后半部分，并保证奇数和奇数，偶数和偶数之间的相对位置不变。

输入：

```
[1,2,3,4]
```

输出：

```
[1,3,2,4]
```

**思路分析**

思路一：使用辅助数值，定义一个辅助数组，遍历两次输入数组，先放入奇数，再放入偶数，返回辅助数组。

思路二：使用双指针的思想，数组前后各有一个指针，一起移动，如果前指针为偶数且后指针为奇数，则前后指针数值交换，前后指针相遇则调整完成。

**代码**

思路一：

```java
class Solution {

    public int[] exchange(int[] nums) {
        int[] res = new int[nums.length];
        int count = 0;
        for(int i :nums){
            if(i%2 != 0){
                res[count++] = i;
            }
        }
        for(int i :nums){
            if(i%2 == 0){
                res[count++] = i;
            }
        }
        return res;
    }
}
```

思路二：

```java
class Solution {

    public int[] exchange(int[] nums) {
        int front=0, back = nums.length-1;
        while(front<back){

            if(nums[front]%2 ==0 && nums[back]%2 != 0){
                int temp = nums[front];
                nums[front] = nums[back];
                nums[back] = temp;
            }
            if(nums[front]%2 !=0) front++;
            if(nums[back]%2 ==0) back--;
                   
        }
        return nums;
    }
}
```

## 22、链表中倒数第k个结点

输入一个链表，输出该链表中倒数第k个节点。为了符合大多数人的习惯，本题从1开始计数，即链表的尾节点是倒数第1个节点。

例如，一个链表有 6 个节点，从头节点开始，它们的值依次是 1、2、3、4、5、6。这个链表的倒数第 3 个节点是值为 4 的节点。 

输入：

```
1->2->3->4->5, 和 k = 2.
```

输出：

```
4->5
```

**思路分析**

思路一：首先求出链表的长度len，然后将len与k进行比较，若k大于链表长度，则返回空，否则，就返回链表的第len-k个元素。

思路二：双指针法。首先定义一个快指针和一个慢指针，快指针先走k步，然后两个指针一起走，当快指针走到链尾时，慢指针刚好在倒数第k个结点。

**代码**

思路一：

```java
import java.util.*;
public class Solution {
    public ListNode FindKthToTail (ListNode pHead, int k) {
        int count=0;
        for(ListNode node = pHead; node!=null; node=node.next){
            count++;
        }
        if(k>count){
            return null;
        }else{
            int step = count-k;
            ListNode node = pHead;
            for(int i=0; i<step; i++){
                node=node.next;
            }
            return node;
        }
    }
}
```

思路二：

```java
import java.util.*;
public class Solution {
    public ListNode FindKthToTail (ListNode pHead, int k) {
        
        ListNode fast = pHead;
        ListNode slow = pHead;
        
        for(int i=0; i<k; i++){
            if(fast==null){
                return null;
            }
            fast=fast.next;
        }
        while(fast!=null){
            fast=fast.next;
            slow=slow.next;
        }
        return slow;
    }
}
```

## 24、反转链表

输入一个链表，反转链表后，输出新链表的表头。

输入：

```
{1,2,3}
```

输出：

```
{3,2,1}
```

**思路分析**

使用三个指针，三个指针分别为当前结点的前结点pre，当前结点head，当前结点的后结点next。对当前结点进行循环操作，首先使用next指针记录下当前结点head的下一个结点，然后将当前结点head的下一个结点设为pre，此时这局部的三个结点的前面两个结点就相当于反转了，而后面的结点还在，然后将pre设为head，将head设为next，继续循环即可。

**代码**

```java
public class Solution {
    public ListNode ReverseList(ListNode head) {
        
        ListNode pre=null;
        ListNode next=null;
        
        while(head!=null){
            next=head.next;
            head.next=pre;
            pre=head;
            head=next;
        }
        return pre;
    }
}
```

## 25、合并两个排序的链表

输入两个递增排序的链表，合并这两个链表并使新链表中的节点仍然是递增排序的。

输入：

```
1->2->4, 1->3->4
```

输出：

```
1->1->2->3->4->4
```

**思路分析**

使用最容易想到的遍历即可，比较两个链表节点值的大小，然后循环迭代即可！

**代码**

```java
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {

        //使用伪头结点，最后返回node.next
        ListNode node = new ListNode(0);
        ListNode cur = node;

        while(list1 !=null && list2 != null){
            if(list1.val<=list2.val){
                cur.next=list1;
                list1=list1.next;
            }else{
                cur.next=list2;
                list2=list2.next;
            }
            cur=cur.next;
        }

        if(list1 != null) cur.next=list1;
        if(list2 != null) cur.next=list2;
        return node.next;

    }
}
```

## 26、树的子结构

输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构）

输入：

```
{8,8,#,9,#,2,#,5},{8,9,#,2}
```

输出：

```
true
```

**思路分析**

思路的话就是首先就是从B的根节点开始判断，如果A树中有与B树的根节点一样的节点，再循环遍历B树的左右节点是否跟该节点的左右节点相同即可。

**代码**

```java
class Solution {
    public boolean isSubStructure(TreeNode A, TreeNode B) {

        if(A == null || B == null) return false;
        return bfs(A,B) || isSubStructure(A.left,B) ||isSubStructure(A.right,B);
    }

    public boolean bfs(TreeNode A, TreeNode B){
        //B树匹配完成，返回true，如果B初始就为null则不会进入bfs
        if(B == null) return true;
        if(A == null || A.val != B.val) return false;
        return bfs(A.left, B.left) && bfs(A.right, B.right); 
    }
}
```

## 27、二叉树的镜像

**题目描述**

操作给定的二叉树，将其变换为源二叉树的镜像。 

比如：

```
源二叉树 
            8
           /  \
          6   10
         / \  / \
        5  7 9 11
        镜像二叉树
            8
           /  \
          10   6
         / \  / \
        11 9 7  5
```

**示例**

输入：

```
     4
   /   \
  2     7
 / \   / \
1   3 6   9
```

输出：

```
     4
   /   \
  7     2
 / \   / \
9   6 3   1
```

**思路分析**

思路一：使用递归判断，将每个节点的左右节点反转即可。

思路二：使用二叉树的广度优先遍历(BFS)，将该二叉树依据深度优先遍历的准则进行左右节点反转。

思路三：使用二叉树的深度优先遍历(DFS)，将该二叉树依据深度优先遍历的准则进行左右节点反转。

**代码**

思路一：

```java
class Solution {
    public TreeNode mirrorTree(TreeNode root) {
        if(root == null) return root;
        TreeNode temp = root.left;
        root.left = root.right;
        root.right = temp;

        mirrorTree(root.left);
        mirrorTree(root.right);
        return root;
    }
    
}
```

思路二：

```java
class Solution {
    public TreeNode mirrorTree(TreeNode root) {
        if(root == null) return root;

        Queue<TreeNode> queue = new LinkedList<>();

        queue.offer(root);
        while(!queue.isEmpty()){
            TreeNode node = queue.poll();

            TreeNode temp = node.right;
            node.right = node.left;
            node.left = temp;

            if(node.left != null) queue.offer(node.left);
            if(node.right != null) queue.offer(node.right);    
        }

        return root;
        
    }
}
```

思路三：

```java
class Solution {
    public TreeNode mirrorTree(TreeNode root) {
        if(root == null) return root;

        Stack<TreeNode> stack = new Stack<>();

        stack.push(root);
        while(!stack.isEmpty()){
            TreeNode node = stack.pop();

            TreeNode temp = node.right;
            node.right = node.left;
            node.left = temp;

            if(node.left != null) stack.push(node.left);
            if(node.right != null) stack.push(node.right);    
        }

        return root;
        
    }
}
```

## 28、对称的二叉树

请实现一个函数，用来判断一棵二叉树是不是对称的。如果一棵二叉树和它的镜像一样，那么它是对称的。

例如，二叉树 [1,2,2,3,4,4,3] 是对称的。

输入：

```
root = [1,2,2,3,4,4,3]
```

输出：

```
true
```

**思路分析**

思路的话跟上题类似，都是使用递归判断。但是注意，判断的应该是`left.left==right.right`和`right.left==left.right`。

**代码**

```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if(root == null) return true;
        return recur(root.left,root.right);
    }

    public boolean recur(TreeNode L, TreeNode R){

        if(L == null && R == null) return true;
        if(L == null || R == null || R.val != L.val) return false;
        //注意
        return recur(L.left,R.right) && recur(L.right,R.left);
    }
}
```

## 29、顺时针打印矩阵

**题目描述**

输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字。

**示例**

输入：

```
matrix = [[1,2,3],[4,5,6],[7,8,9]]
```

输出：

```
[1,2,3,6,9,8,7,4,5]
```

**思路分析**

按顺序输出，定义上下左右四个结点，找到顺时针打印矩阵的规律，当上边界大于下边界或左边界大于右边界时，停止循环。

**代码**

```java
class Solution {
    public int[] spiralOrder(int[][] matrix) {

        if(matrix.length == 0) return new int[0];

        int left = 0, right = matrix[0].length-1;
        int top=0, bottom = matrix.length-1;
        int[] res = new int[matrix.length*matrix[0].length];
        int count = 0;

        while(true){
            for(int i=left; i<=right; i++){
                res[count++] = matrix[top][i];
            }
            top++; 
            if(top>bottom) break;
            for(int i=top; i<=bottom; i++){
                res[count++] = matrix[i][right];  
            }
            right--;
            if(left>right) break;
            for(int i=right; i>=left; i--){
                res[count++] = matrix[bottom][i];  
            }
            bottom--;
            if(top>bottom) break;
            for(int i=bottom; i>=top; i--){
                res[count++] = matrix[i][left];   
            }
            left++;
            if(left>right) break;
        }
        return res;

    }
}
```

## 30、包含min函数的栈

**题目描述**

定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数（时间复杂度应为O（1））。

**示例：**

```
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.min();   --> 返回 -3.
minStack.pop();
minStack.top();      --> 返回 0.
minStack.min();   --> 返回 -2.
```

**思路分析**

参考了别人的思路，这里也是使用了两个栈。
一个用来存所有的元素`stackTotal`,另一个用来存加入新的元素后当前`stackTotal`中对应的最小值。
两个栈中的元素数量始终保持一致，当新的元素小于`stackLittle`栈顶元素时，`stackLittle`像栈顶push新来的元素，否则，`stackLittle`向栈顶加入原栈顶元素。
执行“pop”方法时，两个栈同时弹出各自的栈顶元素。

**代码**

```java
class MinStack {

    Stack<Integer> stack1, stack2;

    /** initialize your data structure here. */
    public MinStack() {
        stack1 = new Stack<>();
        stack2 = new Stack<>();
    }
    
    public void push(int x) {
        stack1.push(x);
        if(stack2.isEmpty() || x<=stack2.peek()){
            stack2.push(x);
        } 
    }
    
    public void pop() {
        int temp = stack1.pop();
        if(temp == stack2.peek()) stack2.pop();

    }
    
    public int top() {
        return stack1.peek();
    }
    
    public int min() {
        return stack2.peek();
    }
}
```

## 31、栈的压入、弹出序列

**题目描述**

输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如，序列 {1,2,3,4,5} 是某栈的压栈序列，序列 {4,5,3,2,1} 是该压栈序列对应的一个弹出序列，但 {4,3,5,1,2} 就不可能是该压栈序列的弹出序列。

**示例**

输入：

```
pushed = [1,2,3,4,5], popped = [4,5,3,2,1]
```

输出：

```
true
```

**思路分析**

这道题主要是使用模拟的思想，定义一个压入栈，将`pushA`中的元素一个个压入栈，每次将元素压入栈之后，循环判断栈顶元素是否与出栈列表的元素相等，如果相等则将对应元素出栈，出栈`count`加1，则循环判断。最后返回压入栈的`isEmpty()`即可。

**代码**

```java
class Solution {
    public boolean validateStackSequences(int[] pushed, int[] popped) {

        Stack<Integer> stack = new Stack<>();
        int count = 0;
        for(int i=0; i<pushed.length; i++){
            stack.push(pushed[i]);
            while(!stack.isEmpty() && stack.peek() == popped[count]){
                stack.pop();
                count++;
            }
        }
        return stack.isEmpty();

    }
}
```

## 32-1、从上往下打印二叉树

**题目描述**

从上往下打印出二叉树的每个节点，同层节点从左至右打印。

**示例**

输入：

```
{5,4,#,3,#,2,#,1}
```

输出：

```
[5,4,3,2,1]
```

**思路分析**

这道题主要运用的思想就是二叉树的广度优先遍历(BFS)，使用队列来实现。

**代码**

```java
class Solution {
    public int[] levelOrder(TreeNode root) {

        if(root == null) return new int[0];

        Queue<TreeNode> queue = new LinkedList<>();
        List<Integer> list = new ArrayList<>();

        queue.offer(root);
        while(!queue.isEmpty()){
            TreeNode node = queue.poll();
            list.add(node.val);
            if(node.left != null) queue.offer(node.left);
            if(node.right != null) queue.offer(node.right);

        }

        int[] res = new int[list.size()];
        for(int i=0; i<res.length; i++){
            res[i] = list.get(i);
        }

        return res;
    }
}
```

## 32-2、从上往下打印二叉树

**题目描述**

从上到下按层打印二叉树，同一层的节点按从左到右的顺序打印，每一层打印到一行。

**示例**

输入：

```
    3
   / \
  9  20
    /  \
   15   7
```

输出：

```
[
  [3],
  [9,20],
  [15,7]
]
```

**思路分析**

这道题实现思路跟上一题差不多，因为要实现一个按层输出，所有需要把每一层的元素单独放在一个List中，按每次队列里的元素添加。

**代码**

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {

        if(root == null) return new ArrayList(0);

        Queue<TreeNode> queue = new LinkedList<>();
        List<List<Integer>> res = new ArrayList<>();

        queue.offer(root);
        while(!queue.isEmpty()){
            List<Integer> temp = new ArrayList<>();
            int count = queue.size();  //队列长度会变
            for(int i=0; i<count; i++){
                TreeNode node = queue.poll();
                temp.add(node.val);
                if(node.left != null) queue.offer(node.left);
                if(node.right != null) queue.offer(node.right);
            }
            res.add(temp);
            

        }

        return res;
    }
}
```

## 32-3、从上往下打印二叉树

**题目描述**

请实现一个函数按照之字形顺序打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右到左的顺序打印，第三行再按照从左到右的顺序打印，其他行以此类推。

**示例**

输入：

```
    3
   / \
  9  20
    /  \
   15   7
```

输出：

```
[
  [3],
  [20,9],
  [15,7]
]
```

**思路分析**

这道题实现思路跟上一题差不多，有区别的就是需要在每层添加一个判断，偶数层倒序输出，奇数层顺序输出。

**代码**

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {

        if(root == null) return new ArrayList(0);

        Queue<TreeNode> queue = new LinkedList<>();
        List<List<Integer>> res = new ArrayList<>();

        queue.offer(root);
        int count=0;
        while(!queue.isEmpty()){
            LinkedList<Integer> temp = new LinkedList<>();
            int len = queue.size();
            for(int i=0; i<len; i++){
                TreeNode node = queue.poll();
                if(count%2==0) temp.addLast(node.val);
                else temp.addFirst(node.val);
                
                if(node.left != null) queue.offer(node.left);
                if(node.right != null) queue.offer(node.right);
            }
            res.add(temp);
            count++;
        }
        return res;
    }
}
```

## 33、二叉搜索树的后序遍历序列

**题目描述**

输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历结果。如果是则返回 `true`，否则返回 `false`。假设输入的数组的任意两个数字都互不相同。

**示例**

输入：

```
[1,6,3,2,5]
```

输出：

```
false
```

**思路分析**

这道题思路是使用递归和分治的思路，将后序遍历分为左子树和右子树的后序遍历。

**代码**

```java
class Solution {
    public boolean verifyPostorder(int[] postorder) {
        return recur(postorder, 0, postorder.length-1);
    }

    public boolean recur(int[] postorder, int i, int j){
        if(i>=j) return true;
        int m = i;
        while(postorder[m] < postorder[j]) m++;
        int p = m;
        while(postorder[m] > postorder[j]) m++;
        return m==j && recur(postorder,i,p-1) && recur(postorder,p,j-1);
    }


}
```

## 34、二叉树中和为某一值的路径

**题目描述**

输入一棵二叉树和一个整数，打印出二叉树中节点值的和为输入整数的所有路径。从树的根节点开始往下一直到叶节点所经过的节点形成一条路径。

**示例**

输入：

```
              5
             / \
            4   8
           /   / \
          11  13  4
         /  \    / \
        7    2  5   1

target=22
```

输出：

```
[
   [5,4,11,2],
   [5,8,4,5]
]
```

**思路分析**

这道题思路是回溯，定义一个path的列表，将路径上的元素都记录下来，如果路径和等于target则加入res，否则就回溯。

**代码**

```java
class Solution {
    LinkedList<List<Integer>> res = new LinkedList<>();
    LinkedList<Integer> path = new LinkedList<>();

    public List<List<Integer>> pathSum(TreeNode root, int target) {
        recur(root,target);
        return res;
    }

    public void recur(TreeNode root, int target){

        if(root == null) return;
        path.add(root.val);
        target -= root.val;
        if(target==0 && root.left == null && root.right == null){
            res.add(new LinkedList(path));
        }
        recur(root.left,target);
        recur(root.right,target);

        path.removeLast();
    }

}
```

## 35、复杂链表的复制

**题目描述**

请实现 copyRandomList 函数，复制一个复杂链表。在复杂链表中，每个节点除了有一个 next 指针指向下一个节点，还有一个 random 指针指向链表中的任意节点或者 null。

**示例**

输入：

```
head = [[7,null],[13,0],[11,4],[10,2],[1,0]]
```

输出：

```
[[7,null],[13,0],[11,4],[10,2],[1,0]]
```

**思路分析**

思路一：相当于简单链表的复制，该题难在random指针的指向，因为random指针指向任意节点或者null，因此在逐个结点复制的时候肯定不能做到通过给复制random指针，最简单的思路肯定就是先遍历一遍复制基本结点，再遍历一遍复制random结点，但这明显复杂度太高，因此我们使用hashmap的思路。

思路二：节省空间复杂度，通过拼接+拆分的思路，构建node1->node1_new->node2->node2_new的链表，这样就可以在找到原结点random指向的同时找到新结点的random指向。然后再使用双指针的思路拆分链表。

**代码**

思路一：

```java
class Solution {
    public Node copyRandomList(Node head) {

        if(head == null) return null;

        Node cur = head;
        Map<Node,Node> map = new HashMap<>();
        while(cur!=null){
            map.put(cur,new Node(cur.val));
            cur=cur.next;
        }

        cur = head;
        while(cur!=null){
            map.get(cur).next = map.get(cur.next);
            map.get(cur).random = map.get(cur.random);
            cur=cur.next;
        }
        return map.get(head);    
    }
}
```

思路二：

```java
class Solution {
    public Node copyRandomList(Node head) {

        if(head == null) return null;
        
        //构造拼接链表
        Node cur = head;
        while(cur!=null){
            Node tmp = new Node(cur.val);
            tmp.next = cur.next;
            cur.next = tmp;
            cur = tmp.next;

        }
        //构造random指针
        cur = head;
        while(cur!=null){
            if(cur.random != null) cur.next.random = cur.random.next;
            cur = cur.next.next;
        }

        //拆分链表
        Node res = head.next;
        Node pre = head; 
        cur = head.next;
        while(cur.next != null){
            pre.next = pre.next.next;
            cur.next = cur.next.next;
            pre = pre.next;
            cur = cur.next;
        }
        pre.next = null; // 单独处理原链表尾节点
        return res;      // 返回新链表头节点
          
    }
}
```

## 36、二叉搜索树和双向链表

**题目描述**

输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的循环双向链表。要求不能创建任何新的节点，只能调整树中节点指针的指向。

**思路分析**

二叉搜索树的中序遍历便是一个排好序的列表，我们只需要在遍历的过程中调整结点的左右节点即可。

**代码**

```java
class Solution {
    Node pre, head;
    public Node treeToDoublyList(Node root) {
        if(root == null) return null;
        dfs(root);
        //构造循环
        head.left = pre;
        pre.right = head;
        return head;
    }


    public void dfs(Node cur){
        if(cur == null) return;
        //左结点
        dfs(cur.left);
        //调整引用
        if(pre!=null) pre.right = cur;
        else head = cur;
        cur.left = pre;
        pre = cur;
        //右结点
        dfs(cur.right);

    }
}
```

## 37、序列化二叉树

**题目描述**

请实现两个函数，分别用来序列化和反序列化二叉树。

输入：

```
你可以将以下二叉树：

    1
   / \
  2   3
     / \
    4   5
```

输出：

```
序列化为 "[1,2,3,null,null,4,5]"
```

**思路分析**

该题的思路主要是层次遍历(BFS)，使用队列来完成。具体思路参考：https://leetcode-cn.com/problems/xu-lie-hua-er-cha-shu-lcof/solution/mian-shi-ti-37-xu-lie-hua-er-cha-shu-ceng-xu-bian-/

**代码**

```java

public class Codec {

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        if(root == null) return "[]";
        Queue<TreeNode> queue = new LinkedList<>();
        StringBuilder res = new StringBuilder("[");
        queue.offer(root);
        while(!queue.isEmpty()){
            TreeNode node = queue.poll();
            if(node != null){
                //添加node.val
                res.append(node.val+",");
                //左右结点入列
                queue.offer(node.left);
                queue.offer(node.right); 
            }else{
                res.append("null,");
            }
        }
        res.deleteCharAt(res.length()-1);
        res.append("]");
        return res.toString();
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        //空则返回
        if(data.equals("[]")) return null;
        //按,split
        String[] lst = data.substring(1,data.length()-1).split(",");
        //定义root结点
        TreeNode root = new TreeNode(Integer.parseInt(lst[0]));
        //定义队列
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        int count = 1;
        while(!queue.isEmpty()){
            TreeNode node = queue.poll();
            if(!lst[count].equals("null")){
                node.left = new TreeNode(Integer.parseInt(lst[count]));
                queue.offer(node.left);
            }
            count++;
            if(!lst[count].equals("null")){
                node.right = new TreeNode(Integer.parseInt(lst[count]));
                queue.offer(node.right);
            }
            count++;
        }
        return root;
    }
}
```

## 38、字符串的排列

**题目描述**

输入一个字符串，打印出该字符串中字符的所有排列。

你可以以任意顺序返回这个字符串数组，但里面不能有重复元素。

输入：

```
s = "abc"
```

输出：

```
["abc","acb","bac","bca","cab","cba"]
```

**思路分析**

该题的思路主要是回溯和剪枝，定义一个res返回结果，字符数组c保存每次添加的结果。主要运用的是字符串排列的知识，每次固定一位，如果遇到重复的字符则进行剪枝。具体思路参考https://leetcode-cn.com/problems/zi-fu-chuan-de-pai-lie-lcof/solution/mian-shi-ti-38-zi-fu-chuan-de-pai-lie-hui-su-fa-by/

**代码**

**代码**

```java
class Solution {

    List<String> res = new ArrayList<>();
    char[] c;

    public String[] permutation(String s) {
        //将s变成字符数组
        c = s.toCharArray();
        dfs(0);
        return res.toArray(new String[res.size()]);

    }

    void dfs(int x){
        if(x == c.length-1){
            res.add(String.valueOf(c));  //将c排列添加到res
            return;
        }
        Set<Character> set = new HashSet<>();
        for(int i=x; i<c.length; i++){
            if(set.contains(c[i])) continue;   //剪枝
            set.add(c[i]);
            swap(i,x);  //将c[i]固定在c的第x位
            dfs(x+1);
            swap(i,x);  //恢复交换
        }
    }

    void swap(int a, int b){
        char tmp = c[a];
        c[a] = c[b];
        c[b] = tmp;
    }
}
```

## 39、数组中出现次数超过一半的数字

**题目描述**

数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。

你可以假设数组是非空的，并且给定的数组总是存在多数元素。

输入：

```
[1, 2, 3, 2, 2, 2, 5, 4, 2]
```

输出：

```
2
```

**思路分析**

思路一：使用哈希表来存储计数，键为出现的数字，值为数字出现的次数。

思路二：使用排序，如果存储出现超过一半的数字，则排序后的数组中位数即为所求。该排序算法为插入排序

思路三：第二种思路是借鉴的摩尔投票法的思路。摩尔投票法的大概思路就是**每次从序列里选择两个不相同的数字删除掉（或称为“抵消”），最后剩下一个数字或几个相同的数字，就是出现次数大于总数一半的那个。**本题使用摩尔投票法的大概思路就是如果存在一个出现次数超过数组长度一半的众数，则使用摩尔投票法之后最后留下的一个或多个数字一定就是那个众数。但为了排除不存在众数的情况下，则最后需要验证一下是否为出现次数超过数组长度一半的那个众数。

**代码**

思路一：

```java
class Solution {
    public int majorityElement(int[] nums) {

        HashMap<Integer,Integer> map = new HashMap<>();
        for(int i=0; i<nums.length; i++){
            if(map.containsKey(nums[i])){
                map.put(nums[i],map.get(nums[i])+1);
            }else{
                map.put(nums[i],1);
            }
        }
        for(Integer key:map.keySet()){
            if(map.get(key)>nums.length/2){
                return key;
            } 
        }
        return 0;
    }
}
```

思路二：

```java
class Solution {
    public int majorityElement(int[] nums) {
        for(int i=0; i<nums.length; i++){
            int min=i;
            for(int j=i; j<nums.length; j++){
                if(nums[j] < nums[min]) min=j;
            }
            int tmp = nums[i];
            nums[i] = nums[min];
            nums[min] = tmp;
        }
        return nums[nums.length/2];
    }
}
```

思路三：

```java
class Solution {
    public int majorityElement(int[] nums) {

        int x=0, count=0;
        for(int i=0; i<nums.length; i++){
            if(count == 0) x = nums[i];
            if(nums[i] == x) count++;
            else count--;
        }
        return x;
    }
}
```

## 40、最小的K个数

**题目描述**

输入整数数组 arr ，找出其中最小的 k 个数。例如，输入4、5、1、6、2、7、3、8这8个数字，则最小的4个数字是1、2、3、4。

输入：

```
arr = [3,2,1], k = 2
```

输出：

```
[1,2] 或者 [2,1]
```

**思路分析**

思路一：使用快速排序，排序完之后选择前面的K个数

思路二：基于快速排序思想的哨兵划分，因为题目只需要返回最小的K个数，并没有要求顺序，因此我们可以采用快速排序的思想。即如果某次快速排序的基准恰好为第K个数，则第K个数的左边则是为比他小的数。

思路三：很简单的一个方法就是使用堆完成。定义一个大顶堆，我们先将数组中的前k个元素放入堆，然后当数组中的元素小于堆顶元素时，再继续放入，然后删除堆顶元素，这样结束后我们便可以数组中最小的k个数的大顶堆，然后遍历输出即可。

**代码**

```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {

        if(k == 0 || arr.length ==0) return new int[0];
        quickSort(arr,0,arr.length-1);
        return Arrays.copyOf(arr,k);
    }

    public void quickSort(int[] arr, int left, int right){
        if(left>right) return;

        int i=left, j=right;
        int pivot = left;  //以left为基准
        while(i<j){
            while(i<j && arr[pivot]<=arr[j]) j--;
            while(i<j && arr[pivot]>=arr[i]) i++;
            swap(arr,i,j);
        }
        swap(arr,i,pivot);

        quickSort(arr,left,i-1);
        quickSort(arr,i+1,right);
    }

    public void swap(int[] arr, int i, int j){
        int tmp = arr[i];
        arr[i] = arr[j];
        arr[j] = tmp;
    }
}
```

思路二：

```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        if(k>=arr.length) return arr;
        return quickSort(arr,k,0,arr.length-1);
    }

    public int[] quickSort(int[] arr, int k, int left, int right){

        int i=left, j=right;
        int pivot = left;  //以left为基准
        while(i<j){
            while(i<j && arr[pivot]<=arr[j]) j--;
            while(i<j && arr[pivot]>=arr[i]) i++;
            swap(arr,i,j);
        }
        swap(arr,i,pivot);

        if(i>k) return quickSort(arr,k,left,i-1);
        if(i<k) return quickSort(arr,k,i+1,right);
        return Arrays.copyOf(arr,k);  //i==k
    }

    public void swap(int[] arr, int i, int j){
        int tmp = arr[i];
        arr[i] = arr[j];
        arr[j] = tmp;
    }
}
```

思路三：

```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        if(k == 0 || arr.length == 0 ) return new int[0];

        //我们要构造一个大小为k的大顶堆，默认是小顶堆，需要重写一下比较器
        //数组元素必须比堆顶元素小才能入堆，当k满了之后入堆需要让堆顶元素出堆
        Queue<Integer> heap = new PriorityQueue<>(k,(v1, v2) -> v2 - v1);

        for(int e: arr){
            //堆满了之后，必须比堆顶元素小才能入堆
            if(heap.isEmpty() || heap.size() < k || e<heap.peek()){
                heap.offer(e);
            }
            //堆大小超过k，堆顶元素出堆
            if(heap.size()>k){
                heap.poll();
            }
        }

        int[] res = new int[k];
        int count=0;
        for(int e:heap){
            res[count++]=e;
        }

        return res;

    }

}
```

## 41、数据流中的中位数

**题目描述**

如何得到一个数据流中的中位数？如果从数据流中读出奇数个数值，那么中位数就是所有数值排序之后位于中间的数值。如果从数据流中读出偶数个数值，那么中位数就是所有数值排序之后中间两个数的平均值。

例如，

[2,3,4] 的中位数是 3

[2,3] 的中位数是 (2 + 3) / 2 = 2.5

设计一个支持以下两种操作的数据结构：

    void addNum(int num) - 从数据流中添加一个整数到数据结构中。
    double findMedian() - 返回目前所有元素的中位数

**示例**

输入：

```
["MedianFinder","addNum","addNum","findMedian","addNum","findMedian"]
[[],[1],[2],[],[3],[]]
```

输出：

```
[null,null,null,1.50000,null,2.00000]
```

**思路分析**

该题可以使用堆排序的思想，建立一个小顶堆(A)和大顶堆(B)，小顶堆保存数据流中较大的一半，大顶堆保存数据流中较小的一半，假设为数据流长度为奇数时，我们让中位数保存在小顶堆，而当数据长度为偶数时，中位数为(大顶堆堆顶元素+小顶堆堆顶元素)/2，而添加元素时，

**代码**

```java
class MedianFinder {
    Queue<Integer> A,B;
    public MedianFinder() {
        A = new PriorityQueue<>(); //小顶堆
        B = new PriorityQueue<>((x, y) -> (y - x));  //大顶堆
    }
    
    public void addNum(int num) {
        if(A.size() != B.size()){
            A.offer(num);
            B.offer(A.poll());
        }else{
            B.offer(num);
            A.offer(B.poll());
        }
    }
    
    public double findMedian() {
        if(A.size() != B.size()) return A.peek();
        else return (double)(A.peek()+B.peek())/2;
    }
}
```

## 42、连续子数组的最大和

**题目描述**

输入一个整型数组，数组中的一个或连续多个整数组成一个子数组。求所有子数组的和的最大值。

要求时间复杂度为O(n)。

**示例**

输入：

```
nums = [-2,1,-3,4,-1,2,1,-5,4]
```

输出：

```
6
```

**思路分析**

思路一：该题运用动态规划的思想，我们假设该数组全为正数，则最大和的求解过程就是第一个元素累加到最后一个元素，但如果其中包含负数怎么办呢？

我们再来回忆全为正数的累加过程，第一个元素和第二个元素相加，必定大于第二个元素，但如果第一个元素为负数，那结果就是第二个元素大，那为何不从第二个元素开始累加呢？

于是我们有了思路：如果以i为截断点，之前的array[i]累加小于单个的array[i]，那就说明前面元素累加相对于当前array[i]来说是负作用，应该舍弃，累加从array[i]开始，反之，则前面的累加为正作用，累加应从上一个前面累加开始，每次记录最大的max，遍历数组之后就可以得到连续子数组的最大和。

思路二：在思路一的基础上，压缩空间，不用定义dp数组，而是直接以nums作为dp数组

**代码**

思路一：

```java
class Solution {
    public int maxSubArray(int[] nums) {
        //动态规划数组
        int[] dp = new int[nums.length];
        //初始化
        dp[0] = nums[0];
        int max = dp[0];

        for(int i=0; i<nums.length-1; i++){
            if(dp[i]+nums[i+1]<nums[i+1]) dp[i+1] = nums[i+1];
            else dp[i+1] = dp[i]+nums[i+1];
            if(dp[i+1]>max) max=dp[i+1];
        }
        return max;
    }
}
```

思路二：

```java
class Solution {
    public int maxSubArray(int[] nums) {
        //初始化
        int max = nums[0];

        for(int i=0; i<nums.length-1; i++){
            if(nums[i]+nums[i+1]>nums[i+1]) nums[i+1] += nums[i];
            if(nums[i+1]>max) max=nums[i+1];
        }
        return max;
    }
}
```

## 43、数字序列中某一位的数字

**题目描述**

输入一个整数 n ，求1～n这n个整数的十进制表示中1出现的次数。

例如，输入12，1～12这些整数中包含1 的数字有1、10、11和12，1一共出现了5次。

**示例**

输入：

```
12
```

输出：

```
5
```

**思路分析**

本题的思路是通过数学找规律求解，具体分析：https://leetcode-cn.com/problems/1nzheng-shu-zhong-1chu-xian-de-ci-shu-lcof/solution/mian-shi-ti-43-1n-zheng-shu-zhong-1-chu-xian-de-2/

**代码**

```java
class Solution {
    public int countDigitOne(int n) {

        int digit=1, res=0;
        int high=n/10, cur=n%10, low=0;
        while(high!=0 || cur!=0){
            if(cur==0) res+=high * digit;
            else if(cur==1) res+= high * digit + low + 1;
            else res+= (high+1)*digit;
            //更新高低位
            low += cur*digit;  //下轮low为cur*digit
            cur = high%10;  //high最低位为下轮cur
            high /=10;  //high去除最低位为下轮high
            digit *=10;
        }

        return res;

    }
}
```

## 44、数字序列中某一位的数字

**题目描述**

数字以0123456789101112131415…的格式序列化到一个字符序列中。在这个序列中，第5位（从下标0开始计数）是5，第13位是1，第19位是4，等等。

请写一个函数，求任意第n位对应的数字。

**示例**

输入：

```
n = 3
```

输出：

```
3
```

**思路分析**

本题的思路就是找规律求解，详细思路分析：https://leetcode-cn.com/problems/shu-zi-xu-lie-zhong-mou-yi-wei-de-shu-zi-lcof/solution/mian-shi-ti-44-shu-zi-xu-lie-zhong-mou-yi-wei-de-6/

**代码**

```java
class Solution {
    public int findNthDigit(int n) {
        int digit = 1;
        long start = 1;
        long count = 9;
        //求数位
        while(n>count){
            n-=count;
            digit++;
            start *= 10;
            count = 9*digit*start;
        }

        //求数字，n-1是处理0
        long num = start + (n-1)/digit;
        //求结果
        return Long.toString(num).charAt((n-1)%digit) - '0';


    }
}
```

## 45、把数组排成最小的数

**题目描述**

输入一个非负整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。

**示例**

输入：

```
[10,2]
```

输出：

```
"102"
```

**思路分析**

思路一：该题本质是使用排序的思想，排序规则为：若拼接字符串 x+y>y+x，则 x “大于” y ；
反之，若 x+y<y+x，则 x “小于” y；因此我们重新定义排序的比较规则，使用快速排序。

思路二：与思路一样，只不过我们使用内置的排序算法来实现。

**代码**

思路一：

```java
class Solution {
    public String minNumber(int[] nums) {
        String[] str = new String[nums.length];
        for(int i=0; i<nums.length; i++){
            str[i] = String.valueOf(nums[i]);
        }
        quickSort(str,0,str.length-1);
        StringBuilder res = new StringBuilder();
        for(String i: str){
            res.append(i);
        }
        return res.toString();
    }

    public void quickSort(String[] str, int left, int right){
        if(left>right) return;
        
        int i=left,j=right;
        String tmp = str[i];
        while(i<j){
            while(i<j && (str[j]+str[left]).compareTo(str[left]+str[j])>=0) j--;
            while(i<j && (str[i]+str[left]).compareTo(str[left]+str[i])<=0) i++;
            tmp = str[i];
            str[i] = str[j];
            str[j] = tmp; 
        }

        //将str[i]设为str[left]
        str[i] = str[left];
        str[left] = tmp;

        quickSort(str, left, i-1);
        quickSort(str, i+1, right);
    }


}
```

思路二：

```java
class Solution {
    public String minNumber(int[] nums) {
        String[] str = new String[nums.length];
        for(int i=0; i<nums.length; i++){
            str[i] = String.valueOf(nums[i]);
        }
        Arrays.sort(str, (x,y)->((x+y).compareTo(y+x)));
        StringBuilder res = new StringBuilder();
        for(String i: str){
            res.append(i);
        }
        return res.toString();
    }


}
```

## 46、连续子数组的最大和

**题目描述**

给定一个数字，我们按照如下规则把它翻译为字符串：0 翻译成 “a” ，1 翻译成 “b”，……，11 翻译成 “l”，……，25 翻译成 “z”。一个数字可能有多个翻译。请编程实现一个函数，用来计算一个数字有多少种不同的翻译方法。

**示例**

输入：

```
12258
```

输出：

```
5
```

**思路分析**

思路一：该题使用动态规划，如果我们发现相邻两位数xixi-1如果大于10且小于25，则是可以被一起翻译的，此时dp[i]=dp[i-1]+dp[i-2]，否则，则dp[i]=dp[i-1]。这里把num变成字符串从左往右判断，当然也可以从右往左判断。

思路二：在思路一的基础上，不使用字符串而是通过取余和取整的操作得到num的每一位，再进行判断。这里只能从右往左判断。

**代码**

思路一：

```java
class Solution {
    public int translateNum(int num) {

        String str = String.valueOf(num);
        int a=1, b=1; //变量初始化，a为0个数字的翻译方法，b为1个数字的翻译方法
        for(int i=2; i<=str.length(); i++){
            String tmp = str.substring(i-2,i);
            int c = tmp.compareTo("10")>=0 && tmp.compareTo("25")<=0 ? a+b:b;
            a=b;
            b=c;
        }
        return b;
    }
}
```

思路二：

```java
class Solution {
    public int translateNum(int num) {
        //变量初始化，a为0个数字的翻译方法，b为1个数字的翻译方法,x为十位，y为个位
        int a=1, b=1, x, y=num%10;
        while(num!=0){
            num/=10;
            x=num%10;
            int c = (10*x+y) >=10 && (10*x+y)<=25 ? a+b:b;
            a=b;
            b=c;
            y=x; //进位
        }
        return b;
    }
}
```

## 47、礼物的最大价值

**题目描述**

在一个 m*n 的棋盘的每一格都放有一个礼物，每个礼物都有一定的价值（价值大于 0）。你可以从棋盘的左上角开始拿格子里的礼物，并每次向右或者向下移动一格、直到到达棋盘的右下角。给定一个棋盘及其上面的礼物的价值，请计算你最多能拿到多少价值的礼物？

**示例**

输入：

```
[
  [1,3,1],
  [1,5,1],
  [4,2,1]
]
```

输出：

```
12
```

**思路分析**

思路一：该题运用动态规划的思想，当i和j不为零时，`grid[i][j]`的最大值等于`grid[i-1][j-1]`+`Math.max(grid[i-1][j],grid[i][j-1])`，否则，`grid[i][j]`的最大值等于`grid[i-1][j]`或`grid[i][j-1]`

**代码**

```java
class Solution {
    public int maxValue(int[][] grid) {
        int m = grid.length, n=grid[0].length;
        for(int i=0; i<m; i++){
            for(int j=0; j<n; j++){
                if(i>0 && j>0) grid[i][j] += Math.max(grid[i-1][j],grid[i][j-1]);
                else if(i>0) grid[i][j] += grid[i-1][j];
                else if(j>0) grid[i][j] += grid[i][j-1];
                else continue;
            }
        }
        return grid[m-1][n-1];
    }
}
```

## 48、最长不含重复字符的子字符串

**题目描述**

请从字符串中找出一个最长的不包含重复字符的子字符串，计算该最长子字符串的长度。

输入：

```
 "abcabcbb"
```

输出：

```
3
```

**思路分析**

该题运用动态规划的思想，当i和j不为零时，`grid[i][j]`的最大值等于`grid[i-1][j-1]`+`Math.max(grid[i-1][j],grid[i][j-1])`，否则，`grid[i][j]`的最大值等于`grid[i-1][j]`或`grid[i][j-1]`

**代码**

```java
class Solution {
    public int maxValue(int[][] grid) {
        int m = grid.length, n=grid[0].length;
        for(int i=0; i<m; i++){
            for(int j=0; j<n; j++){
                if(i>0 && j>0) grid[i][j] += Math.max(grid[i-1][j],grid[i][j-1]);
                else if(i>0) grid[i][j] += grid[i-1][j];
                else if(j>0) grid[i][j] += grid[i][j-1];
                else continue;
            }
        }
        return grid[m-1][n-1];
    }
}
```

## 49、丑数

**题目描述**

我们把只包含质因子 2、3 和 5 的数称作丑数（Ugly Number）。求按从小到大的顺序的第 n 个丑数。

**示例**

输入：

```
10
```

输出：

```
12
```

**思路分析**

该题的思路还是动态规划，具体分析：https://leetcode-cn.com/problems/chou-shu-lcof/solution/mian-shi-ti-49-chou-shu-dong-tai-gui-hua-qing-xi-t/

**代码**

```java
class Solution {
    public int nthUglyNumber(int n) {  

        int[] dp = new int[n];
        int x2=0, x3=0, x5=0;
        dp[0]=1;
        for(int i=1; i<n; i++){
            int y2=2*dp[x2], y3=3*dp[x3], y5=5*dp[x5];
            dp[i] = Math.min(Math.min(y2,y3),y5);
            if(dp[i] == y2) x2++;
            if(dp[i] == y3) x3++;
            if(dp[i] == y5) x5++;
        }

        return dp[n-1];

    }
}
```

## 51、数组中的逆序对

**题目描述**

在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组，求出这个数组中的逆序对的总数。

输入：

```
[7,5,6,4]
```

输出：

```
5
```

**思路分析**

本题最开始想到的就是遍历求解，但是发现这种方法很容易就超出了时间限制。然后在网上寻找题解，这道题很经典的解法就是归并排序。归并排序的思路就是将原来无序的数组一分为二成两个子数组，直到分成的数组长度为1，然后再将相邻的两个子数组合并，可以发现，我们将数组分开之后，当前面的子数组中的首元素大于后面子数组的首元素，则逆序数的结果自加mid+1-left。

**代码**

```java
class Solution {
    int[] nums, tmp;
    public int reversePairs(int[] nums) {
        this.nums=nums;
        this.tmp = new int[nums.length];
        return mergeSort(0,nums.length-1);
    }

    public int mergeSort(int left, int right){
        //终止条件
        if(left>=right) return 0;
        //分离阶段
        int m = (left+right)/2;
        int res = mergeSort(left,m) + mergeSort(m+1,right); 

        //合并阶段
        int i=left, j=m+1;
        //复制nums[left:right]到tmp
        for(int k=left; k<=right; k++)
            tmp[k] = nums[k];
        for(int k=left; k<=right; k++){
            //判断左边界是否越界
            if(i == m+1){
                nums[k] = tmp[j];
                j++;
            }
            //判断右边界是否越界
            else if(j == right+1){
                nums[k] = tmp[i];
                i++;
            }
            else if(tmp[i] <= tmp[j]){
                nums[k] = tmp[i];
                i++;
            }
            else{
                nums[k] = tmp[j];
                j++;
                res += m+1-i;
            }
        }
        return res;
    }
}
```

## 52、两个链表的第一个公共节点

**题目描述**

输入两个链表，找出它们的第一个公共节点。

输入：

```
intersectVal = 8, listA = [4,1,8,4,5], listB = [5,0,1,8,4,5], skipA = 2, skipB = 3
```

输出：

```
8
```

**思路分析**

本题使用双指针解决。设链表A的长度为a，链表B的长度为b，公共子链表的长度为c。双指针A和B的初始指向A链表和B链表的头结点，我们来规定双指针的移动路径，当某个指针移到当该链表的尾端，则指向另外一个链表的头部，比如：A移动到了A链表的尾部，则A下一个的值为headB。我们来计算当两个指针移动到第一个公共结点时所走的长度，A为a+b-c，B为b+a-c，即两个指针走的长度是一样的！因此，我们可以让两个指针同时出发，当两个指针重叠时，则说明该指针指向的是第一个公共结点。

**代码**

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {

        ListNode A = headA, B=headB;
        while(A != B){
            A = A == null? headB:A.next;
            B = B == null? headA:B.next;
        }
        return A;
        
    }
}
```

## 53-1、在排序数组中查找数据i

**题目描述**

统计一个数字在排序数组中出现的次数。

输入：

```
nums = [5,7,7,8,8,10], target = 8
```

输出：

```
2
```

**思路分析**

本题的思路是使用二分查找，使用两次二分查找分别找出target的左边界和右边界，然后结果为right-left-1

**代码**

```java
class Solution {
    public int search(int[] nums, int target) {

        int l=0, r=nums.length-1;
        //寻找右边界
        while(l<=r){
            int m = (l+r)/2;
            if(nums[m] > target) r=m-1;
            else l=m+1;
        }
        int right = l;
        //无target 提前返回
        if(r >= 0 && nums[r] != target) return 0;
        //搜索左边界
        l=0; 
        r=nums.length-1;
        while(l<=r){
            int m = (l+r)/2;
            if(nums[m] < target) l=m+1;
            else r=m-1;
        }
        int left = r;
        return right-left-1;
    }
}
```

## 53-2、0-n-1中缺失的数字

**题目描述**

一个长度为n-1的递增排序数组中的所有数字都是唯一的，并且每个数字都在范围0～n-1之内。在范围0～n-1内的n个数字中有且只有一个数字不在该数组中，请找出这个数字。

输入：

```
[0,1,2,3,4,5,6,7,9]
```

输出：

```
8
```

**思路分析**

本题的思路是使用二分查找，当nums[m]==m时，左边界更新为m+1，否则则右边界更新为m-1。

**代码**

```java
class Solution {
    public int missingNumber(int[] nums) {

        int i=0, j=nums.length-1;
        while(i<=j){
            int m=(i+j)/2;
            if(nums[m] == m) i=m+1;
            else  j=m-1;
        }
        return i;


    }
}
```

## 54、二叉搜索树的第k大的节点

**题目描述**

给定一棵二叉搜索树，请找出其中第k大的节点。

输入：

```
输入: root = [3,1,4,null,2], k = 1
   3
  / \
 1   4
  \
   2
```

输出：

```
4
```

**思路分析**

本题的思路使用倒序的中序遍历，二叉搜索树的中序遍历输出从小到大的结点序列，而倒序则输出从大到小的结点需要，因此我们只需要在输出的时候计数，当计数等于k时便完成。

**代码**

```java
class Solution {
    int k,res;
    public int kthLargest(TreeNode root, int k) {
        this.k=k;
        dfs(root);
        return res;
    }

    void dfs(TreeNode root){
        if(root.right != null) dfs(root.right);
        k--;
        if(k == 0) res=root.val;
        if(root.left != null) dfs(root.left);
    }
}
```

## 55-1、二叉树的深度

**题目描述**

输入一棵二叉树的根节点，求该树的深度。从根节点到叶节点依次经过的节点（含根、叶节点）形成树的一条路径，最长路径的长度为树的深度。

输入：

```
[3,9,20,null,null,15,7]
    3
   / \
  9  20
    /  \
   15   7
```

输出：

```
3
```

**思路分析**

思路一：深度优先遍历，省时间

思路二：广度优先遍历，省空间

**代码**

思路一：

```java
class Solution {
    public int maxDepth(TreeNode root) {
        if(root == null) return 0;
        return Math.max(maxDepth(root.left),maxDepth(root.right))+1;

    }
}
```

思路二：

```java
class Solution {
    public int maxDepth(TreeNode root) {
        if(root == null) return 0;
        
        int res=0;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        while(!queue.isEmpty()){
            int length = queue.size();
            for(int i=0; i<length; i++){
                TreeNode node = queue.poll();
                if(node.left != null) queue.offer(node.left);
                if(node.right != null) queue.offer(node.right);
            }
            res++;

        }
        return res;


    }
}
```

## 55-2、平衡二叉树

**题目描述**

输入一棵二叉树的根节点，判断该树是不是平衡二叉树。如果某二叉树中任意节点的左右子树的深度相差不超过1，那么它就是一棵平衡二叉树。

输入：

```
    3
   / \
  9  20
    /  \
   15   7
```

输出：

```
true
```

**思路分析**

思路是后序遍历+剪枝，我们知道一棵树的深度等于左子树的深度和右子树的深度的最大值+1，因此我们想要判断一棵树是否为平衡二叉树，我们只需要判断递归判断左子树和右子树的深度差。首先考虑自底而上的判断，我们需要构建深度优先遍历，如果底部一棵子树为平衡二叉树，则返回其的深度，否则，返回-1；如果子树返回的是-1，则提前剪枝。

**代码**

```java
class Solution {
    public boolean isBalanced(TreeNode root) {
        return dfs(root) != -1;
    }

    public int dfs(TreeNode root){
        //root为null，返回0
        if(root == null) return 0;
        //计算出左子树的深度
        int left = dfs(root.left);
        if(left==-1) return -1;  //剪枝
        //计算出右子树的深度
        int right = dfs(root.right);
        if(right==-1) return -1;  //剪枝

        return Math.abs(right-left)<2?Math.max(right,left)+1:-1;
    }
}
```

## 56-1、数组中数字出现的次数

**题目描述**

一个整型数组 `nums` 里除两个数字之外，其他数字都出现了两次。请写程序找出这两个只出现一次的数字。要求时间复杂度是O(n)，空间复杂度是O(1)。

输入：

```
nums = [4,1,4,6]
```

输出：

```
[1,6] 或 [6,1]
```

**思路分析**

思路就是位运算中的异或运算，当两个数字相同时异或为结果为0。因此题目如果是要返回一位数，则可以用异或直接得到答案，但是题目需要返回两位数。

本题难点：需要通过异或后的结果找到x和y。我们可以通过异或后的结果上的1来找出x和y中二进制不同的位数，然后再构造子数组即可。

**代码**

```java
class Solution {
    public int[] singleNumbers(int[] nums) {

        int x=0, y=0, n=0,m=1;

        for(int num:nums){
            n^=num;
        }
        while((m&n)==0){
            m<<=1;
        }
        for(int num:nums){
            if((num & m) != 0) x ^= num;
            else y ^= num;    
        }
        return new int[]{x,y};

    }
}
```

## 56-2、数组中数字出现的次数

**题目描述**

在一个数组 `nums` 中除一个数字只出现一次之外，其他数字都出现了三次。请找出那个只出现一次的数字。

输入：

```
nums = [3,4,3,3]
```

输出：

```
4
```

**思路分析**

思路就是位运算+取余，我们将所有数字的二进制相加起来，然后对3取余，就可以得到出现了一次的数字

**代码**

```java
class Solution {
    public int singleNumber(int[] nums) {

        int[] counts = new int[32];
        for(int num:nums){
            for(int i=0; i<counts.length; i++){
                counts[i] +=num&1;
                num>>>=1;
            }
        }

        int res=0, m=3;
        for(int i=0; i<32; i++){
            res<<=1;
            res |= counts[31-i]%3;
        }
        
        return res;

    }
}
```

## 57-1、和为s的两个数字

**题目描述**

输入一个递增排序的数组和一个数字s，在数组中查找两个数，使得它们的和正好是s。如果有多对数字的和等于s，则输出任意一对即可。

输入：

```
nums = [2,7,11,15], target = 9
```

输出：

```
[2,7]
```

**思路分析**

思路一：最开始的思路就是使用hashmap来存储每次计算的结果，以空间换时间。

思路二：思路一考虑的是数组无序，此时使用思路一是较快的，但是如果是有序的，则使用双指针更快。

**代码**

思路一：

```java
class Solution {
    public int singleNumber(int[] nums) {

        int[] counts = new int[32];
        for(int num:nums){
            for(int i=0; i<counts.length; i++){
                counts[i] +=num&1;
                num>>>=1;
            }
        }

        int res=0, m=3;
        for(int i=0; i<32; i++){
            res<<=1;
            res |= counts[31-i]%3;
        }
        
        return res;

    }
}
```

思路二：

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {

        int front=0, back=nums.length-1;
        while(front<=back){
            if(nums[front]+nums[back]>target) back--;
            else if(nums[front]+nums[back]<target) front++;
            else return new int[]{nums[front],nums[back]}; 
        }

        return new int[2];

    }
}
```

## 57-2、和为s的两个数字

**题目描述**

输入一个正整数 target ，输出所有和为 target 的连续正整数序列（至少含有两个数）。

序列内的数字由小到大排列，不同序列按照首个数字从小到大排列。

输入：

```
target = 9
```

输出：

```
[[2,3,4],[4,5]]
```

**思路分析**

该题的思路可以使用滑动窗口求解，指定双指针为1和2，sum为3，当sum小于target时，右指针向右移，sum大于target时，左指针向右移，当target等于sum时，记录下目前的数组。

**代码**

```java
class Solution {
    public int[][] findContinuousSequence(int target) {

        int left=1, right=2,sum=3;
        List<int[]> res = new ArrayList<>();
        while(left<right){
            if(target==sum){
                int[] path = new int[right-left+1];
                for(int i=left; i<=right; i++) path[i-left]=i;
                res.add(path);
            }
            if(target >= sum){
                right++;
                sum+=right;
            }
            else{
                sum-=left;
                left++;
            }
        }

        return res.toArray(new int[0][]);

    }
}
```

## 58-1、翻转单词顺序

**题目描述**

输入一个英文句子，翻转句子中单词的顺序，但单词内字符的顺序不变。为简单起见，标点符号和普通字母一样处理。例如输入字符串"I am a student. "，则输出"student. a am I"。

输入：

```
"the sky is blue"
```

输出：

```
"blue is sky the"
```

**思路分析**

思路一：双指针，两个指针指向一个单词的头索引和尾索引，然后分别将每个单词加入一个`StringBuilder`返回其String即可。

思路二：利用java的内置函数

**代码**

思路一：

```java
class Solution {
    public String reverseWords(String s) {

        s=s.trim();
        StringBuilder res = new StringBuilder();
        int j=s.length()-1, i=j;
        while(i>=0){
            while(i>=0 && s.charAt(i) != ' ') i--;
            res.append(s.substring(i+1,j+1)+" ");
            while(i>=0 && s.charAt(i) ==' ') i--;
            j=i;
        }
        return res.toString().trim();
    }
}
```

思路二：

```java
class Solution {
    public String reverseWords(String s) {
        String[] lst = s.trim().split(" ");
        StringBuilder res = new StringBuilder();
        for(int i=lst.length-1; i>=0; i--){
            if(lst[i].equals("")) continue;
            res.append(lst[i]+" ");
        }

        return res.toString().trim();
    }
}
```

## 58-2、左旋转字符串

**题目描述**

字符串的左旋转操作是把字符串前面的若干个字符转移到字符串的尾部。请定义一个函数实现字符串左旋转操作的功能。比如，输入字符串"abcdefg"和数字2，该函数将返回左旋转两位得到的结果"cdefgab"。

输入：

```
s = "abcdefg", k = 2
```

输出：

```
"cdefgab"
```

**思路分析**

思路一：字符串切片

思路二：数组拼接

**代码**

思路一：

```java
class Solution {
    public String reverseLeftWords(String s, int n) {
        return s.substring(n,s.length())+s.substring(0,n);
    }
}
```

思路二：

```java
class Solution {
    public String reverseLeftWords(String s, int n) {
        
        StringBuilder res = new StringBuilder();
        for(int i=n; i<s.length(); i++){
            res.append(s.charAt(i));
        }
        for(int i=0; i<n; i++){
            res.append(s.charAt(i));
        }

        return res.toString();

    }
}
```

## 59-1、滑动窗口的最大值

**题目描述**

给定一个数组 `nums` 和滑动窗口的大小 `k`，请找出所有滑动窗口里的最大值。

输入：

```
nums = [1,3,-1,-3,5,3,6,7], 和 k = 3
```

输出：

```
[3,3,5,5,6,7] 
```

**思路分析**

思路一：回忆第30题我们曾经做过包含min的栈，即用两个栈实现能得到栈里面最小元素，且时间复制度为O(1)。本题就用这个思路，因为那一题删除的是栈顶元素，而我们本题需要删除的是栈底元素，因此我们本题考虑用队列实现。

**代码**

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {

        //单调队列
        //下面是要注意的点：
        //队列按从大到小放入
        //如果首位值（即最大值）不在窗口区间，删除首位
        //如果新增的值小于队列尾部值，加到队列尾部
        //如果新增值大于队列尾部值，删除队列中比新增值小的值，如果在把新增值加入到队列中
        //如果新增值大于队列中所有值，删除所有，然后把新增值放到队列首位，保证队列一直是从大到小
        if(nums.length==0) return new int[0];

        Deque<Integer> deque = new LinkedList<>();
        int[] res = new int[nums.length-k+1];
        //未形成窗口区间
        for(int i=0; i<k; i++){
            //队列不为空时，当前值与队列尾部值比较，如果大于，删除队列尾部值
            //一直循环删除到队列中的值都大于当前值，或者删到队列为空
            while(!deque.isEmpty() && deque.peekLast() <nums[i]) deque.removeLast();
            //执行完上面的循环后，队列中要么为空，要么值都比当前值大，然后就把当前值添加到队列中
            deque.addLast(nums[i]);
        }
        res[0] = deque.peekFirst();
        //窗口区间形成
        for(int i=k; i<nums.length; i++){
            //i-k是已经在区间外了，如果首位等于nums[i-k]，那么说明此时首位值已经不再区间内了，需要删除
            if(deque.peekFirst() == nums[i-k]) deque.removeFirst();
            while(!deque.isEmpty() && deque.peekLast() <nums[i]) deque.removeLast();
            deque.addLast(nums[i]);
            res[i-k+1] = deque.peekFirst();
        }

        return res;
    }
}
```

## 59-2、队列的最大值

请定义一个队列并实现函数 max_value 得到队列里的最大值，要求函数max_value、push_back 和 pop_front 的均摊时间复杂度都是O(1)。

若队列为空，pop_front 和 max_value 需要返回 -1

输入：

```
["MaxQueue","push_back","push_back","max_value","pop_front","max_value"]
[[],[1],[2],[],[],[]]
```

输出：

```
[null,null,null,2,1,2]
```

**思路分析**

本题的思路与前面的包含最小值的栈类似，需要构建一个对首为原队列最大值的递减队列，该队列为双端队列。

**代码**

```java
class MaxQueue {

    Queue<Integer> queue;
    Deque<Integer> deque;

    public MaxQueue() {
        queue = new LinkedList<>();
        deque = new LinkedList<>();
    }
    
    public int max_value() {
        return deque.isEmpty() ? -1:deque.peekFirst();
    }
    
    public void push_back(int value) {
        queue.offer(value);
        while(!deque.isEmpty() && deque.peekLast() <value){
            deque.pollLast();
        }
        deque.offerLast(value);

    }
    
    public int pop_front() {
        if(queue.isEmpty()) return -1;
        if(queue.peek().equals(deque.peekFirst())){
            deque.pollFirst();
        }
        return queue.poll(); 
    }
}
```

## 60、n个骰子的点数

把n个骰子扔在地上，所有骰子朝上一面的点数之和为s。输入n，打印出s的所有可能的值出现的概率。

你需要用一个浮点数数组返回答案，其中第 i 个元素代表这 n 个骰子所能掷出的点数集合中第 i 小的那个的概率。

输入：

```
1
```

输出：

```
[0.16667,0.16667,0.16667,0.16667,0.16667,0.16667]
```

**思路分析**

本题的思路为动态规划，假设已知n-1个骰子的解为f(n-1)，此时如果增加一个骰子，则这n个骰子的点数和为x的概率为f(n,x)，可知f(n,x)=i=1∑6f(n−1,x−i)/6。具体思路见https://leetcode-cn.com/problems/nge-tou-zi-de-dian-shu-lcof/solution/jian-zhi-offer-60-n-ge-tou-zi-de-dian-sh-z36d/

**代码**

```java
class Solution {
    public double[] dicesProbability(int n) {
        double[] dp = new double[6];

        for(int i=0; i<6; i++){
            dp[i]= 1.0 / 6.0;
        }

        for(int i=2; i<=n; i++){
            double[] tmp = new double[5*i+1];
            for(int j=0; j<dp.length;j++){
                for(int k=0; k<6; k++){
                    tmp[j+k] += dp[j]/6.0;
                }
            }
            dp=tmp;
        }

        return dp;

    }
}
```

## 61、扑克牌中的顺子

从扑克牌中随机抽5张牌，判断是不是一个顺子，即这5张牌是不是连续的。2～10为数字本身，A为1，J为11，Q为12，K为13，而大、小王为 0 ，可以看成任意数字。A 不能视为 14。

输入：

```
[1,2,3,4,5]
```

输出：

```
True
```

**思路分析**

本题的核心思路为判断重复+判断差值，如果不存在大小王(0)，则组成顺子的条件为无重复和max-min<5，再来考虑王的情况

**代码**

```java
class Solution {
    public boolean isStraight(int[] nums) {

        Set repeat = new HashSet<Integer>();
        int min = 14, max=0;
        for(int num : nums){
            if(num==0) continue;
            min = Math.min(min,num);
            max = Math.max(max,num);
            if(repeat.contains(num)) return false;
            repeat.add(num);
        }
        return max-min<5;

    }
}
```

## 62、圆圈中剩下的数字

0,1,···,n-1这n个数字排成一个圆圈，从数字0开始，每次从这个圆圈里删除第m个数字（删除后从下一个数字开始计数）。求出这个圆圈里剩下的最后一个数字。

例如，0、1、2、3、4这5个数字组成一个圆圈，从数字0开始每次删除第3个数字，则删除的前4个数字依次是2、0、4、1，因此最后剩下的数字是3。

输入：

```
n = 5, m = 3
```

输出：

```
3
```

**思路分析**

思路题解：https://leetcode-cn.com/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/solution/si-bu-he-xin-gong-shi-qing-song-nong-don-3vln/

**代码**

```java
class Solution {
    public int lastRemaining(int n, int m) {

        int dp=0;
        for (int i = 2; i <= n; i++) {
            dp = (dp + m) % i;
        }
        return dp;
    }
}
```

## 63、股票的最大利润

假设把某股票的价格按照时间先后顺序存储在数组中，请问买卖该股票一次可能获得的最大利润是多少？

输入：

```
[7,1,5,3,6,4]
```

输出：

```
5
```

**思路分析**

思路题解：https://leetcode-cn.com/problems/gu-piao-de-zui-da-li-run-lcof/solution/mian-shi-ti-63-gu-piao-de-zui-da-li-run-dong-tai-2/

**代码**

```java
class Solution {
    public int maxProfit(int[] prices) {

        if(prices.length==0) return 0;

        int profit=0, min=prices[0];
        for(int i=1; i<prices.length; i++){
            profit=Math.max(profit,prices[i]-min);
            min=Math.min(prices[i],min);
        }

        return profit;

    }
}
```

## 64、求1+2+...+n

求 `1+2+...+n` ，要求不能使用乘除法、for、while、if、else、switch、case等关键字及条件判断语句（A?B:C）。

输入：

```
n=3
```

输出：

```
6
```

**思路分析**

思路题解：https://leetcode-cn.com/problems/qiu-12n-lcof/solution/mian-shi-ti-64-qiu-1-2-nluo-ji-fu-duan-lu-qing-xi-/

**代码**

```java
class Solution {
    public int sumNums(int n) {
        boolean x = n > 1 && (n += sumNums(n - 1)) > 0;
        return n;
    }
}
```

## 65、不用加减乘除做加法

写一个函数，求两个整数之和，要求在函数体内不得使用 “+”、“-”、“*”、“/” 四则运算符号。

输入：

```
a = 1, b = 1

```

输出：

```
2
```

**思路分析**

思路题解：https://blog.csdn.net/wu_kung/article/details/22380603

**代码**

```java
class Solution {
    public int add(int a, int b) {
        while(b!=0){
            int c=(a&b)<<1;
            a ^= b;
            b = c;
        }
        return a;
    }
}
```

## 66、构建乘积数组

给定一个数组 A[0,1,…,n-1]，请构建一个数组 B[0,1,…,n-1]，其中 B[i] 的值是数组 A 中除了下标 i 以外的元素的积, 即 B[i]=A[0]×A[1]×…×A[i-1]×A[i+1]×…×A[n-1]。不能使用除法。

输入：

```
[1,2,3,4,5]
```

输出：

```
[120,60,40,30,24]
```

**思路分析**

本题的难点在于不能使用除法，如果可以使用除法可以先得一个全部的值，然后倒着相除即可。不能使用除法则得使用一点点小技巧。我们可以使用上三角和下三角矩阵，配合两次循环来求解。初始化令B[0]=1；然后B[n]=B[n-1]A[n-1]得到第一次循环的结果。定义tmp变量，让其自乘a[n-1]的结果，然后令B[n]=B[n+1]tmp。

**代码**

```java
class Solution {
    public int[] constructArr(int[] a) {

        if(a.length == 0) return new int[0];
        int[] B = new int[a.length];

        B[0] = 1;
        int tmp = 1;

        for(int i=1; i<a.length; i++){
            B[i] = B[i-1]*a[i-1];
        }
        for(int i=a.length-2; i>=0; i--){
            tmp *= a[i+1];
            B[i] *= tmp;
        }

        return B;
    }
}
```

## 67、把字符串转换成整数

写一个函数 StrToInt，实现把字符串转换成整数这个功能。不能使用 atoi 或者其他类似的库函数。

首先，该函数会根据需要丢弃无用的开头空格字符，直到寻找到第一个非空格的字符为止。

当我们寻找到的第一个非空字符为正或者负号时，则将该符号与之后面尽可能多的连续数字组合起来，作为该整数的正负号；假如第一个非空字符是数字，则直接将其与之后连续的数字字符组合起来，形成整数。

该字符串除了有效的整数部分之后也可能会存在多余的字符，这些字符可以被忽略，它们对于函数不应该造成影响。

注意：假如该字符串中的第一个非空格字符不是一个有效整数字符、字符串为空或字符串仅包含空白字符时，则你的函数不需要进行转换。

在任何情况下，若函数不能进行有效的转换时，请返回 0。

说明：

假设我们的环境只能存储 32 位大小的有符号整数，那么其数值范围为 [−231,  231 − 1]。如果数值超过这个范围，请返回  INT_MAX (231 − 1) 或 INT_MIN (−231) 。

输入：

```
"4193 with words"
```

输出：

```
4193
```

**思路分析**

思路分析：https://leetcode-cn.com/problems/ba-zi-fu-chuan-zhuan-huan-cheng-zheng-shu-lcof/solution/mian-shi-ti-67-ba-zi-fu-chuan-zhuan-huan-cheng-z-4/

**代码**

```java
class Solution {
    public int strToInt(String str) {
        char[] c = str.trim().toCharArray();
        if(c.length == 0) return 0;
        int res = 0, bndry = Integer.MAX_VALUE / 10;
        int i = 1, sign = 1;
        if(c[0] == '-') sign = -1;
        else if(c[0] != '+') i = 0;
        for(int j = i; j < c.length; j++) {
            if(c[j] < '0' || c[j] > '9') break;
            if(res > bndry || res == bndry && c[j] > '7') return sign == 1 ? Integer.MAX_VALUE : Integer.MIN_VALUE;
            res = res * 10 + (c[j] - '0');
        }
        return sign * res;

    }
}
```

## 68-1、二叉搜索树的最近公共祖先

给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

例如，给定如下二叉搜索树:  root = [6,2,8,0,4,7,9,null,null,3,5]

输入：

```
root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 8
```

输出：

```
6
```

**思路分析**

循环判断即可

**代码**

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {

        while(root!=null){
            if(p.val<root.val && q.val<root.val){
                root=root.left;
            }else if(p.val>root.val && q.val>root.val){
                root=root.right;
            }else break;
        }

        return root;
        
    }
}
```

## 68-2、二叉树的最近公共祖先

给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

例如，给定如下二叉搜索树:  root = [6,2,8,0,4,7,9,null,null,3,5]

输入：

```
root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
```

输出：

```
3
```

**思路分析**

同样也是递归判断

**代码**

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if(root == null || root == p || root == q) return root;
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        if(left == null) return right;
        if(right == null) return left;
        return root;
        
    }
}
```





























































































































