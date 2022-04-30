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

## [数组中的第k大元素](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)

```C++
//堆排
class Solution {
public:
    //建立堆函数，把参数中表示下标的参数用一个新变量表示，用来表示当前节点和它的两个子节点中数值最大的节点的下标
    //进行比较谁才是最大的值，，，，注意要保证left 和 right的值小于给定的最右侧边界
    //比较完之后，如果当前节点不是最大值，就交换节点，然后对flag继续调用建堆函数
    void Heapfy(vector<int>& nums, int cur, int len){
        int left = cur * 2 + 1;
        int right = cur * 2 + 2;
        int flag = cur;
        if(left < len && nums[left] > nums[flag]) flag = left;
        if(right < len && nums[right] > nums[flag]) flag = right;
        if(flag != cur){
            swap(nums[cur], nums[flag]);
            Heapfy(nums, flag, len);
        }

    }
    //建立堆，，，就直接从二分之一下标处开始向0循环，每个循环体里面调用建堆函数即可
    void build(vector<int>& nums, int len){
        for(int mid = len / 2; mid >= 0; -- mid){
            Heapfy(nums, mid, len);
        }
    }
    int findKthLargest(vector<int>& nums, int k) {
        int len = nums.size();
        build(nums, len);
        for(int i = 0; i < k - 1; ++ i){
            swap(nums[0], nums[len - i - 1]);
            Heapfy(nums, 0, len - i - 2);
        }
        return nums[0];
    }
};
//快排
//可以就先写个快排，然后直接在quicksort里面修改就好，类似二分的那种修改
class Solution {
public:
    int quick(vector<int>& nums, int left, int right){
        int flag = nums[right];
        int p = left;
        for(int i = left; i <= right; ++ i){
            if(nums[i] > flag){
                swap(nums[p ++], nums[i]);
            }
        }
        swap(nums[right], nums[p]);
        return p;
    }
    int quicksort(vector<int>& nums, int left, int right, int k){
        if(left > right) return -1;//如果返回的是-1，可以看看这里
        int mid = quick(nums, left, right);
        if(mid == k - 1) return nums[mid];
        else if(mid > k - 1){
            return quicksort(nums, left, mid - 1, k);
        }
        else{
            return quicksort(nums, mid + 1, right, k);
        }
        return -1;
        //quicksort(nums, left, mid - 1);
        //quicksort(nums, mid + 1, right);
    }
    int findKthLargest(vector<int>& nums, int k) {
        int len = nums.size() - 1;
        return quicksort(nums, 0, len, k);
        //for(auto i : nums) cout << i << ' ';
        //cout << endl;
        //return 0;
    }
};
```

## [K个一组反转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/submissions/)

```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    pair<ListNode*, ListNode*> rev(ListNode* head, ListNode* tail){
        ListNode* cur = tail -> next;/////////！！！！！及其容易出错，，，一定要记录下来tail后面的节点，用于while判断
        ListNode* prev = head;
        ListNode* behi = nullptr;
        ListNode* tem = nullptr;
        while(prev != cur){//就是这里，既不能写成prev，也不能写成prev != tail，后者会造成死循环
            tem = prev -> next;
            prev -> next = behi;
            behi = prev;
            prev = tem;
        }
        return {head, tail};
    }
    ListNode* reverseKGroup(ListNode* head, int k) {
        ListNode* dummy = new ListNode(0, head);//创建哨兵节点
        ListNode* tail = dummy;
        ListNode* prv = dummy;
        ListNode* nxt = dummy;
        //需要使用四个节点，prev指向需要反转的段的前一个，nxt指向后一个，，head是需要反转的起始节点，tail是需要反转的末尾节点
        while(tail){
            for(int i = 0; i < k; ++ i){
                tail = tail -> next;
                if(!tail){
                    return dummy -> next;//判断到后面的话直接返回，当然视题目而定
                }
            }
            nxt = tail -> next;//保证调用函数之前四个节点都指向正确的位置
            pair<ListNode*, ListNode*> res = rev(head, tail);
            //将反转后的链表段重新加入
            prv -> next = res.second;
            res.first -> next = nxt;
            //重新设置节点
            prv = res.first;
            head = nxt;
            tail = prv;
        }
        return dummy -> next;
    }
};
```

## [三数之和](https://leetcode-cn.com/problems/3sum/)

```C++
//排序 + 双指针  。。。。。。。。。没有用到哈希！！！
//比较麻烦的就是下面（1）（2）（3）中要跳过重复元素
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        vector<vector<int>> result;
        int len = nums.size();
        for(int i = 0; i < len; ++ i){
            if(i != 0 && nums[i] == nums[i - 1]) continue ;//（1）
            int left = i + 1;
            int right = len - 1;
            int cur = left;//这里要设置一个cur，不能直接在（2）中判断nums[left] == nums[left - 1]，因为其实第一个元素和第二个可以重复
            while(left < right){
                if(left != cur && nums[left] == nums[left - 1]) {//（2）
                    left ++;
                    continue ;
                }
                if(right != len - 1 && nums[right] == nums[right + 1]) {//（3）
                    right --;
                    continue ;
                }
                if(nums[left] + nums[right] + nums[i] == 0) {
                    result.push_back({nums[i], nums[left], nums[right]});
                    left ++;
                    right --;
                }
                else if(nums[left] + nums[right] + nums[i] > 0) right --;
                else left ++;
            }
        }
        return result;
    }
};
```

