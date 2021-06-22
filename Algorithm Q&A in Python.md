
# Data Science Algorithm Coding Question Answer :+1:

While in most data science interviews, algorithm is not mandatory required by the company. It's always a plus to know some of the high frequency ones while preparing for the interview. The purpose of this repo is to quick refresh on Algorithm interview questions in Python. The questions will be ranked by frequency and the answers will be provided in Python.

* **1.Two Sum**
* Given an array of integers nums and an integer target, return indices of the two numbers such that they add up to target.

* Python Answer
```
class Solution:
    def twoSum(self, nums: List[int], target: int):
        seen={}
        for i,v in enumerate(nums):
            remaining=target-v
            if remaining in seen:
                     return [seen[remaining],i]
            seen[v]=i
        return[]

 ```
* **2.Best time to buy and sell stock**
* 

* Python Answer
```

```

* **3.Verify an Alien Dictionary**
* 

* Python Answer
```

```

* **4.Valid Parentheses**
* 

* Python Answer
```

```

* **5.Reverse Integer**
* 

* Answer
```

```
