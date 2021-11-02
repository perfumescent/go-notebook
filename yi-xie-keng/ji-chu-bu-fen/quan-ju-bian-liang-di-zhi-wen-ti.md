# 全局变量地址问题

力扣 [**145. 二叉树的后序遍历**](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/)&#x20;

```go
var res []int
func postorderTraversal(root *TreeNode) []int {
    p(root)
    return res
}
func p(root *TreeNode){
    if root != nil{
        p(root.Left)
        p(root.Right)
        res = append(res, root.Val)
    }

```

以上代码在测试用例为 \[] 时输出为 \[] ，但在提交中的 \[] 用例时却发生错误，显示输出是\[1,2,3]。

我估计是全局变量在多次调用中，导致内存中res指向的内容没有清除。加了初始化代码后便正常了。

```go
var res []int
func postorderTraversal(root *TreeNode) []int {
    res = []int{}
    p(root)
    return res
}
func p(root *TreeNode){
    if root != nil{
        p(root.Left)
        p(root.Right)
        res = append(res, root.Val)
    }
}
```
