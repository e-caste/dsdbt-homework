# Homework 2 - Enrico Castelli s280124

## Data Mining with RapidMiner

### 1.

#### (a)

The most discriminative parameter, which is the root of the decision tree, is `node-caps`, meaning that if the cancer has infiltrated the lymph nodes (`node-caps`=`yes`) there is an higher probability that it will present itself again in the future.  

| node-caps       | yes     | no     |   ? |
| :------------- | :----------: | -----------: | --: |
|  probability of recurrence | 22.97%   | 55.36%   | 37.5% |

#### (b)

The height of the generated Decision Tree is 7, counting the root of the tree.  

An example of a pure partition would be the following:
![pure-partition](pure-partition.png)
Where the decisions to reach it are:  
node-caps[no] -> irradiat[no] -> tumor-size[35-39] -> breast-quad[left_up] -> menopause[ge40] -> no-recurrence-events