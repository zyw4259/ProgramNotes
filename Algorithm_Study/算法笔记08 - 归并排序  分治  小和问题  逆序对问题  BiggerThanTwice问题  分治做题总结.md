[TOC]



## 1. 归并排序

### 1.1 归并排序简介

- **分 (Divide)：** 不直接处理整个长数组，而是把它对半再对半，直到你手上只有一堆堆单个元素的数组。一个元素的数组，天然就是排好序的！

- **治 (Conquer)：** 既然单个元素的数组已经有序，这一步其实就是完成了。

- **合 (Merge)：** 现在开始最关键的一步：把那些排好序的小数组两两合并成更大的、仍然有序的数组。这个合并过程是归并排序的精髓。

无论数据初始状态如何，归并排序总是在 O(nlogn) 的时间完成。这意味着它的性能非常稳定和优秀。



### 1.2 归并排序递归实现

大体思路是：将数组左半部分归并排序，将数组右边部分归并排序，再将有序的两侧合并成一个更大的有序数组

1：先处理边界情况

2：标记数组中点，以mid划分左右，分别先将左右排好序

3：最后将排好序的左右部分合并，得到整个有序的数组

```java
    // 归并排序的递归实现
    public static void mergeSort(int[] arr, int l, int r) {
        // 1
        if (arr == null || arr.length < 2) {
            return;
        }
        if (l == r) {
            return;
        }
		// 2
        int mid = l + ((r - l) >> 1);
        mergeSort(arr, l ,mid);
        mergeSort(arr, mid + 1, r);
        // 3
        merge(arr, l, mid, r);

    }
```

现在只缺合并的部分没有实现，合并部分的思路如下：

我们采用`merge()`方法

```
比较 & 填充 (谁小移谁)

   arr: [1, 5, 8] vs [2, 4, 9]  --->  help: [1, 2, 4, 5] ...
         ↑            ↑                     (p1, p2 依次移动)
   (循环此过程，直到一边处理完毕)
```

1：定义p1、p2两个指针，分别标记左边部分的头部和右边部分的起始位置，同时创建一个help数组来帮助我们排序

2：进入循环，当两个指针有一个越界了，即左右有一个部分的数处理完毕即跳出循环。用两个指针依次比较左右两边的数，小的那个加入help数组，并将指针向后移，处理完一个部分的排序

3：经过上一步处理，还有一个部分的指针没越界，所以还差一部分数没有进入加入help数组，我们将其加入

4：此时help数组就是排好序的arr，我们将help的东西复制到arr

```java
    public static void merge(int[] arr, int l, int mid, int r) {
        // 1
        int p1 = l;
        int p2 = mid + 1;
        int idx = 0;
        int[] help = new int[r - l + 1];
        
		// 2
        while (p1 <= mid && p2 <= r) {
            if (arr[p1] <= arr[p2]) {
                help[idx++] = arr[p1++];
            } else {
                help[idx++] = arr[p2++];
            }
        }
		
        // 3
        while (p1 <= mid) {
            help[idx++] = arr[p1++];
        }
        while (p2 <= r) {
            help[idx++] = arr[p2++];
        }
		
        // 4
        System.arraycopy(help, 0, arr, l, help.length);
    }
```

​	

### 1.3 归并排序的迭代实现

大体思路：我们初始设置一个**步长**来代表排序的左右组长度，初始为1，这在逻辑上将这个数组分为了很多子组，每个子组排序好其中的左右组再合并，然后将步长x2，直到步长比数组大为止

1：步长从1开始归并

2：大循环，让步长增到比数组大为止，也就是直到整个数组最终合并成一个有序的数组

3：用l记录左组的第一个位置（因为左组第一个位置会变动，每个步长要处理数组划分的每个子组，所以l就代表当前处理的子组的左组开头）

4：当前步长执行的归并循环，依次处理各个子组，当左组开头超出数组就结束（意味着每个子组都处理完毕）

5：如果左组已经没办法取完整了，即当前左组开头加上步长已经越界了，就说明根本就没有右组了，此次步长的归并就应该结束了

6：找到当前子组的中间（划分左右组），找到右组的处理结束标记`r`，右组不一定要完整等于步长，可以取不满，取不满就直接取到数组结尾

7：表示将当前子组合并（当前子组的左右组已经有序，只差合并让整个子组有序）

8：移动，处理下一个子组

9：我们必须注意，不能先将step * 2，因为很可能会越界，所以先判断

