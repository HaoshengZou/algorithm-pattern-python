# 二叉树

## 知识点

二叉树的递归定义是二叉树的重要定义方式之一（还可用图论术语定义）。
既然定义都是递归的，那解法很自然很多都是递归的（注意，递归+分治，之前回溯法也是递归调用）。
@211016：暂时先只掌握递归解法，和基本的非递归遍历，和层序遍历。

### 二叉树遍历

**前序遍历**：**先访问根节点**，再前序遍历左子树，再前序遍历右子树

**中序遍历**：先中序遍历左子树，**再访问根节点**，再中序遍历右子树

**后序遍历**：先后序遍历左子树，再后序遍历右子树，**再访问根节点**

注意点

- 以根访问顺序决定是什么遍历
- 左子树都是优先右子树

#### 递归模板

- 递归实现二叉树遍历非常简单，不同顺序区别仅在于访问父结点顺序

```Python
def preorder_rec(root):
    if root is None:
        return
    visit(root)
    preorder_rec(root.left)
    preorder_rec(root.right)
    return

def inorder_rec(root):
    if root is None:
        return
    inorder_rec(root.left)
    visit(root)
    inorder_rec(root.right)
    return

def postorder_rec(root):
    if root is None:
        return
    postorder_rec(root.left)
    postorder_rec(root.right)
    visit(root)
    return
```

#### 非递归模板

硬想、包括力扣官方题解，都没法用统一的方式记忆三种顺序的非递归遍历。

"颜色标记法"，可以统一记忆，只需更改中间三行append的顺序（注意用stack左右总是反一下）：

```python
def postorderTraversal(self, root: TreeNode) -> List[int]:
    WHITE, GRAY = 0, 1
    result = []
    stack = [(WHITE, root)]
    while stack:
        color, node = stack.pop()
        if node is None: continue  # 保留这个写法，记忆signature
        if color == WHITE:
            stack.append((GRAY, node))  # 无论什么顺序，root总是子树入口，第二次总是压GRAY入栈
            stack.append((WHITE, node.right))
            stack.append((WHITE, node.left))
        else:
            result.append(node.val)
    return result
```


#### [前序非递归](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/)

- DFS的一个特例（但其他序不是），因此可以用栈来实现。记下自己想出来的版本。
- 注意常见细节：用stack，左右反一下，最终可以先左。

```Python
class Solution:
    def preorderTraversal(self, root: TreeNode) -> List[int]:
        result = []
        s = [root]

        while s:
            node = s.pop()
            if node is not None:
                result.append(node.val)
                s.append(node.right)  # 用stack，左右反一下，最终可以先左。
                s.append(node.left)
        
        return result
```

#### [中序非递归](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

> 递归的时候隐式地维护了一个栈（函数调用栈），而我们在迭代的时候需要显式地将这个栈模拟出来

官方题解的这句话并不能帮助写出代码……
虽然不喜欢官方题解中while循环的双or终止条件，但自己想出的版本也只是歪打正着。
while循环的双or终止条件还是可以作为三种顺序的统一终止条件的。

```python
class Solution:
    def inorderTraversal(self, root: TreeNode) -> List[int]:
        result = []
        s = [root]
        last_node = root

        while s:
            peek = s[-1]
            if peek is not None:
                if last_node is None:  # time to visit this one
                    # 左孩子为None，或刚访问了左子树最后一个叶子的右孩子None
                    # 【其实也不是很直观，有点歪打正着。也导致后序出错了！之后删掉这种做法！
                    last_node = s.pop()
                    result.append(last_node.val)
                    s.append(last_node.right)
                else:
                    s.append(peek.left)  # left subtree first
            else:  # peek is None
                last_node = s.pop()
        
        return result
```

#### [后序非递归](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/)

力扣有个评论，确实就后序最难硬想出来。

```Python
class Solution:
    def postorderTraversal(self, root: TreeNode) -> List[int]:

        s, postorder = [], []
        node, last_visit = root, None
        
        while len(s) > 0 or node is not None:
            if node is not None:
                s.append(node)
                node = node.left
            else:
                peek = s[-1]
                if peek.right is not None and last_visit != peek.right:
                    node = peek.right
                else:
                    last_visit = s.pop()
                    postorder.append(last_visit.val)
        
        
        return postorder
```

[comment]: 注意点核心就是：根节点必须在右节点弹出之后，再弹出

#### DFS 深度搜索-从下向上（分治法）（仍为递归）

```Python
class Solution:
    def preorderTraversal(self, root: TreeNode) -> List[int]:
        
        if root is None:
            return []
        
        left_result = self.preorderTraversal(root.left)
        right_result = self.preorderTraversal(root.right)
        
        return [root.val] + left_result + right_result
```

注意点：

> 从上向下和从下向上（分治法/递归）区别：前者一般将最终结果通过指针参数传入，后者一般递归返回结果最后合并，比如此例。

