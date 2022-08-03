# Merge Sort 归并排序
归并排序, 将 arr 左右破开两半, 直到破到只剩 1 element, 再集合到一起, 集合的时候 进行sort 

思想是 recursion, 常用场景, 对 **多组** 已经 **sorted arr / list** 进行 排序

# 经典例题&模板
88. Merge Sorted Array: https://leetcode.com/problems/merge-sorted-array/

mergeBreak 是 mergesort的入口 也是 一破两半函数, n 是当前的 arr 的长度, 找到中间点 mid 声明两个新的数组装 left 和 right 的值, 把 arr 的值放到对应的 左右数组中, 然后接着破左右, base情况: 当长度为1时, 不破了. 最后调用 mergeTogether 去实现拼接

mergeTogether 是 合并 2 sorted array 的实现, 三指针即可

```java
private void mergeBreak(int[] arr, int n){
    if(n < 2)   return;
    int mid = n / 2;
    int[] left = new int[mid];
    int[] right = new int[n-mid];
    for(int i = 0; i < mid; i++)    left[i] = arr[i];
    for(int i = 0; i < n - mid; i++)right[i] = arr[i+mid];
    mergeBreak(left, mid);
    mergeBreak(right, n - mid);
    mergeTogether(arr, left, right, mid, n-mid);
}

private void mergeTogether(int[] arr, int[] left, int[] right, int l, int r){
    int i = 0, j = 0, k = 0;
    while(i < l && j < r){
        if(left[i] > right[j]){
            arr[k++] = right[j++];
        }else{
            arr[k++] = left[i++];
        }
    }
    while(i < l) arr[k++] = left[i++];
    while(j < r) arr[k++] = right[j++];
}
```

# Problems

Merge k Sorted Lists: https://leetcode.com/problems/merge-k-sorted-lists/


HARD实现: 两点: 

1. 使用 merge sort 的思想, 对 k 个list进行 split, 直到最后成为 两个list 为一组, 再归并返回. **注意**: 在 split 的时候, 如果抵达 one list 则 返回 list[left] 作为 recursion 的 base

2. 复用之前的 merge2list 源代码

```java
public ListNode mergeKLists(ListNode[] lists) {
    if(lists == null || lists.length == 0)  return null;
    return mergeK(lists, 0, lists.length - 1);
}

private ListNode mergeK(ListNode[] lists, int left, int right){
    if(right > left){
        int mid = left + (right - left) / 2;
        return mergeTwo(mergeK(lists, left, mid), mergeK(lists, mid+1, right));
    }
    return lists[left];
}


private ListNode mergeTwo(ListNode list1, ListNode list2){
    ListNode head = new ListNode();
    ListNode dummy = head;
    while(list1 != null && list2 != null){
        if(list1.val > list2.val){
            head.next = list2;
            head = head.next;
            list2 = list2.next;
        }else{
            head.next = list1;
            head = head.next;
            list1 = list1.next;
        }
    }
    if(list1 != null){
        head.next = list1;
    }
    if(list2 != null){
        head.next = list2;
    }
    return dummy.next;
}
```


## 315. Count of Smaller Numbers After Self
https://leetcode.com/problems/count-of-smaller-numbers-after-self/

```java


```