```java
    public static void mergeSortIter(int[] arr) {
        if (arr == null || arr.length < 2) {
            return;
        }
        
		// 1
        int step = 1;
        
        // 2
        while (step < arr.length) {
            // 3
            int l = 0;

            // 4
            while (l < arr.length - 1) {
                // 5
                if (step >= arr.length - l) {
                    break;
                }
                // 6
                int mid = l + step - 1;
                int r = (mid + step) < arr.length ? mid + step : arr.length - 1;
                // 7
                merge(arr, l, mid, r);
                // 8
                l = r + 1;
            }
            // 9
            if (step > arr.length / 2) {
                break;
            }
            step <<= 1;
        }
    }
```

示例：

```
+----------------------------------------------------------------------+
| 核心: 从长度为1的子数组开始，两两合并，直到整个数组有序。                     |
|                                                                      |
| 初始数组: [8, 3, 6, 4, 9, 2, 7, 5]                                    |
|                                                                      |
| 步长 = 1                                                              |
|   - [8] 和 [3] -> [3, 8]                                              |
|   - [6] 和 [4] -> [4, 6]                                             |
|   - [9] 和 [2] -> [2, 9]                                             |
|   - [7] 和 [5] -> [5, 7]                                             |
|   结果: [3, 8, 4, 6, 2, 9, 5, 7]                                     |
|                                                                      |
| 步长 = 2 (步长 x 2)                                                  |
|   - [3, 8] 和 [4, 6] -> [3, 4, 6, 8]                                 |
|   - [2, 9] 和 [5, 7] -> [2, 5, 7, 9]                                 |
|   结果: [3, 4, 6, 8, 2, 5, 7, 9]                                     |
|                                                                      |
| 步长 = 4 (步长 x 2)                                                  |
|   - [3, 4, 6, 8] 和 [2, 5, 7, 9] -> [2, 3, 4, 5, 6, 7, 8, 9]          |
|   结果: [2, 3, 4, 5, 6, 7, 8, 9]                                     |
|                                                                      |
| 步长 = 8 (步长 x 2)                                                  |
|   - 步长不小于数组长度，排序完成。                                   |
|                                                                      |
| 最终结果: [2, 3, 4, 5, 6, 7, 8, 9]                                   |
+----------------------------------------------------------------------+
```

​	

## 2. 相关题目

### 2.1 小和问题

**要求**：在一个数组中，一个数左边比它小的数的总和，叫该数的小和，求数组中所有数的小和累加起来的值



**大体思路**：

① 这个问题可以转化为：对于数组中的每一个数，求出它右边有多少个数比它大，然后将这个数与该数量相乘，最后将所有结果累加。

将数组从中间一分为二，递归地对左半部分和右半部分进行同样的操作。

而最小的单元中，左组右组都只有一个数，天然就是有序的，而在merge后整体变得有序，可以和另外一个部分比较且计数了



② **这是算法最关键的一步。**我们处理两个已经排好序的子数组（我们称之为“左组”和“右组”），将它们合并成一个大的有序数组，并在此过程中计算“小和”。

准备两个指针，`p1` 指向左组的起始位置，`p2` 指向右组的起始位置。

比较 `p1` 和 `p2` 指向的数：

- 情况一：`左组[p1] < 右组[p2]`

​	这时，我们发现了一个“小和”产生的机会。因为左右两组内部已经是有序的，所以当 `左组[p1]` 小于 `右组[p2]` 时，它必然也小于 `右组[p2]` 之后的所有数。

​	右组中从 `p2` 到结尾，一共有 `(右组长度 - p2索引)` 个数都比 `左组[p1]` 大。

​	累加小和： 因此，`左组[p1]` 产生的“小和”为 `左组[p1] * (右组剩余数的数量)`。我们将这个值累加到全局的小和总数中。

​	将 `左组[p1]` 放入辅助数组，然后 `p1` 指针后移一位。

- 情况二：`左组[p1] >= 右组[p2]`

​	这种情况下，`左组[p1]` 并不比 `右组[p2]` 小，因此它不能为 `右组[p2]` 贡献小和。

​	我们只将 `右组[p2]` 放入辅助数组，然后 `p2` 指针后移一位。

随着递归的逐层返回，每一层合并产生的小和都会被累加，最终得到整个数组的小和总和。



**代码演示**：