## [手撕快排&手撕归并](https://leetcode-cn.com/problems/sort-an-array/)

```C++
//手撕快排
class Solution {
public:
    int Quick(vector<int>& nums, int left, int right){
        //下面两句是加入随机取值，因为力扣上有个测试用例使用常规快排会超市超时，是故意设计的升序数组，所以常规排序是最坏时间复杂度O(n2)
        int tmp = left + rand() % (right - left + 1);
        swap(nums[tmp], nums[right]);
        
        int cur = nums[right];
        int p = left;
        for(int i = left; i < right; ++ i){//这里一定是小于right，不能等于，否则出问题
            if(nums[i] < cur){
                swap(nums[i], nums[p ++]);
            }
        }
        swap(nums[right], nums[p]);
        return p;
    }
    void quicksort(vector<int>& nums, int left, int right){
        if(left >= right) return ;
        int mid = Quick(nums, left, right);
        quicksort(nums, left, mid - 1);
        quicksort(nums, mid + 1, right);
    }
    vector<int> sortArray(vector<int>& nums) {
        int len = nums.size() - 1;
        quicksort(nums, 0, len);
        return nums;
    }
};
//手撕归并
class Solution {
public:
    void merge(vector<int>& nums, int left, int mid, int right){
        vector<int> tmp;
        int cur1 = left, cur2 = mid + 1;//cur2从mid + 1开始
        while(cur1 <= mid && cur2 <= right){
            if(nums[cur1] > nums[cur2]){
                tmp.emplace_back(nums[cur2]);
                ++ cur2;
            }
            else{
                tmp.emplace_back(nums[cur1]);
                ++ cur1;
            }
        }
        while(cur1 <= mid){
            tmp.emplace_back(nums[cur1]);
            ++ cur1;
        }
        while(cur2 <= right){
            tmp.emplace_back(nums[cur2]);
            ++ cur2;
        }
        int len = tmp.size();
        for(int i = 0; i < len; ++ i){//注意最后把数组赋值这块
            nums[left + i] = tmp[i];
        }
    }
    void mergesort(vector<int>& nums, int left, int right){
        if(left >= right) return ;//大于等于
        int mid = left + ((right - left) >> 1);
        mergesort(nums, left, mid);
        mergesort(nums, mid + 1, right);
        merge(nums, left, mid, right);
    }
    vector<int> sortArray(vector<int>& nums) {
        mergesort(nums, 0, nums.size() - 1);
        return nums;
    }
};
```

##  [最大子数组和](https://leetcode-cn.com/problems/maximum-subarray/)

```C++
//dp动态规划 递推公式 dp[i] = max(nums[i], dp[i - 1] + nums[i]);,,,,,就是看一线dp[i - 1] + nums[i]之后和nums[i]本身比较谁更大
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int len = nums.size();
        vector<int> dp(len, 0);
        dp[0] = nums[0];
        for(int i = 1; i < len; ++ i) dp[i] = max(nums[i], dp[i - 1] + nums[i]);
        return *max_element(dp.begin(), dp.end());
    }
};
```

## [合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

```C++
//直接使用归并排序中的方法进行合并即可，，，更进阶的是合并k个有序链表，就使用类似于归并排序的方法
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        ListNode* dummy = new ListNode();
        ListNode* cur = dummy;
        while(list1 && list2){
            if(list1 -> val > list2 -> val){
                cur -> next = list2;
                list2 = list2 -> next;
            }
            else{
                cur -> next = list1;
                list1 = list1 -> next;
            }
            cur = cur -> next;
        }
        /*
        //下面这个没必要，因为是链表排序，所以直接把剩下的挂载到cur的结尾就好
        //可是不知道怎么回事，下面的代码比后面的执行用时要少，按理来说应该要多才对啊
        while(list2){
            cur -> next = list2;
            list2 = list2 -> next;
            cur = cur -> next;
        }
        while(list1){
            cur -> next = list1;
            list1 = list1 -> next;
            cur = cur -> next;
        }
        */
        if(list1) cur -> next =  list1;
        if(list2) cur -> next =  list2;
        return dummy -> next;
    }
};
```

## [两数之和](https://leetcode-cn.com/problems/two-sum/)

```C++
//哈希，减小空间复杂度，成为O(n)
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> mapping;
        int len = nums.size();
        for(int i = 0; i < len; ++ i){
            if(mapping.find(target - nums[i]) != mapping.end()){
                return {i, mapping[target - nums[i]]};
            }
            mapping[nums[i]] = i;
        }
        return {};
    }
};
```

## [二叉树的层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

```C++
//使用队列存储节点
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> res;
        queue<TreeNode*> que;
        TreeNode* tmp;
        if(!root) return res;
        que.push(root);
        //res.push_back({root -> val});
        while(!que.empty()){
            vector<int> vec;
            for(int i = que.size(); i > 0; -- i){//遍历当前队列中的所有节点，即为遍历同层的节点
                tmp = que.front();
                que.pop();
                vec.push_back(tmp -> val);
                if(tmp -> left) que.push(tmp -> left);
                if(tmp -> right) que.push(tmp -> right);
            }
            res.push_back(vec);
        }
        return res;
    }
};
```

