---
layout: post
title: Leetcode 1_Two_Sum
---

two sum题干：

>Given an array of integers, return indices of the two numbers such that they
>add up to a specific target.
>You may assume that each input would have exactly one solution, and you may
>not use the same element twice. 
>Example:
>Given nums = [2, 7, 11, 15], target = 9,
>Because nums[0] + nums[1] = 2 + 7 = 9,
>return [0, 1].

两个for循环嵌套，最天真，是因为时间复杂度最高，但是不能忘它是空间复杂度最低的。

C++解一：

{% highlight cpp %}
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
	vector<int> result;
   	for (int i=0; i<nums.size();++i){
		for(int j=i+1; j<nums.size();++j){
			if(nums[i]+nums[j]==target){
				result.push_back(i);
				result.push_back(j);
				return result;
			}
		}
	}
    }
};
{% endhighlight %}

Python解一：

{% hightlight python%}
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        for x in range (0, len(nums)):
            for y in range(x+1, len(nums)):
                if nums[x]+nums[y] == target:
return [x, y] 
{% endhighlight %}

