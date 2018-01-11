---
layout: post
title: 1_Two_Sum
---

#### Two Sum题干：

>Given an array of integers, return indices of the two numbers such that they
>add up to a specific target.
>You may assume that each input would have exactly one solution, and you may
>not use the same element twice. 
>Example:
>Given nums = [2, 7, 11, 15], target = 9,
>Because nums[0] + nums[1] = 2 + 7 = 9,
>return [0, 1].

两层for循环，最天真，是因为时间复杂度最高O(n<sup>2</sup>)，但是不能忘它是空间复杂度最低的O(1)。但是最天真的遍历不代表它没什么好说的。

#### C++解一：

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
        throw std::invalid_argument("No solution");
    }   
};
{% endhighlight %}

#### Python2.x/3.x解一：

{% highlight python%}
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        for i in range (0, len(nums)):
            for j in range(i+1, len(nums)):
                if nums[i]+nums[j] == target:
		    return [i, j]
        raise ValueError("No solution") 
{% endhighlight %}

#### Java解一：

{% highlight java %}
class Solution {
    public int[] twoSum(int[] nums, int target) {
        for (int i=0; i<nums.length; i++){
            for (int j=i+1; j<nums.length; j++){
                if (nums[i]+nums[j]==target){
                    return new int[] { i,j };
                }
            }
        }
        throw new IllegalArgumentException("No solution");
    }
}
{% endhighlight %}

#### C解一：

{% highlight c %}
/**
 * Note: The returned array must be malloced, assume caller calls free().
 **/
int* twoSum(int* nums, int numsSize, int target) {
    static int result[2]= { -1, -1 };
    for(int i = 0; i < numsSize; i++)
    {
        for(int j = i+1; j < numsSize; j++)
        {
            if(nums[i]+nums[j] == target)
            {
                result[0] = i ;
                result[1] = j ;
                return result;
            }
        }
    }
    return NULL;
}
{% endhighlight%}

#### C#解一:

{% highlight cs %}
public class Solution {
    public int[] TwoSum(int[] nums, int target) {
         for (int i=0; i<nums.Length; i++){
            for (int j=i+1; j<nums.Length; j++){
                if (nums[i]+nums[j]==target){
                    return new int[] { i,j };
                }
            }
        }
        throw new ArgumentNullException("No solution");
    }
}
{% endhighlight %}

#### Javascript解一：

{% highlight js %}
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 **/
var twoSum = function(nums, target) {
    for (var i=0; i<nums.length; i++){
        for (var j=i+1; j<nums.length; j++){
            if (nums[i]+nums[j]===target){
                return [i,j];
            }
        }
    }
    throw new Error("No solution");
};
{% endhighlight %}

#### Ruby解一:

{% highlight ruby %}
# @param {Integer[]} nums
# @param {Integer} target
# @return {Integer[]}
def two_sum(nums, target)
  nums.each_with_index do |num1, i|
    nums[i+1..-1].each_with_index do |num2, j|
      return [i, i+j+1] if num1 + num2 == target
    end
  end
  return "No solution"
end
{% endhighlight %}

#### Swift解一：

{% highlight swift %}
class Solution {
    func twoSum(_ nums: [Int], _ target: Int) -> [Int] {
        var result = [Int]()
         for i in 0..<nums.count {
            for j in i+1..<nums.count {
                if nums[i] + nums[j] == target {
                    result.append(i)
                    result.append(j)
                    return result
                }                
            }
         }
    return result  
    }
}
{% endhighlight %}

#### Golang解一：
{% highlight go %}
func twoSum(nums []int, target int) []int {
    for i := 0; i < len(nums); i++ {
        for j := i + 1; j < len(nums); j++ {
            if nums[i] + nums[j] == target {
                return []int{i, j}
            }
        }
    }
    return nil
}
{% endhighlight%}

#### Kotlin解一：
{% highlight kotlin %}
class Solution {
    fun twoSum(nums: IntArray, target: Int): IntArray {
        for( i in 0..nums.size-1){
            for( j in i+1..nums.size-1){
                if(nums[i] + nums[j] == target){
                    return intArrayOf(i,j)
                }
            }
        }        
    return intArrayOf()
    }
}
{% endhighlight %}

