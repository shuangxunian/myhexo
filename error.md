<!--
 * @Author: yuelin.wang
 * @Date: 2021-10-19 14:17:45
 * @LastEditors: yuelin.wang
 * @LastEditTime: 2021-10-21 14:14:00
 * @Description: 
-->
想=通知 B页面

为了解决这种麻烦，产生了vuex。但是我们肯定希望组件的高可复用性，

友链：
  极客生活
  程
  粟米
  驯鹿
  sknp
  lc
  corsunchen
  henry



排序算法（Sorting algorithm）是计算机科学最古老、最基本的课题之一。要想成为合格的程序员，就必须理解和掌握各种排序算法。

目前，最常见的排序算法大概有七八种，其中"快速排序"（Quicksort）使用得最广泛，速度也较快。它是图灵奖得主C. A. R. Hoare（1934--）于1960时提出来的。

"快速排序"的思想很简单，整个排序过程只需要三步：
1. 在数据集之中，选择一个元素作为"基准"（pivot）。
2. 所有小于"基准"的元素，都移到"基准"的左边；所有大于"基准"的元素，都移到"基准"的右边。
3. 对"基准"左边和右边的两个子集，不断重复第一步和第二步，直到所有子集只剩下一个元素为止。

举例来说，现在有一个数据集{85, 24, 63, 45, 17, 31, 96, 50}，怎么对其排序呢？

第一步，选择中间的元素45作为"基准"。（基准值可以任意选择，但是选择中间的值比较容易理解。

![](https://api2.mubu.com/v3/document_image/83533ad9-f39d-46f4-8145-309bb681e3fa-3807603.jpg)

第二步，按照顺序，将每个元素与"基准"进行比较，形成两个子集，一个"小于45"，另一个"大于等于45"。

![](https://api2.mubu.com/v3/document_image/06f26b2e-5fb6-42e6-ac12-850e5b48da95-3807603.jpg)

第三步，对两个子集不断重复第一步和第二步，直到所有子集只剩下一个元素为止。

![](https://api2.mubu.com/v3/document_image/086fa999-563b-480e-bfd5-60a7a8ad6122-3807603.jpg)

![](https://api2.mubu.com/v3/document_image/456ad4c9-1256-47d8-aff6-8676e986cef9-3807603.jpg)

![](https://api2.mubu.com/v3/document_image/69e94e68-23a4-432a-8c0b-49bb0948043e-3807603.jpg)

![](https://api2.mubu.com/v3/document_image/654e39a0-7522-4252-ac7e-5524da0719a4-3807603.jpg)

```javascript
let quickSort = function(arr) {
　　if (arr.length <= 1) { return arr; }
　　let pivotIndex = Math.floor(arr.length / 2);
　　let pivot = arr.splice(pivotIndex, 1)[0];
　　let left = [];
　　let right = [];
　　for (let i = 0; i < arr.length; i++){
　　　　if (arr[i] < pivot) {
　　　　　　left.push(arr[i]);
　　　　} else {
　　　　　　right.push(arr[i]);
　　　　}
　　}
　　return quickSort(left).concat([pivot], quickSort(right));
};
```