
---
2391
https://leetcode.com/problems/minimum-amount-of-time-to-collect-garbage/description/
SOL:
- find the last position of all trucks
- result = total garbage + truck position travel expense
EX:
**Input:** garbage = ["G","P","GP","GG"], travel = [2,4,3]
M_pos = 0
G_pos = 3
P_post = 2
total_garbage = 6
--> result = 6 + 0 + (2+4+3) + (2+4) = 21

**Input:** garbage = ["MMM","PGM","GP"], travel = [3,10]
M_pos = 1
G_pos = 2
P_post = 2
total_garbage = 8
--> result = 8 + 3 + (13) + (13) = 37