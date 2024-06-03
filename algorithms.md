# LeetCode Hot100题解

[1. 两数之和](https://leetcode.cn/problems/two-sum/)

`开始使用了两轮循环，实际一次循环就够，边遍历边检查`

```golang
func twoSum(nums []int, target int) []int {
    occ := make(map[int]int)
    for i, num := range nums {
        if j, ok := occ[target-num]; ok {
            return []int{j, i}
        }
        occ[num] = i
    }
    return []int{-1, -1}
}
```

[2. 两数相加](https://leetcode.cn/problems/add-two-numbers/)

`链表相加，常规题，需要注意最后的进位`

```golang
func addTwoNumbers(l1 *ListNode, l2 *ListNode) *ListNode {
    carrier := 0
    ans := &ListNode{}
    tail := ans
    for l1 != nil || l2 != nil || carrier != 0 {
        var val1, val2 int
        if l1 != nil {
            val1 = l1.Val 
            l1 = l1.Next
        }

        if l2 != nil {
            val2 = l2.Val
            l2 = l2.Next
        }

        curSum :=  (val1 + val2 + carrier)
        digit := curSum % 10 
        carrier = curSum / 10
        node := &ListNode{
            Val: digit,
        }
        tail.Next = node
        tail = tail.Next
    } 
    return ans.Next
}
```

[3. 无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/):star::star::star::star:

`这道题比较难理解一些`，核心是找到当前遍历到的字符上一次出现的位置和**不包含重复字符串的子串起点**，**重点是左边界的更新**

```golang
func lengthOfLongestSubstring(s string) int {
	occurance := make(map[byte]int)
	var ans int
	for i, j := 0, 0; i < len(s); i++ {
		left := -1
		if val, ok := occurance[s[i]]; ok {
			left = val
		}
		j = max(left+1, j)
		ans = max(ans, i-j+1)
		occurance[s[i]] = i
	}
	return ans
}

func max(a, b int) int {
	if a > b {
		return a
	}
	return b
}
```

例如"abba"这种case

>1. i = 0，j = 0，occ[a] = 0
>2. i = 1， j = 0，occ[b] = 1
>3. i = 2， j =  2，occ[b] = 2
>4. i = 3， j =  2，occ[a] = 3，注意：此时虽然上次a出现的位置是0，但是计算不重复子串长度的时候不能以`3-0+1`来计算，因为中间可能有重复字符，**不包含重复字符串的子串起点**`j`才是真正的起点（`3-2+1`）

[【hard】4. 寻找两个正序数组的中位数](https://leetcode.cn/problems/median-of-two-sorted-arrays/)

[5. 最长回文子串](https://leetcode.cn/problems/longest-palindromic-substring/)

`中心拓展法：注意边界条件 i >= 0 && j < len(s) && i <= j && s[i] == s[j]` 

```go
func longestPalindrome(s string) string {
	var ans string
	for i := 0; i < len(s); i++ {
		palindrome1 := expand(s, i, i)
		palindrome2 := expand(s, i, i+1)
		if len(palindrome1) > len(ans) {
			ans = palindrome1
		}

		if len(palindrome2) > len(ans) {
			ans = palindrome2
		}
	}
	return ans
}

func expand(s string, i, j int) string {
	for i >= 0 && j < len(s) && i <= j && s[i] == s[j] {
		i--
		j++
	}
	return s[i+1 : j]
}
```

[【hard】10. 正则表达式匹配](https://leetcode.cn/problems/regular-expression-matching/)

[11. 盛最多水的容器](https://leetcode.cn/problems/container-with-most-water/description/)

`贪心法 + 双指针`

```go
func maxArea(height []int) int {
    l, r := 0, len(height) - 1
    ans := 0
    for l < r {
        ans = max(ans, min(height[l], height[r])*(r-l))
        if height[l] < height[r] {
            l++
        } else {
            r--
        }
    }
    return ans
}
```

[15. 三数之和](https://leetcode.cn/problems/3sum/description/)

`可以理解成是固定first，后面用一个双指针，所以时间复杂度是`$O(n^2)$

```go
func threeSum(nums []int) [][]int {
	n := len(nums)
	sort.Ints(nums)
	ans := make([][]int, 0)

    for first := 0; first < n - 2; first++ {
        if first > 0 && nums[first] == nums[first - 1] {
            continue
        }
        target := -1 * nums[first]
        third := n - 1

        for second := first+1; second < third; second++ {
            // 需要和上一次枚举的数不相同
            if second > first + 1 && nums[second] == nums[second - 1] {
                continue
            }

            // 需要保证 b 的指针在 c 的指针的左侧
            for second < third && nums[second] + nums[third] > target {
                third--
            }

            if second == third {
                break
            }

            if nums[second] + nums[third] == target {
                ans = append(ans, []int{nums[first], nums[second], nums[third]})
            } 
        }
    }
    return ans
}
```



