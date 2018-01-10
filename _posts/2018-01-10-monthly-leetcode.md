---
layout: post
title: Leetcode 1.Two_Sum
---

<div class="message">
威玲旺卡的Leetcode的代码[Repo](https://github.com/notagenius/codejam_practice)
</div>

第一题two sum的题目：

>Given an array of integers, return indices of the two numbers such that they
>add up to a specific target.
>You may assume that each input would have exactly one solution, and you may
>not use the same element twice. 
>Example:
>Given nums = [2, 7, 11, 15], target = 9,
>Because nums[0] + nums[1] = 2 + 7 = 9,
>return [0, 1].

人人所说的最天真解法，两个for循环嵌套，最天真，是因为时间复杂度最高，但是不要忘记这是空间复杂度最低的。所以，记得考量。

C++解法：

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


