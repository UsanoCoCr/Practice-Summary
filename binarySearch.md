# 目录
- [目录](#目录)
- [1146. 快照数组](#1146-快照数组)
- [154. 寻找旋转排序数组中的最小值 II](#154-寻找旋转排序数组中的最小值-ii)
- [4. 寻找两个正序数组的中位数](#4-寻找两个正序数组的中位数)

# 1146. 快照数组

实现支持下列接口的「快照数组」- SnapshotArray：

SnapshotArray(int length) - 初始化一个与指定长度相等的 类数组 的数据结构。初始时，每个元素都等于 0。
void set(index, val) - 会将指定索引 index 处的元素设置为 val。
int snap() - 获取该数组的快照，并返回快照的编号 snap_id（快照号是调用 snap() 的总次数减去 1）。
int get(index, snap_id) - 根据指定的 snap_id 选择快照，并返回该快照指定索引 index 的值。

**技巧：** 使用algorithm库中的upper_bound函数，可以在log(n)的时间内找到第一个大于某个数的位置；使用lower_bound函数，可以在log(n)的时间内找到第一个大于等于某个数的位置。

<details>
<summary>快照数组</summary>

```c++
class SnapshotArray {
public:
    map<int, map<int, int>> historySnap;
    int currentSnap;
    SnapshotArray(int length) {
        currentSnap = 0;
    }
    
    void set(int index, int val) {
        historySnap[index][currentSnap] = val;
    }
    
    int snap() {
        return currentSnap++;
    }
    
    int get(int index, int snap_id) {
        auto it = historySnap[index].upper_bound(snap_id);
        if(it == historySnap[index].begin()) {
            return 0;
        }
        else {
            return (--it)->second;
        }
    }
};

/**
 * Your SnapshotArray object will be instantiated and called as such:
 * SnapshotArray* obj = new SnapshotArray(length);
 * obj->set(index,val);
 * int param_2 = obj->snap();
 * int param_3 = obj->get(index,snap_id);
 */
```
</details>

# 154. 寻找旋转排序数组中的最小值 II
已知一个长度为 n 的数组，预先按照升序排列，经由 1 到 n 次 旋转 后，得到输入数组。例如，原数组 nums = [0,1,4,4,5,6,7] 在变化后可能得到：
若旋转 4 次，则可以得到 [4,5,6,7,0,1,4]
若旋转 7 次，则可以得到 [0,1,4,4,5,6,7]
注意，数组 [a[0], a[1], a[2], ..., a[n-1]] 旋转一次 的结果为数组 [a[n-1], a[0], a[1], a[2], ..., a[n-2]] 。

给你一个可能存在 重复 元素值的数组 nums ，它原来是一个升序排列的数组，并按上述情形进行了多次旋转。请你找出并返回数组中的 最小元素 。

你必须尽可能减少整个过程的操作步骤。

**技巧：** 在相等的时候right--即可，这样复杂度计算出来还是log(n)。

<details>
<summary>寻找旋转排序数组中的最小值 II</summary>

```c++
class Solution {
public:
    int findMin(vector<int>& nums) {
        int start = findStart(nums, 0, nums.size()-1);
        //cout<<start;
        return nums[start];
    }
    int findStart(vector<int>& nums, int left, int right){
        int mid = (left + right) / 2;
        if(left == right){
            if(left == 0)
                return 0;
            if(nums[left-1] > nums[left])
                return left;
            if(nums[left-1] < nums[left])
                return left - 1;
            return left + 1;
        }
        if(nums[mid] > nums[right])
            return findStart(nums, mid+1, right);
        else if (nums[mid] < nums[right])
            return findStart(nums, left, mid);
        else{
            return findStart(nums, left, right-1);
        }
    }
};
```
</details>

# 4. 寻找两个正序数组的中位数
给定两个大小分别为 m 和 n 的正序（从小到大）数组 nums1 和 nums2。请你找出并返回这两个正序数组的 中位数 。

算法的时间复杂度应该为 O(log (m+n)) 

**技巧：** 二分查找，每次排除掉k/2个数，然后递归查找。

<details>
<summary>寻找两个正序数组的中位数</summary>

```c++
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int n = nums1.size();
        int m = nums2.size();
        int left = (n + m + 1) / 2;
        int right = (n + m + 2) / 2;
        return (getKth(nums1, 0, n - 1, nums2, 0, m - 1, left) + getKth(nums1, 0, n - 1, nums2, 0, m - 1, right)) * 0.5;  
    }  
    int getKth(vector<int>& nums1, int start1, int end1, vector<int>& nums2, int start2, int end2, int k) {
        int len1 = end1 - start1 + 1;
        int len2 = end2 - start2 + 1;
        //让 len1 的长度小于 len2，这样就能保证如果有数组空了，一定是 len1 
        if (len1 > len2) return getKth(nums2, start2, end2, nums1, start1, end1, k);
        if (len1 == 0) return nums2[start2 + k - 1];
        if (k == 1) return min(nums1[start1], nums2[start2]);
        int i = start1 + min(len1, k / 2) - 1;
        int j = start2 + min(len2, k / 2) - 1;
        if (nums1[i] > nums2[j]) {
            return getKth(nums1, start1, end1, nums2, j + 1, end2, k - (j - start2 + 1));
        }
        else {
            return getKth(nums1, i + 1, end1, nums2, start2, end2, k - (i - start1 + 1));
        }
    }
};
```
</details>