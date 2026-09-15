# Data Structures Prerequisite Graph Specification

This document details the prerequisite dependency topology and diagnostic triggers for the event prototype.

---

## 1. Graph Topology

```text
[Node 1: binary_tree_inorder_traversal]
   │
   ├── [Node 2: traversal_ordering_rules] (Left -> Node -> Right)
   │
   └── [Node 3: recursion]
         │
         └── [Node 4: call_stack_reasoning] (Stack depth & base cases)
```

---

## 2. Concept Node Definitions

### Node 1: `binary_tree_inorder_traversal` (Target Concept)
- **Description:** Visiting every node in a binary tree in symmetric order ($L \rightarrow N \rightarrow R$).
- **Success Criteria:** Correctly produces sequence `B, A, C` for tree `A(B, C)`.
- **Misconception Patterns:**
  - `preorder_confusion`: Outputs `Root, Left, Right` (`A, B, C`).
  - `postorder_confusion`: Outputs `Left, Right, Root` (`B, C, A`).

### Node 2: `traversal_ordering_rules` (Prerequisite 1)
- **Description:** Grasping the rule definition without recursive implementation details.
- **Tie-Breaker Question:** "In a 3-node tree $A \leftarrow B \rightarrow C$, which node is processed first in inorder traversal?"
- **Expected Answer:** "Node B" (the left child).

### Node 3: `recursion` (Prerequisite 2 - Diagnostic Focus)
- **Description:** Understanding recursive function call execution.
- **Diagnostic Question:** "When `inorder(node)` is called, what action occurs before printing the current node's value?"
- **Expected Answer:** "Call `inorder(node.left)` to process the left subtree."

### Node 4: `call_stack_reasoning` (Deep Prerequisite)
- **Description:** Stack frame allocation, base case condition, and return handling.
- **Diagnostic Question:** "What prevents `inorder(node.left)` from causing infinite recursion when reaching a leaf node?"
- **Expected Answer:** "The base case check `if (node == null) return;`."

