



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

[15. 三数之和](https://leetcode.cn/problems/3sum/description/):star::star::star::star:

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

17.[电话号码的组合](https://leetcode.cn/problems/letter-combinations-of-a-phone-number/description/)

`dfs + 回溯，参考`[golang题解](https://books.halfrost.com/leetcode/ChapterFour/0001~0099/0017.Letter-Combinations-of-a-Phone-Number/),

```go
func letterCombinations(digits string) []string {
	var ans []string
	if len(digits) == 0 {
		return ans
	}
	helper(0, digits, "", &ans)
	return ans
}

func helper(i int, s, cur string, ans *[]string) {
	if i >= len(s) {
		*ans = append(*ans, cur)
		return
	}

	letters := dict[rune(s[i])]
	for k := 0; k < len(letters); k++ {
		helper(i+1, s, cur+string(letters[k]), ans)
	}

}

// dfs
var dict = map[rune]string{
	'2': "abc",
	'3': "def",
	'4': "ghi",
	'5': "jkl",
	'6': "mno",
	'7': "pqrs",
	'8': "tuv",
	'9': "wxyz",
}
```

[19. 删除链表的倒数第 N 个结点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)

`双指针或者栈`

- 双指针

```go
func removeNthFromEnd(head *ListNode, n int) *ListNode {
    if head == nil {
        return nil
    }

    dummy := &ListNode{0, head}
    pre := dummy
    cur := head

    for n > 0 && cur != nil {
        cur = cur.Next
        n--
    }

    for ; cur != nil; cur = cur.Next {
        pre = pre.Next
    }
    pre.Next = pre.Next.Next
    return dummy.Next
}
```

- 栈

```go
func removeNthFromEnd(head *ListNode, n int) *ListNode {
    nodes := []*ListNode{}
    dummy := &ListNode{0, head}
    for node := dummy; node != nil; node = node.Next {
        nodes = append(nodes, node)
    }
    prev := nodes[len(nodes)-1-n]
    prev.Next = prev.Next.Next
    return dummy.Next
}
```

[20. 有效的括号](https://leetcode.cn/problems/valid-parentheses/)

`栈	`

```go
func isValid(s string) bool {
    // stack
    var stk []rune
    dict := map[rune]rune {
        ')' : '(',
        ']' :'[', 
        '}': '{',
    }

    for _, ch := range s {
        if len(stk) == 0 {
            stk = append(stk, ch)
            continue
        }  
        if val, ok := dict[ch]; ok && val == stk[len(stk)-1] {
            stk = stk[:len(stk)-1]
        } else {
            stk = append(stk, ch)
        }
    }

    return len(stk) == 0
}
```



[21. 合并两个有序链表](https://leetcode.cn/problems/merge-two-sorted-lists/)

`递归或者迭代`

- 递归

```go
func mergeTwoLists(list1 *ListNode, list2 *ListNode) *ListNode {
	if list1 == nil {
		return list2
	} else if list2 == nil {
		return list1
	} else if list1.Val > list2.Val {
		list2.Next = mergeTwoLists(list1, list2.Next)
		return list2
	}
	list1.Next = mergeTwoLists(list1.Next, list2)
	return list1
}
```

[:star::star::star:22. 括号生成](https://leetcode.cn/problems/generate-parentheses/)

`dfs+剪枝`

>第一次尝试的时候只能想到生成所有的 $2^{2n}$个组合后，再进行判断

```go
func generateParenthesis(n int) []string {
	var ans []string
	dfs(&ans, "", n, n)
	return ans
}

func dfs(ans *[]string, cur string, l, r int) {
	if l < 0 || r < l {
        return
    }
    if l == 0 && r == 0 {
        *ans = append(*ans, cur)
    }
    dfs(ans, cur + "(", l-1, r)
    dfs(ans, cur + ")", l, r-1)
}

```

[:star::star::star::star:31. 下一个排列](https://leetcode.cn/problems/next-permutation/)

`trick解法，双指针`

- 从右往左找到第一个nums[i-1] < nums[i]的数
- 从右往左找到第一个恰好大于nums[i-1]的数然后交换
- reverse从i开始的数组

```go
func nextPermutation(nums []int)  {
    // 从后往前找到 nums[i-1] < nums[i]的
    i := len(nums) - 2
    for ;i >= 0 && nums[i] >= nums[i+1];i-- {}
    if i >= 0 {
        // 从右往左开始找第一个大于nums[i]的
        j := len(nums) - 1
        for ;j >= 0 && nums[j] <= nums[i];j-- {}
        // swap
        nums[i], nums[j] = nums[j], nums[i]
    }

    // reverse
    l, r := i+1, len(nums) - 1
    for l < r {
        nums[l], nums[r] = nums[r], nums[l]
        l++
        r--
    }
}
```

[:star::star::star::star:33. 搜索旋转排序数组](https://leetcode.cn/problems/search-in-rotated-sorted-array/)

`分类讨论，中间切开一半一定有一部分有序`

```go
func search(nums []int, target int) int {
    // binary search
    i, j := 0, len(nums) - 1
    for i <= j {
        mid := i + (j-i)/2
        if nums[mid] == target {
            return mid
        } 
        if nums[mid] >= nums[i] { // 左边是升序(这个等于千万不能漏掉)，例如 3 3 2
            if nums[mid] > target && target >= nums[i] {
                j = mid - 1
            } else {
                i = mid + 1
            }
        } else { // 右边是升序
            if nums[mid] < target && target <= nums[j] {
                i = mid + 1
            } else {
                j = mid - 1
            } 
        }
    }
    return -1
}
```

[:star::star::star::star:34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)

```go
func searchRange(nums []int, target int) []int {
    // 两次2分
    f := func(l, r int, leftFlag bool) int {
        ans := len(nums) // 设置成右边界默认值，右边找不到的时候用这个值
        for l <= r {
            mid := l + (r-l)/2
            if (leftFlag && nums[mid] >= target) || nums[mid] > target {
                r = mid-1
                ans = mid
            } else {
                l = mid + 1
            }
        }
        return ans
    }
    l := f(0, len(nums)-1, true)
    r := f(0, len(nums)-1, false)-1
    if l <= r {
        return []int{l, r}
    }
    return []int{-1, -1}
}
```

另一个解法：

```go
// 高手解法，把target+1，都搜索左边界
func searchRange(nums []int, target int) []int {
	left := search(nums, target)
	if left == len(nums) || nums[left] != target {
		return []int{-1, -1}
	}
	right := search(nums, target+1) - 1
	return []int{left, right}
}

func search(nums []int, target int) int {
	left, right := 0, len(nums)-1
	for left <= right {
		mid := left + (right-left)/2
		if nums[mid] < target {
			left = mid + 1
		} else {
			right = mid - 1
		}
	}
	return left
}
```

[:warning:39. 组合总和](https://leetcode.cn/problems/combination-sum/)

> 这道题直接套dfs的模板就行，但是要注意递归中用到的cur slice是一个引用类型，往ans中push的时候需要深拷贝，涉及两个Golang知识点：
>
> - slice是传引用
> - 使用闭包时要先定义函数，L4

```go
func combinationSum(candidates []int, target int) (ans [][]int) {
	// dfs + 剪枝
	sort.Ints(candidates)
    var dfs func(cur []int, start, target int) 
	dfs = func(cur []int, start, target int) {
		if target <= 0 {
			if target == 0 {
				ans = append(ans, append([]int(nil), cur...))
			}
			return
		}

		for i := start; i < len(candidates); i++ {
			cur = append(cur, candidates[i])
			dfs(cur, i, target-candidates[i])
			cur = cur[:len(cur)-1]
		}
	}
	dfs([]int{}, 0, target)
	return ans
}

```

[46. 全排列](https://leetcode.cn/problems/permutations/)

`dfs + 回溯`，可以使用交换的方式节省空间，具体参考官方题解

```go
func permute(nums []int) (ans [][]int) {
    // dfs
    visited := make([]bool, len(nums))
    var dfs func(cur []int, visited []bool)
    dfs = func(cur []int, visited []bool) {
        if len(cur) == len(nums) {
            ans = append(ans, append([]int(nil), cur...))
            return
        }
        for i := 0; i < len(nums); i++ {
            if visited[i] {
                continue
            }
            visited[i] = true
            cur = append(cur, nums[i])
            dfs(cur, visited)
            cur = cur[:len(cur)-1]
            visited[i] = false
        }
    }
    dfs([]int(nil), visited)
    return ans
}
```

