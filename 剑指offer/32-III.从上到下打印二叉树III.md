### 题目：剑指offer 32-III.从上到下打印二叉树III
请实现一个函数按照之字形顺序打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右到左的顺序打印，第三行再按照从左到右的顺序打印，其他行以此类推。


例如:

给定二叉树: `[3,9,20,null,null,15,7]`,

![image](https://user-images.githubusercontent.com/41363767/159850014-40041565-0c0d-46b1-81ef-fce1f1f3442a.png)

返回其层次遍历结果：
```
[
  [3],
  [20,9],
  [15,7]
]
```

**提示：**
- 节点总数 <= 1000

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-iii-lcof


### 解题过程
#### 提交1 - 用辅助栈来反序输出
```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> lorder;
        if (!root)
            return lorder;
        deque<TreeNode*> queue = { root };
        TreeNode* last = root;
        vector<int> curr_level;
        bool leftToRight = true;
        while (!queue.empty()) {
            int curr_num = queue.size();
            stack<int> stack;
            for (int i = 0; i < curr_num; ++i) {
                TreeNode* current = queue.front();
                if (leftToRight)
                    curr_level.push_back(current->val);
                else
                    stack.push(current->val);
                queue.pop_front();
                if (current->left != NULL)
                    queue.push_back(current->left);
                if (current->right != NULL)
                    queue.push_back(current->right);
            }
            while (!stack.empty()) {
                curr_level.push_back(stack.top());
                stack.pop();
            }
            lorder.push_back(curr_level);
            curr_level.clear();
            leftToRight = !leftToRight;
        }
        return lorder;
    }
};
```
**结果：** 执行用时: 4 ms        内存消耗: 13.1 MB

**分析：**
用bool型变量`leftToRight`交替控制从左到右的打印还是从右到左打印输出。
- 当从左到右打印时，出队列且直接插入结果vector中即可；
- 当从右到左打印时，每次出队列先入栈，当需要换行时，不断出栈并插入结果vector。
每换行一次，`leftToRight`值反转一次。