```java
    public static int getSmallSum(int[] arr) {

        if (arr == null || arr.length < 2) {
            return 0;
        }

        return process(arr, 0, arr.length - 1);
    }

    // 作用：求[l, r]范围的小和
    private static int process(int[] arr, int l, int r) {

        if (l == r) {
            return 0;
        }
        int mid = l + ((r - l) >> 1);

        return
                process(arr, l, mid)    // 求得左组的小和
                +
                process(arr, mid + 1, r)    // 求得右组的小和
                +
                mergeSmallSum(arr, l, mid, r);  // 求得左右组有序的前提下，求得总的小和

    }

    // 作用：获得该范围的小和，并排序好[l, r]的数
    private static int mergeSmallSum(int[] arr, int l, int mid, int r) {

        int[] help = new int[r - l + 1];
        int p1 = l;
        int p2 = mid + 1;
        int idx = 0;
        int rst = 0;

        while (p1 <= mid && p2 <= r) {
            if (arr[p1] < arr[p2]) {
                rst += arr[p1] * (r - p2 + 1);	// 小和计算
                help[idx++] = arr[p1++];
            } else {
                help[idx++] = arr[p2++];
            }
        }

        while (p1 <= mid) {
            help[idx++] = arr[p1++];
        }
        while (p2 <= r) {
            help[idx++] = arr[p2++];
        }

        for(int i = 0; i < help.length; i++) {
            arr[l + i] = help[i];
        }

        return rst;
    }
```

**对数器：**

```java
    // 对数器验证
    public static int[] arrRam(int maxVal, int maxLen) {
        int max = (int) (Math.random() * maxVal) + 1;
        int len = (int) (Math.random() * maxLen) + 1;

        int[] arr = new int[len];
        for (int i = 0; i < arr.length; i++) {
            arr[i] = (int) (Math.random() * max) + 1;
        }
        return arr;
    }

    public static int comparator(int[] arr) {
        if (arr == null || arr.length < 2) {
            return 0;
        }
        int rst = 0;
        for (int i = 1; i < arr.length; i++) {
            for (int j = 0; j < i; j++) {
                if (arr[j] < arr[i]) {
                    rst += arr[j];
                }
            }
        }
        return rst;
    }

    public static void main(String[] args) {
        int maxVal = 100;
        int maxLen = 100;
        int testTimes = 100000;

        for (int i = 0; i < testTimes; i++) {
            int[] arr = arrRam(maxVal, maxLen);
            int[] arr2 = arr.clone();

            if (getSmallSum(arr) != comparator(arr2)) {
                System.out.println("Error");
            }
        }
        System.out.println("Finish");
    }
```

​	

### 2.2 逆序对问题

要求：求统计数组中，所有左边比右边大的数对一共有多少对。

例如：[2, 4, 1, 5] 就有[2, 1]、[4, 1]两对

大体思路：

这样的问题同样考虑归并排序的思路，因为逆序对的产生和计算同样可以完美融入到merge这一步！

和小和问题的思路类似

① 我们首先将数组从中间一分为二，将问题分解成两个子问题：

​	计算左边一半数组的逆序对数量。

​	计算右边一半数组的逆序对数量。

通过递归的方式，不断重复分解过程，直到子数组的长度为 1。长度为 1 的数组，其内部逆序对数量显然为 0。

② 合并：当我们计算完左右组的逆序对后，左右组分别已经有序了，此时我们需要合并让左右组有序，并在这个过程中计算当前整组的逆序对

这种逆序对的特点是：一个数字在左边的子数组，另一个数字在右边的子数组，并且左边的数字大于右边的数字。

我们使用两个指针p1, p2，分别指向左、右两个子数组的起始位置。

- 如果 `左指针的数字` <= `右指针的数字`，说明这个左边的数字比右边所有还没处理的数字都要小，它无法与右边的任何数字构成逆序对。我们将这个较小的左边数字放入help数组，并将左指针向右移动一位。

- 如果 `左指针的数字` > `右指针的数字`，因为左边子数组是有序的，所以当前左指针所指的数字，以及它后面所有的数字，都比当前右指针的数字要大。因此，它们都能和当前右指针的数字构成逆序对。**逆序对的数量就是左子数组剩余元素的个数。**我们将这个数量累加到总结果中。



**我的代码：**

```java
    public static int getInversePair(int arr[]) {
        if (arr == null || arr.length < 2) {
            return 0;
        }

        return process(arr, 0, arr.length - 1);
    }

    private static int process(int[] arr, int l, int r) {

        if (l == r) {
            return 0;
        }
        int mid = l + ((r - l) >> 1);

        return
                process(arr, l, mid)
                +
                process(arr, mid + 1, r)
                +
                mergeInvPair(arr, l, mid, r);

    }

    private static int mergeInvPair(int[] arr, int l, int mid, int r) {

        int[] help = new int[r - l + 1];
        int p1 = l;
        int p2 = mid + 1;
        int idx = 0;
        int rst = 0;    // 多少对

        while (p1 <= mid && p2 <= r) {
            if (arr[p1] <= arr[p2]) {
                help[idx++] = arr[p1++];
            } else {
                rst += mid - p1 + 1;
                help[idx++] = arr[p2++];
            }
        }

        while (p1 <= mid) {
            help[idx++] = arr[p1++];
        }
        while (p2 <= r) {
            help[idx++] = arr[p2++];
        }

        for(int i = 0; i < help.length; i++) {
            arr[l + i] = help[i];
        }

        return rst;

    }
```

