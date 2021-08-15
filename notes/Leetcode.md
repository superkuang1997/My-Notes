规划：

- leetcode 出现频率top200
- 剑指offer all
- leetcode 精选TOP面试题 出现频率top100
- 专题



# 数组





# 链表

## 快慢指针

链表长度为奇数，fast指向链表中的最后一个节点

链表长度为偶数，fast指向null

```java
ListNode slow = head, fast = head;
// 快慢指针
while (fast != null && fast.next != null) {
    slow = slow.next;
    fast = fast.next.next;
}
```



## 递归框架

```java
void traverse(ListNode head) {
    if (head == null) return;
    // 前序遍历代码
    traverse(head.next);
    // 后序遍历代码
}
```



## offer25

```java
// 迭代
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    ListNode head = new ListNode(0);
    ListNode r = head;
    while (l1 != null && l2 != null) {
        if (l1.val < l2.val) {
            r.next = l1;
            r = r.next;
            l1 = l1.next;
        } else {
            r.next = l2;
            r = r.next;
            l2 = l2.next;
        }
    }
    if (l1 != null) {
        r.next = l1;
    }
    if (l2 != null) {
        r.next = l2;
    }
    return head.next;        
}
```



```java
// 递归
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    if (l1 == null) {
        return l2;
    }
    if (l2 == null) {
        return l1;
    }
    if (l1.val < l2.val) {
        l1.next = mergeTwoLists(l1.next, l2);
        return l1;
    } else {
        l2.next = mergeTwoLists(l1, l2.next);
        return l2;
    }
}
```



## offer52

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode curA = headA;
        ListNode curB = headB;
        
        while (curA != curB) {
            curA = curA != null ? curA.next : headB;
            curB = curB != null ? curB.next : headA;
        }
        return curA;
    }
}
```



# DFS

## offer12

```java
class Solution {
    public boolean exist(char[][] board, String word) {
        char[] words = word.toCharArray();
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                if (dfs(board, words, i, j, 0)) return true;
            }
        }
        return false;
    }

    boolean dfs(char[][] board, char[] words, int i, int j, int k) {
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length || board[i][j] != words[k]) {
            return false;
        }
        if (k == words.length - 1) return true;
        char temp = board[i][j];
        board[i][j] = '/';
        boolean res = dfs(board, words, i + 1, j, k + 1) || dfs(board, words, i - 1, j, k + 1) ||
                dfs(board, words, i, j + 1, k + 1) || dfs(board, words, i, j - 1, k + 1);
        board[i][j] = temp;
        return res;
    }
}
```







## offer13

```java
class Solution {
    public int movingCount(int m, int n, int k) {
        boolean[][] visited = new boolean[m][n];
        return dfs(0, 0, m, n, k, visited);

    }

    private int dfs(int i, int j, int m, int n, int k, boolean[][] visited) {
        if (i < 0 || i >= m || j < 0 || j >= n || visited[i][j] ||
                i / 10 + i % 10 + j / 10 + j % 10 > k) {
            return 0;
        }
        visited[i][j] = true; // 表示已访问过
        return dfs(i + 1, j, m, n, k, visited) + dfs(i, j + 1, m, n, k, visited) + 1;
    }
}
```







## offer27

```java
class Solution {
    public TreeNode mirrorTree(TreeNode root) {
        TreeNode res = null;
        if (root != null) {
            res = new TreeNode(root.val);
            res.left = mirrorTree(root.right);
            res.right = mirrorTree(root.left);
        }
        return res;
    }
}
```







## offer28

问题：判断一个树是否对称

对称二叉树：

- `L.val = R.val` ：即此两对称节点值相等。
- `L.left.val = R.right.val` ：即L的左子节点和R的右子节点对称
- `L.right.val = R.left.val` ：即L的右子节点和R的左子节点对称

因此，需要判断两个树是否是镜像



递归的目的：

- 判断两个树是否为镜像
- 传入 left or right
- 返回 true or false 

递归的停止条件：

- 传入的树是否为空

递归层级的关系：

- 第一个树的左子树和第二个树的右子树必须是镜像，是则返回true，不是则返回false
- 第一个树的右子树和第二个树的左子树必须是镜像，是则返回true，不是则返回false

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
    public boolean isSymmetric(TreeNode root) {
        return isMirror(root, root);
    }

    private boolean isMirror(TreeNode t1, TreeNode t2) {    
        if (t1 == null && t2 == null) return true;
        if (t1 == null || t2 == null) return false;
        return t1.val == t2.val && isMirror(t1.left, t2.right) && isMirror(t1.right, t2.left);
    }
}
```



## offer54

二叉搜索树的第k大节点 

way1：对BST中序遍历，可以得到正向有序结果

```java
class Solution {
    List<Integer> list = new ArrayList<>();
    public int kthLargest(TreeNode root, int k) {
        inOrder(root);
        return list.get(list.size() - k);
        
    }

    private void inOrder(TreeNode root) {
        if (root.left != null) inOrder(root.left);
        list.add(root.val);
        if (root.right != null) inOrder(root.right);
    }
}
```



way2：对BST先右后左进行中序遍历，可以得到反向有序结果，若 `count == k` 则代表找到，提前停止遍历

