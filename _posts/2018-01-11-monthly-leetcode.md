---
layout: post
title: 折腾一个Two_Sum
---
<div class="message">
在这篇文章里，我用了10个计算机语言解决一个算法小问题。再看了下Leetcode执行它们的性能。
</div>

#### Two Sum题干：

>Given an array of integers, return indices of the two numbers such that they
>add up to a specific target.
>You may assume that each input would have exactly one solution, and you may
>not use the same element twice. 
>Example:
>Given nums = [2, 7, 11, 15], target = 9,
>Because nums[0] + nums[1] = 2 + 7 = 9,
>return [0, 1].

两层for循环，最天真，是因为时间复杂度最高O(n<sup>2</sup>)，但是不能忘它是空间复杂度最低的O(1)。但是最天真的遍历不代表它没什么好折腾的。可以换语言写写。

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
Performance: 190ms

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
Performance: python2 5543ms / python3 time out

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
Performance: 43ms

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
Performance: 103ms

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
Performance: 805ms

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
Performance: 200ms

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
Performance: 5624ms

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
Performance: 947ms

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
Performance: 63ms

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
Performance: 593ms

#### Home Studio不专业分析：

蛮无聊的吧，每个代码我都尽力让他们相近了，`Ruby`, `Swift`, `Golang`和`Kotlin`全部都不懂异常，就被我忽悠了。
过去学语言都掉进了语法这个白痴的误区里，我现在问我自己它们之间有啥各自的语法特性和优劣，我根本就说不出来，很衰。
所以唯一的意义是温习了一遍语法，不过我把它们都送到Leetcode里去运行了，从过去有限的刷题经验上看各种语言的测试集是一样的。
整理了它们的运行时间绘制了一张图。
![placeholder](/image/2018-01-11-Leetcode.png "Leetcode Performance")

关于正式的计算机语言Benchmark看 [The Computer Language Benchmarks Game](https://benchmarksgame.alioth.debian.org/) 。

图和Benchmark比较，除了`Java`和`Go`快的不正常，`C++`看起来有点慢，但是不知道编译器，`Python`真是不济，其他排序还挺理性。我想，

- Leetcode里的`Java`和`go`，应该不是`CPU运行时间`。

- `Python3`直接Time Out，在单独的手动测试集上`Python3`竟然比`Python2`慢将近一倍。这个真意外。

#### 关于这篇博客一些有意思的东西：

- 这是我fork [Hyde](https://github.com/poole/hyde) 后修改的一个`Github Page`

- 博文是我在 [Vim](http://www.vim.org/) 里搭配插件 [livemark.vim](https://github.com/miyakogi/livemark.vim) 直接码的，目的是熟悉下 [Jerkll](https://jekyllrb.com/) 的`Markdown`

- 图表是在 [Infogram](https://infogram.com/) 里生成的

- 题目和测试都是在 [Leetcode](https://leetcode.com/) 上进行的

- 期间把 [阿狗](https://www.instagram.com/thatwolfiefeeling/) 忘记，以至于它被泡在洗衣粉里一晚

- 一直单曲重复`Luca Brasi`的 [Theme Song from Hq](https://www.youtube.com/watch?v=zA8Db1uOLDE) 

- 发现Markdown定义的符号里嵌套html的tag会失败，这样的`O(n<sup>2</sup>)`和 O(n<sup>2</sup>)

如果有建议，观点或者问题的话，可以给我发邮件`aileen365@gmail.com`，因为我还不知道该怎么在静态网页里设`留言板`，有主意欢迎告诉我。