​	

### 2.3 BiggerThanTwice问题

要求：统计数组中，所有左边的数大于右边数的两倍的数对一共有多少对。

大体思路：

参考以上两个问题的思路，这里只说在合并过程的处理

在merge之前左右两个组已经有序，开始merge附带计数：

我们使用两个指针，`p1` 指向左半部分（`left`）的起始位置，`p2` 指向右半部分（`right`）的起始位置。

对于 `p1` 指向的当前元素 `arr[p1]`，我们移动 `p2`，直到找到第一个不满足 `arr[p1] > 2 * arr[p2]` 的位置。

此时，右半部分在 `p2` 指针之前的所有元素，都满足 `arr[p1]` 大于它们的两倍。我们可以直接将这个数量累加到结果中。

接着，我们将 `p1` 指针后移一位，继续重复上述过程，直到 `p1` 遍历完整个左半部分。

由于两个子数组都是有序的，`p2` 指针不需要每次都从头开始，而是可以接着上次的位置继续向后移动。这保证了我们在计算跨区域数对时的总时间复杂度为线性级别 O(N)。



代码演示：

```java
    public static int Solution(int[] arr) {
        if (arr == null || arr.length < 2) {
            return 0;
        }

        return process(arr, 0, arr.length - 1);
    }

    public static int process(int[] arr, int l, int r) {
        if (l == r) {
            return 0;
        }
        int mid = l + ((r - l) >> 1);

        return process(arr, l, mid) + process(arr, mid + 1, r) + mergeBTT(arr, l, mid, r);
    }

    public static int mergeBTT(int[] arr, int l, int mid, int r) {

        // 先处理计数问题
        int p1 = l;
        int p2 = mid + 1;
        int rst = 0;

        while (p1 <= mid) {
            while (p2 <= r && arr[p1] > arr[p2] * 2) {
                p2++;
            }
            rst += p2 - mid - 1;
            p1++;
        }

        // 再排有序
        int[] help = new int[r - l + 1];
        p1 = l;
        p2 = mid + 1;
        int idx = 0;
        while (p1 <= mid && p2 <= r) {
            if (arr[p1] <= arr[p2]) {
                help[idx++] = arr[p1++];
            } else {
                help[idx++] = arr[p2++];
            }
        }
        while (p1 <= mid) {
            help[idx++] = arr[p1++];
        }
        while (p2 <= r) {
            help[idx++] = arr[p2++];
        }
        for (int i = 0; i < help.length; i++) {
            arr[l + i] = help[i];
        }
        return rst;
    }
```

​	

## 3. 一些启发

通过以上三道题我们可以总结出一类题的做题方法

我们可以看到，这三道题的核心都在于利用归并排序的`merge`过程，计数可以完美融入合并的过程

这些问题都在统计一个数组中，符合某种特定大小关系的数对 `(arr[i], arr[j])` 的信息，其中一个数在另一个数的左边 (`i < j`)。

如果用暴力法解决，我们通常会写一个双重循环，就像你的“对数器”里那样，时间复杂度为 O(N2)。而归并排序的`merge`过程，恰好为我们提供了一个将复杂度降至 O(NlogN) 的绝佳机会。

这类问题的通用解决方案都遵循了**分治 ** 的思想，这与归并排序的结构完美契合。

一个问题的整体答案，可以被拆解为**三个部分**：

1. 左半部分内部的答案。
2. 右半部分内部的答案。
3. 跨越左右两部分的答案（即一个数在左半部分，另一个数在右半部分）。

这个思路可以递归地进行下去，直到数组被拆分成单个元素。当子数组只有一个元素时，其内部答案为0。

而整个算法的精髓就在于第3步：如何在合并 (Merge) 左右两个已经排好序的子数组时，高效地计算出“跨区域”的答案。

因为左右两个子数组已经各自有序，这就提供了一个巨大的便利：

- 在**小和问题**中，当 `左组[p1] < 右组[p2]` 时，我们能立刻确定 `左组[p1]` 比 `右组[p2]` 以及其后的所有元素都小。
- 在**逆序对问题**中，当 `左组[p1] > 右组[p2]` 时，我们能立刻确定 `左组[p1]` 以及其后的所有元素都比 `右组[p2]` 大。
- 在**BiggerThanTwice问题**中，对于一个 `左组[p1]`，我们可以在右组中用一个不回退的指针 `p2` 快速找到所有满足 `arr[p1] > 2 * arr[p2]` 的元素。

这种利用“**单调性**”（因为数组已有序）来避免重复比较和计数的技巧，是`merge`过程能做到线性的(O(N))关键。