#### [层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

层序遍历是BFS的特例（前序遍历是DFS特例）

```Python
class Solution:
    def levelOrder(self, root: TreeNode) -> List[List[int]]:
        if root is None: return []  # 实际做题单独处理dummy input靠谱
        result = []
        bfs = collections.deque([root])  # 这样bfs中保证从来没有None节点

        while bfs:
            level = []
            level_size = len(bfs)
            for _ in range(level_size):
                node = bfs.popleft()
                level.append(node.val)
                if node.left is not None:
                    bfs.append(node.left)
                if node.right is not None:
                    bfs.append(node.right)

            result.append(level)

        return result
```

## 分治法应用（注意二叉树也经常用到分治法思想的！）

先分别处理局部，再合并结果

适用场景

- 快速排序
- 归并排序
- 二叉树相关问题

分治法模板

- 递归返回条件
- 分段处理
- 合并结果

## 常见题目示例

### [maximum-depth-of-binary-tree](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

> 给定一个二叉树，找出其最大深度。

- 思路 1：递归模板

```Python
class Solution:
    def maxDepth(self, root: TreeNode) -> int:        
        if root is None:
            return 0
        
        return 1 + max(self.maxDepth(root.left), self.maxDepth(root.right))
        # 可以递归内部函数，也可以递归自己 
```

或者反过来计算深度：（但是，在下一道判断平衡二叉树时，还是叶子节点从0起返回更好）
```python
class Solution:
    def maxDepth(self, root: TreeNode) -> int:
        def depth(node, d):
            if node is None:
                return d
            return max(depth(node.left, d + 1), depth(node.right, d + 1))
        return depth(root, 0)
```

- 思路 2：层序遍历（略过了）

```Python
class Solution:
    def maxDepth(self, root: TreeNode) -> List[List[int]]:        
        depth = 0
        if root is None:
            return depth
        
        bfs = collections.deque([root])
        
        while bfs:
            depth += 1  # 模板此处是返回遍历结果，这里只需记录深度
            level_size = len(bfs)
            for _ in range(level_size):
                node = bfs.popleft()
                if node.left is not None:
                    bfs.append(node.left)
                if node.right is not None:
                    bfs.append(node.right)
        
        return depth
```

### [balanced-binary-tree](https://leetcode-cn.com/problems/balanced-binary-tree/)

> 给定一个二叉树，判断它是否是高度平衡的二叉树。

- 思路 1：递归，左边平衡 && 右边平衡 && 左右两边高度 <= 1（平衡的定义）。O(n)

```Python
class Solution:
    def isBalanced(self, root: TreeNode) -> bool: 
        def depth(root):
            if root is None:
                return 0, True  # depth, is_balanced
            
            dl, bl = depth(root.left)
            dr, br = depth(root.right)
            return max(dl, dr) + 1, bl and br and abs(dl - dr) < 2
        
        _, out = depth(root)
        
        return out
```

- 思路 2：使用后序遍历实现分治法的迭代版本

```Python
class Solution:
    def isBalanced(self, root: TreeNode) -> bool:

        s = [[TreeNode(), -1, -1]]
        node, last = root, None
        while len(s) > 1 or node is not None:
            if node is not None:
                s.append([node, -1, -1])
                node = node.left
                if node is None:
                    s[-1][1] = 0
            else:
                peek = s[-1][0]
                if peek.right is not None and last != peek.right:
                    node = peek.right
                else:
                    if peek.right is None:
                        s[-1][2] = 0
                    last, dl, dr = s.pop()
                    if abs(dl - dr) > 1:
                        return False
                    d = max(dl, dr) + 1
                    if s[-1][1] == -1:
                        s[-1][1] = d
                    else:
                        s[-1][2] = d
        
        return True
```

### [binary-tree-maximum-path-sum](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)

> 给定一个**非空**二叉树，返回其最大路径和。

- 思路：分治法。合并左右子树的结果，分类讨论。

- 最大路径的可能情况：左子树的最大路径，右子树的最大路径，或通过根结点的最大路径。其中通过根结点的最大路径值等于以左子树根结点为端点的最大路径值加以右子树根结点为端点的最大路径值再加上根结点值，这里还要考虑有负值的情况即负值路径需要丢弃不取。

```Python
class Solution:
    def maxPathSum(self, root: TreeNode) -> int:
        self.maxPath = float('-inf')  # 对象直接通过引用在递归调用中共享，primitive type必须这样才能共享
        
        def largest_path_ends_at(node):
            if node is None:
                return float('-inf')  # 记住这里返回-inf而非0！例如输入[-3]，左右向答案的贡献变成了0
            
            e_l = largest_path_ends_at(node.left)
            e_r = largest_path_ends_at(node.right)
            
            self.maxPath = max(self.maxPath, node.val + max(0, e_l) + max(0, e_r), e_l, e_r)
            
            return node.val + max(e_l, e_r, 0)  # 这里有个0，处理叶子节点返回值
        
        largest_path_ends_at(root)
        return self.maxPath
```

