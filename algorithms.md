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

:star::star::star::star:[3. 无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)

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