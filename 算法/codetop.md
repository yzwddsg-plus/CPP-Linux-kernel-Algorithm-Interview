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