### [lowest-common-ancestor-of-a-binary-tree](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

> 给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

- 思路：分治法递归，一个函数同时搜索p和q，找到即返回p或q本身

- 合并结果：
  - pq分处两侧，root即为解
  - 左右均为None，继续返回None
  - 仅一边非None，返回这一边，说明这一边在当前或者之前找到了解

- [次官方题解](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/solution/236-er-cha-shu-de-zui-jin-gong-gong-zu-xian-hou-xu/) 很好。

```Python
class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        if root is None or root == p or root == q:
            return root
        left = self.lowestCommonAncestor(root.left, p, q)
        right = self.lowestCommonAncestor(root.right, p, q)
        
        if not left:
            return right
        if not right:
            return left
        return root
```

### 层序遍历应用

### [binary-tree-zigzag-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

> 给定一个二叉树，返回其节点值的锯齿形层次遍历。Z 字形遍历

- 思路：在BFS迭代模板上，写结果时用deque颠倒顺序

```Python
class Solution:
    def zigzagLevelOrder(self, root: TreeNode) -> List[List[int]]:
        levels = []
        if root is None:  # 实际做题还是单独处理dummy input靠谱
            return levels
        
        bfs = collections.deque([root])  # 正好作为deque的操练
        level = collections.deque([])
        append_this = level.append
        
        while bfs:
            level_size = len(bfs)
            for _ in range(level_size):
                node = bfs.popleft()
                append_this(node.val)
                
                if node.left is not None:
                    bfs.append(node.left)
                if node.right is not None:
                    bfs.append(node.right)
            
            levels.append(list(level))
            append_this = level.appendleft if append_this == level.append else level.append  # 也可以土一点，这里比较骚
            level.clear()
        
        return levels
```

### 二叉搜索树应用

### [validate-binary-search-tree](https://leetcode-cn.com/problems/validate-binary-search-tree/)

> 给定一个二叉树，判断其是否是一个有效的二叉搜索树。

- 递归：直觉先想到，相对好想到一些，但也不trivial，要加lower bound和upper bound，不能只比较root.val和父亲

```python
class Solution:
    def isValidBST(self, root: TreeNode) -> bool:
        def isBST(root, lb=float('-inf'), ub=float('inf')):
            if root is None:
                return True
            if not (lb < root.val < ub):
                return False
            left_isBST = isBST(root.left, lb, root.val)
            right_isBST = isBST(root.right, root.val, ub)
            return left_isBST and right_isBST
        
        return isBST(root)
```

不过，不能碰到False提前终止整个函数调用栈、输出。

- 利用性质：BST中序遍历应得到升序序列。递归中序遍历仍不能提前输出，故用非递归遍历的模板。

```python
class Solution:
    def isValidBST(self, root: TreeNode) -> bool:
        pre_val = float('-inf')  # result
        WHITE, GRAY = 0, 1
        s = [(WHITE, root)]

        while s:
            color, node = s.pop()
            if node is None: continue
            if color == WHITE:
                s.append((WHITE, node.right))
                s.append((GRAY, node))
                s.append((WHITE, node.left))
            else:
                if node.val <= pre_val:
                    return False
                pre_val = node.val  # result
        
        return True
```


#### [insert-into-a-binary-search-tree](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)

> 给定二叉搜索树（BST）的根节点和要插入树中的值，将值插入二叉搜索树。 返回插入后二叉搜索树的根节点。

- 思路：找到最后一个叶子节点，插入即可。但此题深挖可以涉及到如何插入并维持平衡二叉搜索树的问题，优先级很低。

```Python
class Solution:
    def insertIntoBST(self, root: TreeNode, val: int) -> TreeNode:
        if root is None:
            return TreeNode(val)
        node, parent = root, None
        while node is not None:
            parent = node
            node = node.left if val < node.val else node.right
        
        if val < parent.val:
            parent.left = TreeNode(val)
        else:
            parent.right = TreeNode(val)
        return root
```

## 总结

- 掌握二叉树递归与非递归遍历
- 理解 DFS 前序遍历与分治法
- 理解 BFS 层序遍历

## 练习

- [ ] [maximum-depth-of-binary-tree](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)
- [ ] [balanced-binary-tree](https://leetcode-cn.com/problems/balanced-binary-tree/)
- [ ] [binary-tree-maximum-path-sum](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)
- [ ] [lowest-common-ancestor-of-a-binary-tree](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)
- [ ] [binary-tree-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)
- [ ] [binary-tree-level-order-traversal-ii](https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/)
- [ ] [binary-tree-zigzag-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)
- [ ] [validate-binary-search-tree](https://leetcode-cn.com/problems/validate-binary-search-tree/)
- [ ] [insert-into-a-binary-search-tree](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)