```java
class Solution {
    private int res;
    private int count;
    public int kthLargest(TreeNode root, int k) {
        inOrder(root, k);
        return res;
        
    }

    private void inOrder(TreeNode root, int k) {
        if (root.right != null) inOrder(root.right, k);
        if (++count == k) {
            res = root.val;
            return;
        }
        if (root.left != null) inOrder(root.left, k);
    }
}
```





## offer55-1

```java
// 自己写的 不优雅 效率还行
class Solution {
    private int max;

    public int maxDepth(TreeNode root) {
        if (root == null) return 0;
        dfs(root, 1);
        return max;
    }

    private void dfs(TreeNode root, int depth) {
        if (depth > max) max = depth;
        if (root.left != null) dfs(root.left, depth + 1);
        if (root.right != null) dfs(root.right, depth + 1);       
    }
}
```



```java
// 参考答案
class Solution {
    public int maxDepth(TreeNode root) {
        if(root == null) {
            return 0;
        }
        return Math.max(maxDepth(root.left), maxDepth(root.right)) + 1;
    }
}
```



# BFS

## offer32-1

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
    
    public int[] levelOrder(TreeNode root) {
        if (root == null) return new int[0];
        List<Integer> list = new ArrayList<>();
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            TreeNode node = queue.remove();
            list.add(node.val);
            if (node.left != null) queue.add(node.left);
            if (node.right != null) queue.add(node.right);
        }

        int[] res = new int[list.size()];
        for (int i = 0; i < list.size(); i++) {
            res[i] = list.get(i);
        }
        return res;
    }
}
```



## offer32-2

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
    
    public int[] levelOrder(TreeNode root) {
        if (root == null) return new int[0];
        List<Integer> list = new ArrayList<>();
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            TreeNode node = queue.remove();
            list.add(node.val);
            if (node.left != null) queue.add(node.left);
            if (node.right != null) queue.add(node.right);
        }

        int[] res = new int[list.size()];
        for (int i = 0; i < list.size(); i++) {
            res[i] = list.get(i);
        }
        return res;
    }
}
```







## offer32-3

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
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while (!queue.isEmpty()) {
            LinkedList<Integer> list = new LinkedList<>();
            int len = queue.size();
            for (int i = 0; i < len; i++) {
                TreeNode node = queue.remove();
                if (node.left != null) queue.add(node.left);
                if (node.right != null) queue.add(node.right);
                if (res.size() % 2 == 0) {
                    list.addLast(node.val);
                } else {
                    list.addFirst(node.val);
                }
            }  
        res.add(list);
        }
        return res;
    }
}
```







# 树

## offer68-1

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
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null) return null;
        if (root.val < p.val && root.val < q.val) {
            return lowestCommonAncestor(root.right, p, q);
        }
        if (root.val > p.val && root.val > q.val) {
            return lowestCommonAncestor(root.left, p, q);
        }

        return root;
    }
}
```



## offer68-2

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
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || root == p || root == q) return root;
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        if (left == null && right == null) {
            return null;
        } else if (left == null && right != null) {
            return right;
        } else if (left != null && right == null) {
            return left;
        } else {
            return root;
        }
    }
}
```





# 递归

## offer-26

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
    public boolean isSubStructure(TreeNode A, TreeNode B) {
        if (A == null || B == null) return false;

        return isEqual(A, B) || isSubStructure(A.left, B) || isSubStructure(A.right, B);
        
    }

    private boolean isEqual(TreeNode A, TreeNode B) {
        if (B == null) return true;
        if (A == null) return false;
        return A.val == B.val && isEqual(A.left, B.left) && isEqual(A.right, B.right);
    } 
}
```



## offer-64

```java
class Solution {
    public int sumNums(int n) {
        boolean temp = n > 0 && (n += sumNums(n-1)) > 0;
        return n;
    }
}
```





# 动态规划

## 索引

```
lc-198 打家劫舍 
lc-213 打家劫舍2
```



## offer10-1 斐波那契数列

```java
class Solution {
    public int fib(int n) {
        int n1 = 0, n2 = 1;
        int sum = 0;
        for (int i = 0; i < n; i++) {
            sum = (n1 + n2) % 1000000007;
            n1 = n2;
            n2 = sum;
        }
        return n1;
    }
}
```



## offer10-2 青蛙跳台阶

```java
class Solution {
    public int numWays(int n) {
        int n1 = 0, n2 = 1;
        int sum = 1;
        for (int i = 0; i < n; i++) {
            sum = (n1 + n2) % 1000000007;
            n1 = n2;
            n2 = sum;
        }
        return sum;
    }
}
```



## offer42 连续子数组的最大和

```java
class Solution {
    public int maxSubArray(int[] nums) {
       int[] dp = new int[nums.length];
       dp[0] = nums[0];
       int max = dp[0];
       for (int i = 1; i < nums.length; i++) {
            dp[i] = Math.max(nums[i], dp[i - 1] + nums[i]);
            max = dp[i] > max ? dp[i] : max;                       
       }    
       return max;

    }
}
```





## 300 最长递增子序列

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        if (nums.length == 0) return 0;
        int[] dp = new int[nums.length];
        dp[0] = 1;
        int res = 1;
        for (int i = 1; i < nums.length; i++) {
            dp[i] = 1;
            for (int j = 0; j < i; j++) {
                if (nums[i] > nums[j]) {
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
                
            }
            res = Math.max(res, dp[i]);
        }
        return res;
    }
}
```





