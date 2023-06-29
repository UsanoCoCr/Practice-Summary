# 目录
- [目录](#目录)
- [1146. 快照数组](#1146-快照数组)

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

