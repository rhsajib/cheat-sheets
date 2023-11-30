### Binary search 

- ref: https://leetcode.com/problems/koko-eating-bananas/solutions/769702/python-clear-explanation-powerful-ultimate-binary-search-template-solved-many-problems/

#### Intro
Binary Search is quite easy to understand conceptually. Basically, it splits the search space into two halves and only keep the half that probably has the search target and throw away the other half that would not possibly have the answer. In this manner, we reduce the search space to half the size at every step, until we find the target. Binary Search helps us reduce the search time from linear O(n) to logarithmic O(log n). But when it comes to implementation, it's rather difficult to write a bug-free code in just a few minutes. Some of the most common problems include:

- When to exit the loop? Should we use left < right or left <= right as the while loop condition?
- How to initialize the boundary variable left and right?
- How to update the boundary? How to choose the appropriate combination from left = mid , left = mid + 1 and right = mid, right = mid - 1?

A rather common misunderstanding of binary search is that people often think this technique could only be used in simple scenario like "Given a sorted array, find a specific value in it". As a matter of fact, it can be applied to much more complicated situations.

After a lot of practice in LeetCode, I've made a powerful binary search template and solved many Hard problems by just slightly twisting this template. I'll share the template with you guys in this post. I don't want to just show off the code and leave. Most importantly, I want to share the logical thinking: how to apply this general template to all sorts of problems. Hopefully, after reading this post, people wouldn't be pissed off any more when LeetCoding, "Holy sh*t! This problem could be solved with binary search! Why didn't I think of that before!"


#### Most Generalized Binary Search
Suppose we have a search space. It could be an array, a range, etc. Usually it's sorted in ascend order. For most tasks, we can transform the requirement into the following generalized form:

Minimize k , s.t. condition(k) is True

The following code is the most generalized binary search template:

    ```python
    def binary_search(array) -> int:
    def condition(value) -> bool:
        pass

    left, right = 0, len(array)
    while left < right:
        mid = left + (right - left) // 2
        if condition(mid):
            right = mid
        else:
            left = mid + 1
    return left
    ```

What's really nice of this template is that, for most of the binary search problems, we only need to modify three parts after copy-pasting this template, and never need to worry about corner cases and bugs in code any more:

- Correctly initialize the boundary variables left and right. Only one rule: set up the boundary to include all possible elements;
- Decide return value. Is it return left or return left - 1? Remember this: after exiting the while loop, left is the minimal k​ satisfying the condition function;
- Design the condition function. This is the most difficult and most beautiful part. Needs lots of practice.
  
Below I'll show you guys how to apply this powerful template to many LeetCode problems.

#### Basic Application

#### [278. First Bad Version [Easy]](https://leetcode.com/problems/first-bad-version/description/)

