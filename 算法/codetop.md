# codetop按频度排序

## [反转链表](https://leetcode-cn.com/problems/reverse-linked-list/solution/fan-zhuan-lian-biao-by-leetcode/)

```C++
//迭代，硬背就完事了
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* slow = nullptr;
        ListNode* fast = head;
        ListNode* tem = nullptr;//记录fast的下一个节点，要不然就和后面的链表断开了
        while(fast){
            tem = fast -> next;
            fast -> next = slow;//反转fast和slow
            slow = fast;
            fast = tem;//重新赋值fast和slow，但是不要和上一条语句顺序写反
        }
        return slow;
    }
};
//递归
//假设head后面的节点已经反序了
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (!head || !head->next) {
            return head;
        }
        ListNode* newHead = reverseList(head->next);//获取后面的反序的头节点
        head->next->next = head;//反转head和head -> next之间的节点
        head->next = nullptr;//不指向空可能会产生循环
        return newHead;
    }
};

```

## [无重复的最长子串](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

```C++
//滑动窗口
//使用一个哈希表记录窗口中的char的个数
//每次right移动后将right位置的char个数加一，然后滑动窗口，直到s[right]的个数小于等于1为止
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        unordered_map<char, int> mapping;//题解中大多使用的是unordered_set,使用它的insert和erase方法，但是我不习惯
        int result = 0;
        int left = 0;//左窗口
        int right = 0;//右窗口
        int len = s.size();
        for(;right < len; ++ right){
            mapping[s[right]] ++;//将当前右指针指向的char的个数加一
            while(mapping[s[right]] > 1){//当这个char的个数大于1，移动左窗口，直到不大于1
                mapping[s[left]] --;
                left ++;
            }
            result = max(result, right - left + 1);
        }
        return result;
    }
};
```

