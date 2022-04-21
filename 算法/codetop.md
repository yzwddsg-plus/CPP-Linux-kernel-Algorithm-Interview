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

## [LRU缓存](https://leetcode-cn.com/problems/lru-cache/)

```C++
//map是缓存，用于查找，因为查找快，，，，双向链表中的节点存缓存数据，，，，，链表节点需要自己定义实现
struct ListNode1{
    int key;
    int val;
    ListNode1* prev;
    ListNode1* next;
    ListNode1() : key(0), val(0), prev(nullptr), next(nullptr) {};
    ListNode1(int k, int v) : key(k), val(v), prev(nullptr), next(nullptr) {};
};//注意构造函数的形式和类差不多，，，，，，，，别忘了定义完结构体后有一个分号
class LRUCache {
public:
    LRUCache(int capacity) {
        max_size = capacity;//如果之前定义的容量的变量名为capacity，这个语句应该改为 this -> capacity = capacity;
        head = new ListNode1();
        tail = new ListNode1();
        head -> next = tail;
        tail -> prev = head;
    }
    
    int get(int key) {
        if(mapping.find(key) == mapping.end()){
            return -1;
        }
        else{
            ListNode1* tmp = mapping[key];
            //把tmp从原来位置上取出
            tmp -> prev -> next = tmp -> next;
            tmp -> next -> prev = tmp -> prev;
            //设置tmp的prev和next
            tmp -> next = head -> next;
            tmp -> prev = head;
            //插入head后面
            head -> next -> prev = tmp;
            head -> next = tmp;
            return tmp -> val;
        }
    }
    
    void put(int key, int value) {
        if(mapping.find(key) != mapping.end()){
            ListNode1* tmp = mapping[key];
            tmp -> prev -> next = tmp -> next;
            tmp -> next -> prev = tmp -> prev;

            tmp -> next = head -> next;
            tmp -> prev = head;

            head -> next -> prev = tmp;
            head -> next = tmp;
            tmp -> val = value;
        }
        else{
            if(mapping.size() < max_size){
                ListNode1* tmp = new ListNode1(key, value);
                mapping[key] = tmp;

                tmp -> prev = head;
                tmp -> next = head -> next;

                head -> next -> prev = tmp;
                head -> next = tmp;
            }
            else{
                mapping.erase(tail -> prev -> key);
                ListNode1* dis = tail -> prev;
                tail -> prev = dis -> prev;
                dis -> prev -> next = tail;
                delete dis;

                ListNode1* tmp = new ListNode1(key, value);
                mapping[key] = tmp;
                tmp -> prev = head;
                tmp -> next = head -> next;

                head -> next -> prev = tmp;
                head -> next = tmp;
            }
        }
    }
private:
    int max_size;//容量
    unordered_map<int, ListNode1*> mapping;//get put 平均复杂度O(1)关键所在，通过一个哈希表判断，不用遍历双向链表
    ListNode1* head;//先设置头尾哨兵，后面可以避免很多麻烦的操作
    ListNode1* tail;
};
```

