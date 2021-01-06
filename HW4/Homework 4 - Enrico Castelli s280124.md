# Homework 4 - Enrico Castelli s280124

## Optimizer

### 1. Relational algebra

<img src="HW4 Relational Algebra.svg" alt="HW4 Relational Algebra" style="zoom:100%;" />

Types of JOIN:

1. CS-B: hash join
2. CC-OS: hash join
3. [CC-OS]-S: nested loop, where S is the inner table
4. antisemijoin: nested loop, where the table on the right is the inner table

Types of GROUP BY:

1. GB SId: sort
2. GB BId: hash

The GROUP BY anticipation of GB BId is not possible, since the antisemijoin below it requires the SId attribute in the tuples of the joined tables.

The GROUP BY anticipation of GB SId TODO

### 2. Physical structures

| Table | Index                                  | Access Path                             |
| ----- | -------------------------------------- | --------------------------------------- |
| CS    | primary B+tree on date                 | index range scan                        |
| B     | primary hash on city                   | full index scan                         |
| CC    | secondary hash on region               | fast full index scan + access by row id |
| OS    | no index because reduction factor is 1 |                                         |
| S     | no index because table is small        |                                         |

