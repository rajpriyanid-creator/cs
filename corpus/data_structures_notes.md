# Approved Course Notes — Data Structures

---

## [Source ID: DS-NOTE-01] Binary Tree Inorder Traversal

Inorder traversal is a depth-first traversal method for binary trees.
The sequence of operations for a node is:
1. Traverse the left subtree recursively by calling `inorder(node.left)`.
2. Visit and process the current node (e.g., print `node.value`).
3. Traverse the right subtree recursively by calling `inorder(node.right)`.

For a 3-node binary tree with Root A, Left Child B, and Right Child C ($B \leftarrow A \rightarrow C$), the inorder traversal order is **B, A, C**.

---

## [Source ID: DS-NOTE-02] Recursion Foundations

Recursion is a programming technique where a function calls itself to solve a smaller instance of the same problem.
Every valid recursive function must contain two essential components:
1. **Base Case:** A terminating condition that stops further recursive calls (e.g., `if (node == null) return;`).
2. **Recursive Step:** The call to the function itself with modified arguments moving towards the base case.

When a recursive function reaches a node, it defers processing the current node until the recursive call on its left child returns.

---

## [Source ID: DS-NOTE-03] Call Stack & Execution Depth

When a function call is executed in a program, a new frame is pushed onto the system **Call Stack**.
The frame stores:
- Function parameters
- Local variables
- Return memory address

In deep recursion, each recursive invocation pushes a new frame onto the stack. When the base case is reached, frames are popped off the stack in Last-In, First-Out (LIFO) order, returning control to the caller.
