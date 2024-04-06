# LeetCode刷题笔记

***

- Tag顺序：数组-> 链表-> 哈希表->字符串->栈与队列->树->回溯->贪心->动态规划->图论->高级数据结构。先从简单刷起，做了几个类型题目之后，再慢慢做中等题目、困难题目。

- 兔子法：想一会儿想不出来就看答案

- 做算法题的技巧：

  - 先用固定的值对特定的例子完成求解。
  - 然后再将固定的值改为变化的值，从特定的例子推广到一般的例子，最终完成普遍意义上的求解。

  ---> **先静后动** 

## 数组

### 数组理论基础

- 首先要知道数组在内存中的存储方式，这样才能真正理解数组相关的面试题：

  - **数组是存放在连续内存空间上的相同类型数据的集合。** 
  - 数组可以方便的通过下标索引的方式获取到下标相对应的数据。

  注意：**数组下标都是从0开始的。** **数组内存空间的地址是连续的。** 

  - 正是**因为数组的在内存空间的地址是连续的，所以我们在删除或者增添元素的时候，就难免要移动其他元素的地址。** 

    例如删除下标为3的元素，需要对下标为3的元素后面的所有元素都要做移动操作，如图所示：

    <img src="img/数组/删除下标为3的元素.png" style="zoom:50%;" />

    **数组的元素是不能删的，只能覆盖。** 

### 1. 二分查找

#### 题目

- 给定一个 n 个元素有序的（升序）整型数组 nums 和一个目标值 target  ，写一个函数搜索 nums 中的 target，如果目标值存在返回下标，否则返回 -1。

  示例 1:

  ```
  输入: nums = [-1,0,3,5,9,12], target = 9     
  输出: 4       
  解释: 9 出现在 nums 中并且下标为 4     
  ```

  示例 2:

  ```
  输入: nums = [-1,0,3,5,9,12], target = 2     
  输出: -1        
  解释: 2 不存在 nums 中因此返回 -1     
  ```

  提示：

  - 你可以假设 nums 中的所有元素是不重复的。
  - n 将在 [1, 10000]之间。
  - nums 的每个元素都将在 [-9999, 9999]之间。

#### 思路

- **这道题目的前提是数组为有序数组**，同时题目还强调**数组中无重复元素**，因为一旦有重复元素，使用二分查找法返回的元素下标可能不是唯一的，这些都是使用二分法的前提条件。

  二分查找涉及的很多的边界条件，逻辑比较简单，但就是写不好。例如到底是 `while(left < right)` 还是 `while(left <= right)`，到底是`right = middle`呢，还是要`right = middle - 1`呢？

  大家写二分法经常写乱，主要是因为**对区间的定义没有想清楚，区间的定义就是不变量**。要在二分查找的过程中，保持不变量，就是在while寻找中每一次边界的处理都要坚持根据区间的定义来操作，这就是**循环不变量**规则。

- 我们定义 target 是在一个在左闭右闭的区间里，**也就是[left, right] （这个很重要非常重要）**。区间的定义就决定了二分法的代码应该如何写，**因为定义target在[left, right]区间，所以有如下两点：** 

  - while (left <= right) 要使用 <= ，因为left == right是有意义的，所以使用 <=
  - if (nums[middle] > target) right 要赋值为 middle - 1，因为当前这个nums[middle]一定不是target，那么接下来要查找的左区间结束下标位置就是 middle - 1

  代码如下：

  ```java
  class Solution {
      public int search(int[] nums, int target) {
          int left, right, middle;
          left = 0;
          right  = nums.length - 1;
          while(left <= right){
              middle = (left + right) / 2;
              if(nums[middle] == target){
                  return middle;
              }else if(nums[middle] < target){
                  left = middle + 1;
              }else{
                  right = middle - 1;
              }
          }
          return -1;
      }
  }
  ```

  - 时间复杂度：O(log n)
  - 空间复杂度：O(1)

### 2. 移除元素

#### 题目

- 给你一个数组 nums 和一个值 val，你需要 **原地** 移除所有数值等于 val 的元素，并返回移除后数组的新长度。

  不要使用额外的数组空间，你必须仅使用 O(1) 额外空间并 原地 修改输入数组。元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

  - 说明:

    为什么返回数值是整数，但输出的答案是数组呢?

    请注意，输入数组是以「引用」方式传递的，这意味着在函数里修改输入数组对于调用者是可见的。

    你可以想象内部操作如下:

    ```java
    // nums 是以“引用”方式传递的。也就是说，不对实参作任何拷贝
    int len = removeElement(nums, val);
    
    // 在函数里修改输入数组对于调用者是可见的。
    // 根据你的函数返回的长度, 它会打印出数组中 该长度范围内 的所有元素。
    for (int i = 0; i < len; i++) {
        print(nums[i]);
    }
    ```

  示例 1：

  ```
  输入：nums = [3,2,2,3], val = 3
  输出：2, nums = [2,2]
  解释：函数应该返回新的长度 2, 并且 nums 中的前两个元素均为 2。你不需要考虑数组中超出新长度后面的元素。例如，函数返回的新长度为 2 ，而 nums = [2,2,3,3] 或 nums = [2,2,0,0]，也会被视作正确答案。
  ```

   示例 2：

  ```
  输入：nums = [0,1,2,2,3,0,4,2], val = 2
  输出：5, nums = [0,1,4,0,3]
  解释：函数应该返回新的长度 5, 并且 nums 中的前五个元素为 0, 1, 3, 0, 4。注意这五个元素可为任意顺序。你不需要考虑数组中超出新长度后面的元素。
  ```

  提示：

  - `0 <= nums.length <= 100`
  - `0 <= nums[i] <= 50`
  - `0 <= val <= 100`

#### 思路

- 返回移除后数组的新长度很容易，**难点在于如何覆盖掉被移除的元素**？-> **双指针法** 

##### ★双指针法

- 双指针法（快慢指针法）： **通过一个快指针和慢指针在一个for循环下完成两个for循环的工作。** 

  定义快慢指针

  - **快指针**：寻找新数组的元素 ，新数组就是不含有目标元素的数组
  - **慢指针**：指向更新的新数组下标的位置

  **双指针法（快慢指针法）在数组和链表的操作中是非常常见的，很多考察数组、链表、字符串等操作的面试题，都使用双指针法。** 

- 利用双指针法实现元素覆盖：

  ```java
  class Solution {
      public int removeElement(int[] nums, int val) {
          int slowIndex = 0;
          for(int fastIndex = 0; fastIndex < nums.length; fastIndex++){
              if(nums[fastIndex] != val){
                  nums[slowIndex++] = nums[fastIndex];
              }
          }
          return slowIndex;
      }
  }
  ```

  - 时间复杂度：O(n)
  - 空间复杂度：O(1)

- 由于题目允许元素的顺序可以改变，所以可以用**相向双指针法**，以实现**移动最少的元素** 

  ```java
  class Solution {
      public int removeElement(int[] nums, int val) {
          int leftIndex = 0, rightIndex = nums.length - 1;
          while(leftIndex <= rightIndex){
              //如果nums[leftIndex]的值不等于要删除的元素的值
              if(nums[leftIndex] != val){
                  if(nums[rightIndex] != val){
                      leftIndex++;
                  }else{
                      rightIndex--;
                  }
              }else{	//如果nums[leftIndex]的值等于要删除的元素的值
                  if(nums[rightIndex] != val){
                      nums[leftIndex++] = nums[rightIndex--];
                  }else{
                      rightIndex--;
                  }
              }
          }
          return leftIndex;
      }
  }
  ```

  - 时间复杂度：O(n)
  - 空间复杂度：O(1)

### 3. 有序数组的平方

#### 题目

- 给你一个按 **非递减顺序** 排序的整数数组 `nums`，返回 **每个数字的平方** 组成的新数组，要求也按 **非递减顺序** 排序。

  **示例 1：** 

  ```
  输入：nums = [-4,-1,0,3,10]
  输出：[0,1,9,16,100]
  解释：平方后，数组变为 [16,1,0,9,100]
  排序后，数组变为 [0,1,9,16,100]
  ```

  **示例 2：** 

  ```
  输入：nums = [-7,-3,2,3,11]
  输出：[4,9,9,49,121]
  ```

  **提示：** 

  - `1 <= nums.length <= 10^4`
  - `-10^4 <= nums[i] <= 10^4`
  - `nums` 已按 **非递减顺序** 排序

#### 思路

- 依然使用双指针法。

- 数组其实是有序的， 只不过负数平方之后可能成为最大数了。

  那么**数组平方的最大值就在数组的两端，不是最左边就是最右边，不可能是中间**。

  此时可以考虑双指针法了，i 指向起始位置，j 指向终止位置。

- 定义一个新数组result，和A数组一样的大小，让 k 指向result数组终止位置。

  - 如果`A[i] * A[i] < A[j] * A[j]` 那么`result[k--] = A[j] * A[j];` 。
  - 如果`A[i] * A[i] >= A[j] * A[j]` 那么`result[k--] = A[i] * A[i];` 。

  代码如下：

  ```java
  class Solution {
      public int[] sortedSquares(int[] nums) {
          int[] newNums = new int[nums.length];
          int k = newNums.length - 1;     //令k指向newNums数组的最后一个位置
          int leftIndex = 0, rightIndex = nums.length - 1;
          while(k >= 0){
              if(nums[leftIndex] * nums[leftIndex] < nums[rightIndex] * nums[rightIndex]){
                  newNums[k--] = nums[rightIndex] * nums[rightIndex];
                  rightIndex--;
              }else{
                  newNums[k--] = nums[leftIndex] * nums[leftIndex];
                  leftIndex++;
              }
          }
          return newNums;
      }
  }
  ```
  
  - 时间复杂度：O(n)
  - 空间复杂度：O(n)

### 4. 长度最小的子数组

#### 题目

- 给定一个含有 `n` 个正整数的数组和一个正整数 `target` 。找出该数组中满足其和 ≥ target 的长度最小的 连续子数组 [nums~i~, nums~i+1~, ..., nums~r-1~, nums~r~] ，并返回其长度。如果不存在符合条件的子数组，返回 0。

  **示例 1：**

  ```
  输入：target = 7, nums = [2,3,1,2,4,3]
  输出：2
  解释：子数组 [4,3] 是该条件下的长度最小的子数组。
  ```

  **示例 2：**

  ```
  输入：target = 4, nums = [1,4,4]
  输出：1
  ```

  **示例 3：**

  ```
  输入：target = 11, nums = [1,1,1,1,1,1,1,1]
  输出：0
  ```

  **提示：**

  - `1 <= target <= 10^9`
  - `1 <= nums.length <= 10^5`
  - `1 <= nums[i] <= 10^5`

#### 思路

##### ★滑动窗口

- 所谓滑动窗口，**就是不断的调节子序列的起始位置和终止位置，从而得出我们要想的结果**。

  在暴力解法中，是一个for循环滑动窗口的起始位置，一个for循环为滑动窗口的终止位置，用两个 for 循环 完成了一个不断搜索区间的过程。

  那么滑动窗口如何用一个for循环来完成这个操作呢？

- 首先要思考 如果用一个for循环，那么应该表示 滑动窗口的起始位置，还是终止位置。

  - 如果只用一个for循环来表示 滑动窗口的起始位置，那么如何遍历剩下的终止位置？

    此时难免再次陷入 暴力解法的怪圈。

  - 所以 **只用一个for循环，那么这个循环的索引，一定是表示 滑动窗口的终止位置**。

    那么问题来了， 滑动窗口的起始位置如何移动呢？

  在本题中实现滑动窗口，主要确定如下三点：

  1. 窗口内是什么？
  2. 如何移动窗口的起始位置？
  3. 如何移动窗口的结束位置？

- 窗口就是 满足其和 ≥ s 的长度最小的 连续 子数组。

  窗口的起始位置如何移动：如果当前窗口的值大于 s 了，窗口就要向前移动了（也就是该缩小了）。

  窗口的结束位置如何移动：**窗口的结束位置**就是遍历数组的指针，也就是**for循环里的索引**。

  解题的关键在于 **窗口的起始位置如何移动**，如图所示：

  <img src="img/数组/动态调节滑动窗口的起始位置.png" style="zoom:67%;" />

  可以发现**滑动窗口的精妙之处在于根据当前子序列和大小的情况，不断调节子序列的起始位置。从而将O(n^2)暴力解法降为O(n)。** 

  代码如下：

  ```java
  class Solution {
      public int minSubArrayLen(int target, int[] nums) {
          int sum, result, subArrayLen;    
          int leftIndex;  //滑动窗口起始位置
          leftIndex = sum = 0;  
          result = Integer.MAX_VALUE;
          for(int rightIndex = 0; rightIndex < nums.length; rightIndex++){
              sum += nums[rightIndex];
              while(sum >= target){
                  subArrayLen = rightIndex - leftIndex + 1;
                  result = subArrayLen < result ? subArrayLen : result;
                  sum -= nums[leftIndex++];   //滑动窗口起始位置向前移动
              }
          }
          return result == Integer.MAX_VALUE ? 0 : result;
      }
  }
  ```
  
  - 时间复杂度：O(n)
  - 空间复杂度：O(1)


### 5. 螺旋矩阵Ⅱ

#### 题目

- 给你一个正整数 `n` ，生成一个包含 `1` 到 `n^2` 所有元素，且元素按顺时针顺序螺旋排列的 `n x n` 正方形矩阵 `matrix` 。

  **示例 1：**

  ```
  输入：n = 3
  输出：[[1,2,3],[8,9,4],[7,6,5]]
  ```

  ![](img/数组/螺旋矩阵示例.png)

  **示例 2：**

  ```
  输入：n = 1
  输出：[[1]]
  ```

  **提示：**

  - `1 <= n <= 20` 

#### 思路

- 求解本题要坚持**循环不变量原则**。

  模拟顺时针画矩阵的过程：

  - 填充上行从左到右
  - 填充右列从上到下
  - 填充下行从右到左
  - 填充左列从下到上

  由外向内一圈一圈这么画下去。这里一圈下来，我们要画每四条边，这四条边怎么画，每画一条边都要坚持一致的左闭右开，或者左开右闭的原则，这样这一圈才能按照统一的规则画下来。

  那么我按照**左闭右开的原则**，来画一圈，大家看一下：

  <img src="img/数组/螺旋矩阵示例左闭右开.png" style="zoom:25%;" />

  这里每一种颜色，代表一条边，我们遍历的长度，可以看出每一个拐角处的处理规则，拐角处让给新的一条边来继续画。这也是坚持了每条边左闭右开的原则。

  代码如下：

  ```java
  class Solution {
      public int[][] generateMatrix(int n) {
          int num = 0;    //矩阵中填入的数字
          int[][] matrix = new int[n][n];
          int start = 0;  //每一圈循环开始的点(start,start)
          int loop = 0;   //循环的圈数
          int i, j;		//i指代行下标，j指代列下标
          while (loop < n / 2) {
              for(j = start; j < n - loop - 1; j++){
                  matrix[start][j] = ++num;
              }
              for(i = start; i < n - loop - 1; i++){
                  matrix[i][j] = ++num;
              }
              for(; j > loop; j--){
                  matrix[i][j] = ++num;
              }
              for(; i > loop; i--){
                  matrix[i][j] = ++num;
              }
              start++;    //循环开始的点的更新
              loop++;     //已经循环了一圈
          }
          //当n为奇数时，处理矩阵中间的那个单元块
          if(n % 2 == 1){
              matrix[loop][loop] = ++num;
          }
          return matrix;
      }
  }
  ```

  - 时间复杂度 O(n^2): 模拟遍历二维矩阵的时间
  - 空间复杂度 O(1)

### 总结篇

- 数组是非常基础的数据结构，在面试中，考察数组的题目一般在思维上都不难，主要是考察对代码的掌控能力。也就是说，想法很简单，但实现起来 可能就不是那么回事了。

- 首先要知道数组在内存中的存储方式，这样才能真正理解数组相关的面试题。

  **数组是存放在连续内存空间上的相同类型数据的集合。** 数组可以方便的通过下标索引的方式获取到下标下对应的数据。

  需要两点注意的是：

  - **数组下标都是从0开始的。**
  - **数组内存空间的地址是连续的**

  正是**因为数组的在内存空间的地址是连续的，所以我们在删除或者增添元素的时候，就难免要移动其他元素的地址。** 

  **数组的元素是不能删的，只能覆盖。** 

![](img/数组/数组总结.png)

## 链表

### 链表理论基础

- 什么是链表，链表是一种通过指针串联在一起的线性结构，**每一个结点由两部分组成，一个是数据域一个是指针域**（存放指向下一个结点的指针），最后一个结点的指针域指向null（空指针的意思）。

  链表的入口结点称为链表的头结点也就是head。

  <img src="img/链表/单链表.png" style="zoom: 67%;" />

#### 链表的类型

- 单链表：即为前面的图，单链表中的指针域只能指向结点的下一个结点。

- 双链表：每一个结点有两个指针域，一个指向下一个结点，一个指向上一个结点。双链表 既可以向前查询也可以向后查询。

  <img src="img/链表/双链表.png" style="zoom:67%;" />

- 循环链表：顾名思义，就是链表首尾相连。循环链表可以用来解决约瑟夫环问题。

  <img src="img/链表/循环链表.png" style="zoom:67%;" />

#### 链表的存储方式

- 了解完链表的类型，再来说一说链表在内存中的存储方式。

  **数组是在内存中是连续分布的，但是链表在内存中可不是连续分布的**。链表是通过指针域的指针链接在内存中各个结点。所以链表中的结点在内存中不是连续分布的 ，而是散乱分布在内存中的某地址上，分配机制取决于操作系统的内存管理。

  如图所示：

  ![](img/链表/链表存储方式.png)

  这个链表起始结点为2， 终止结点为7， 各个结点分布在内存的不同地址空间上，通过指针串联在一起。

#### 链表的定义

- 接下来说一说链表的定义。链表结点的定义，很多同学在面试的时候都写不好。这是因为平时在刷leetcode的时候，链表的结点都默认定义好了，直接用就行了，所以同学们都没有注意到链表的结点是如何定义的。而在面试的时候，一旦要自己手写链表，就写的错漏百出。

  ```java
  public class ListNode {
      // 结点的值
      int val;
  
      // 下一个结点
      ListNode next;
  
      // 结点的构造函数(无参)
      public ListNode() {
      }
  
      // 结点的构造函数(有一个参数)
      public ListNode(int val) {
          this.val = val;
      }
  
      // 结点的构造函数(有两个参数)
      public ListNode(int val, ListNode next) {
          this.val = val;
          this.next = next;
      }
  }
  ```

#### 链表的操作

##### 删除结点

- 删除D结点，如图所示：

  <img src="img/链表/删除D结点.png" style="zoom:67%;" />

  只要将C结点的next指针指向E结点就可以了。

  那有同学说了，D结点不是依然存留在内存里么？只不过是没有在这个链表里而已。

  是这样的，所以在C++里最好是再手动释放这个D结点，释放这块内存。

  其他语言例如Java、Python，就有自己的内存回收机制，就不用自己手动释放了。

##### 添加结点

- 添加F结点，如图所示：

  <img src="img/链表/添加F结点.png" style="zoom:67%;" />

  可以看出链表的增添和删除都是O(1)操作，也不会影响到其他结点。

  但是要注意，要是删除第五个结点，需要从头结点查找到第四个结点通过next指针进行删除操作，**查找的时间复杂度是O(n)**。

#### 性能分析

- 再把链表的特性和数组的特性进行一个对比，如图所示：

  <img src="img/链表/链表和数组性能分析.png" style="zoom:67%;" />

  数组在定义的时候，长度就是固定的，如果想改动数组的长度，就需要重新定义一个新的数组。

  链表的长度可以是不固定的，并且可以动态增删， 适合数据量不固定，频繁增删，较少查询的场景。

### 1. 移除链表元素

#### 题目

- 给你一个链表的头结点 `head` 和一个整数 `val` ，请你删除链表中所有满足 `Node.val == val` 的结点，并返回 **新的头结点** 。

  **示例 1：**

  ```
  输入：head = [1,2,6,3,4,5,6], val = 6
  输出：[1,2,3,4,5]
  ```

  **示例 2：**

  ```
  输入：head = [], val = 1
  输出：[]
  ```

  **示例 3：**

  ```
  输入：head = [7,7,7,7], val = 7
  输出：[]
  ```

  **提示：**

  - 列表中的结点数目在范围 `[0, 104]` 内
  - `1 <= Node.val <= 50`
  - `0 <= val <= 50`

#### 思路

- 一般情况下，如果当前结点是要删除的结点，那么**让当前结点的前一个结点的next指针直接指向当前结点的下一个结点**即可。

  ![](img/链表/删除链表结点.png)

  但是如果头结点是要删除的结点，就可以有两种方式对链表进行操作：

  1. **直接使用原来的链表来进行删除操作。**
  2. **设置一个虚拟头结点再进行删除操作。**

- 方式1：直接使用原来的链表来进行删除操作。

  - 该方式需要增加一段业务逻辑：

    移除头结点和移除其他结点的操作是不一样的，因为链表的其他结点都是**通过前一个结点来移除当前结点**，而头结点没有前一个结点。

    所以头结点如何移除呢，其实只要**将头结点向后移动一位**就可以，这样就从链表中移除了一个头结点。

    ![](img/链表/移除头结点1.png)

    ![](img/链表/移除头结点2.png)

  代码如下：

  ```java
  /**
   * Definition for singly-linked list.
   * public class ListNode {
   * int val;
   * ListNode next;
   * ListNode() {}
   * ListNode(int val) { this.val = val; }
   * ListNode(int val, ListNode next) { this.val = val; this.next = next; }
   * }
   */
  class Solution {
      public ListNode removeElements(ListNode head, int val) {
          //移除头结点
          while (head != null && head.val == val) {
              head = head.next;
          }
          if (head == null) {
              return null;
          }
          ListNode pre = head;
          ListNode cur = head.next;
          while (cur != null) {
              if (cur.val == val) {
                  pre.next = cur.next;
              } else {
                  pre = cur;
              }
              cur = cur.next;
          }
          return head;
      }
  }
  ```

##### ★虚拟头结点

- 方式2：设置一个**虚拟头结点**再进行删除操作。

  - 这种方式的好处是将删除头结点这种特殊的情况转变为一般的情况，让我们可以以一种统一的逻辑来移除链表的结点。

    ![](img/链表/虚拟头结点.png)

    最后记得在题目中，return 头结点的时候，别忘了 `return dummyHead.next;`， 这才是新的头结点。

  代码如下：

  ```java
  /**
   * Definition for singly-linked list.
   * public class ListNode {
   * int val;
   * ListNode next;
   * ListNode() {}
   * ListNode(int val) { this.val = val; }
   * ListNode(int val, ListNode next) { this.val = val; this.next = next; }
   * }
   */
  class Solution {
      public ListNode removeElements(ListNode head, int val) {
          if (head == null) {
              return null;
          }
          //添加虚拟头结点
          ListNode dummyHead = new ListNode(-1, head);
          ListNode pre = dummyHead;
          ListNode cur = head;
          while (cur != null) {
              if (cur.val == val) {
                  pre.next = cur.next;
              } else {
                  pre = cur;
              }
              cur = cur.next;
          }
          return dummyHead.next;
      }
  }
  ```

### 2.设计链表

#### 题目

- 你可以选择使用单链表或者双链表，设计并实现自己的链表。

  单链表中的结点应该具备两个属性：`val` 和 `next` 。`val` 是当前结点的值，`next` 是指向下一个结点的指针/引用。

  如果是双向链表，则还需要属性 `prev` 以指示链表中的上一个结点。假设链表中的所有结点下标从 **0** 开始。

- 实现 `MyLinkedList` 类：

  - `MyLinkedList()` 初始化 `MyLinkedList` 对象。
  - `int get(int index)` 获取链表中下标为 `index` 的结点的值。如果下标无效，则返回 `-1` 。
  - `void addAtHead(int val)` 将一个值为 `val` 的结点插入到链表中第一个元素之前。在插入完成后，新结点会成为链表的第一个结点。
  - `void addAtTail(int val)` 将一个值为 `val` 的结点追加到链表中作为链表的最后一个元素。
  - `void addAtIndex(int index, int val)` 将一个值为 `val` 的结点插入到链表中下标为 `index` 的结点之前。如果 `index` 等于链表的长度，那么该结点会被追加到链表的末尾。如果 `index` 比长度更大，该结点将 **不会插入** 到链表中。
  - `void deleteAtIndex(int index)` 如果下标有效，则删除链表中下标为 `index` 的结点。

  **示例：**

  ```
  输入
  ["MyLinkedList", "addAtHead", "addAtTail", "addAtIndex", "get", "deleteAtIndex", "get"]
  [[], [1], [3], [1, 2], [1], [1], [1]]
  输出
  [null, null, null, null, 2, null, 3]
  
  解释
  MyLinkedList myLinkedList = new MyLinkedList();
  myLinkedList.addAtHead(1);
  myLinkedList.addAtTail(3);
  myLinkedList.addAtIndex(1, 2);    // 链表变为 1->2->3
  myLinkedList.get(1);              // 返回 2
  myLinkedList.deleteAtIndex(1);    // 现在，链表变为 1->3
  myLinkedList.get(1);              // 返回 3
  ```

  **提示：**

  - `0 <= index, val <= 1000`
  - 请不要使用内置的 LinkedList 库。
  - 调用 `get`、`addAtHead`、`addAtTail`、`addAtIndex` 和 `deleteAtIndex` 的次数不超过 `2000` 。

#### 思路

- 单链表实现：

  ```java
  //单链表
  class ListNode {
      int val;
      ListNode next;
  
      public ListNode() {
  
      }
  
      public ListNode(int val) {
          this.val = val;
      }
  
      public ListNode(int val, ListNode next) {
          this.val = val;
          this.next = next;
      }
  }
  
  class MyLinkedList {
      //链表中元素的个数
      int size;
      //虚拟头结点
      ListNode dummyHead;
  
      //初始化链表
      public MyLinkedList() {
          size = 0;
          dummyHead = new ListNode(-1, null);
      }
  
      public int get(int index) {
          if (index < 0 || index >= size) {
              return -1;
          }
          ListNode cur = dummyHead.next;
          for (int i = 0; i < index; i++) {
              cur = cur.next;
          }
          return cur.val;
      }
  
      public void addAtHead(int val) {
          addAtIndex(0, val);
      }
  
      public void addAtTail(int val) {
          addAtIndex(size, val);
      }
  
      public void addAtIndex(int index, int val) {
          if (index < 0 || index > size) {
              return;
          }
          ListNode pre = dummyHead;
          ListNode cur = dummyHead.next;
          for (int i = 0; i < index; i++) {
              pre = cur;
              cur = cur.next;
          }
          ListNode node = new ListNode(val);
          pre.next = node;
          node.next = cur;
          size++;
      }
  
      public void deleteAtIndex(int index) {
          if (index < 0 || index >= size) {
              return;
          }
          ListNode pre = dummyHead;
          ListNode cur = dummyHead.next;
          for (int i = 0; i < index; i++) {
              pre = cur;
              cur = cur.next;
          }
          pre.next = cur.next;
          size--;
      }
  }
  
  /**
   * Your MyLinkedList object will be instantiated and called as such:
   * MyLinkedList obj = new MyLinkedList();
   * int param_1 = obj.get(index);
   * obj.addAtHead(val);
   * obj.addAtTail(val);
   * obj.addAtIndex(index,val);
   * obj.deleteAtIndex(index);
   */
  ```

  **抓住核心：`index` 指向的是要处理的结点**。

- 双链表实现：

  ```java
  //双链表
  class ListNode {
      int val;
      ListNode next, prev;
  
      public ListNode() {
      }
  
      public ListNode(int val) {
          this.val = val;
      }
  
      public ListNode(int val, ListNode prev, ListNode next) {
          this.val = val;
          this.prev = prev;
          this.next = next;
      }
  }
  
  class MyLinkedList {
      //链表中元素的个数
      int size;
      //虚拟头结点
      ListNode dummyHead, dummyTail;
  
      //初始化链表
      public MyLinkedList() {
          size = 0;
          dummyHead = new ListNode(-1);
          dummyTail = new ListNode(-1, dummyHead, null);
          dummyHead.prev = null;
          dummyHead.next = dummyTail;
      }
  
      public int get(int index) {
          if (index < 0 || index >= size) {
              return -1;
          }
          ListNode cur;
          //判断哪一边遍历的时间更短
          if (index <= size / 2) {
              cur = dummyHead.next;
              for (int i = 0; i < index; i++) {
                  cur = cur.next;
              }
          } else {
              cur = dummyTail.prev;
              for (int i = size - 1; i > index; i--) {
                  cur = cur.prev;
              }
          }
          return cur.val;
      }
  
      public void addAtHead(int val) {
          addAtIndex(0, val);
      }
  
      public void addAtTail(int val) {
          addAtIndex(size, val);
      }
  
      public void addAtIndex(int index, int val) {
          if (index < 0 || index > size) {
              return;
          }
          ListNode pre = dummyHead;
          ListNode cur = dummyHead.next;
          for (int i = 0; i < index; i++) {
              pre = cur;
              cur = cur.next;
          }
          ListNode node = new ListNode(val);
          pre.next = node;
          node.prev = pre;
          node.next = cur;
          cur.prev = node;
          size++;
      }
  
      public void deleteAtIndex(int index) {
          if (index < 0 || index >= size) {
              return;
          }
          ListNode pre = dummyHead;
          ListNode cur = dummyHead.next;
          for (int i = 0; i < index; i++) {
              pre = cur;
              cur = cur.next;
          }
          pre.next = cur.next;
          cur.next.prev = pre;
          size--;
      }
  }
  
  /**
   * Your MyLinkedList object will be instantiated and called as such:
   * MyLinkedList obj = new MyLinkedList();
   * int param_1 = obj.get(index);
   * obj.addAtHead(val);
   * obj.addAtTail(val);
   * obj.addAtIndex(index,val);
   * obj.deleteAtIndex(index);
   */
  ```

### 3.★反转链表

#### 题目

- 给你单链表的头结点 `head` ，请你反转链表，并返回反转后的链表。

  **示例 1：**

  ![img](https://assets.leetcode.com/uploads/2021/02/19/rev1ex1.jpg)

  ```
  输入：head = [1,2,3,4,5]
  输出：[5,4,3,2,1]
  ```

  **示例 2：**

  ![img](https://assets.leetcode.com/uploads/2021/02/19/rev1ex2.jpg)

  ```
  输入：head = [1,2]
  输出：[2,1]
  ```

  **示例 3：**

  ```
  输入：head = []
  输出：[]
  ```

  **提示：**

  - 链表中结点的数目范围是 `[0, 5000]`
  - `-5000 <= Node.val <= 5000`

#### 思路

- 首先定义一个`cur`指针，指向头结点，再定义一个`pre`指针，初始化为`null`。

  然后就要开始反转了，**首先要把 `cur.next` 结点用`tmp`指针保存一下**，也就是保存一下这个结点。

  为什么要保存一下这个结点呢，因为接下来要改变 `cur.next` 的指向了，将 `cur.next` 指向 `pre` ，此时已经反转了第一个结点了，为了让 `cur` 能指向下一个要反转的结点，就要用 `tmp` 保存这个结点。

  接下来，就是循环走如下代码逻辑了，继续移动`pre`和`cur`指针。

  最后，`cur` 指针已经指向了`null`，循环结束，链表也反转完毕了。 此时我们`return pre`指针就可以了，`pre`指针就指向了新的头结点。

  ```java
  /**
   * Definition for singly-linked list.
   * public class ListNode {
   *     int val;
   *     ListNode next;
   *     ListNode() {}
   *     ListNode(int val) { this.val = val; }
   *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
   * }
   */
  //双指针法
  class Solution {
      public ListNode reverseList(ListNode head) {
          ListNode pre, cur, tmp;
          pre = null;
          cur = head;
          tmp = null;
          while (cur != null) {
              //用 tmp 先保存当前结点的下一个结点
              tmp = cur.next;
              cur.next = pre;
              pre = cur;
              cur = tmp;
          }
          return pre;
      }
  }
  ```

- 还有一种递归的方法（核心思想和前面的迭代法是一样的）：

  ```java
  /**
   * Definition for singly-linked list.
   * public class ListNode {
   *     int val;
   *     ListNode next;
   *     ListNode() {}
   *     ListNode(int val) { this.val = val; }
   *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
   * }
   */
   //递归法
  class Solution {
      public ListNode reverseList(ListNode head) {
          return reverse(null, head);
      }
  
      private ListNode reverse(ListNode prev, ListNode cur) {
          if (cur == null) {
              return prev;    //返回反转以后的头指针
          }
          ListNode tmp = cur.next;
          cur.next = prev;
          return reverse(cur, tmp);
      }
  }
  ```

### 3.2 反转链表 II

#### 题目

- 给你单链表的头指针 `head` 和两个整数 `left` 和 `right` ，其中 `left <= right` 。请你反转从位置 `left` 到位置 `right` 的链表节点，返回 **反转后的链表** 。

  **示例 1：**

  ![img](https://assets.leetcode.com/uploads/2021/02/19/rev2ex2.jpg)

  ```
  输入：head = [1,2,3,4,5], left = 2, right = 4
  输出：[1,4,3,2,5]
  ```

  **示例 2：**

  ```
  输入：head = [5], left = 1, right = 1
  输出：[5]
  ```

  **提示：**

  - 链表中节点数目为 `n`
  - `1 <= n <= 500`
  - `-500 <= Node.val <= 500`
  - `1 <= left <= right <= n`

#### 思路

- 和 `3.反转链表` 的不同之处在于，需要额外定义两个指针，分别指向 `left - 1` 所在的结点和 `left` 所在的结点，才能完成链表的部分反转。

  ```java
  class Solution {
      public ListNode reverseBetween(ListNode head, int left, int right) {
          if (head == null || head.next == null) { // 排除特殊情况
              return head;
          }
          ListNode dummyHead = new ListNode(-1, head); // 增加一个虚拟头结点
          ListNode pre, cur, next, tmpt1, tmpt2;
          tmpt1 = dummyHead;
          for (int i = 0; i < left - 1; i++) {
              tmpt1 = tmpt1.next; // tmpt1用于指向left左边的结点
          }
          tmpt2 = tmpt1.next; // tmpt2用于指向left所在的结点
          pre = tmpt2;
          cur = tmpt2.next;
          for (int i = 0; i < right - left; i++) { // 反转的次数
              next = cur.next;
              cur.next = pre;
              pre = cur;
              cur = next;
          }
          tmpt1.next = pre;
          tmpt2.next = cur;
          return dummyHead.next;
      }
  }
  ```

### 4.两两交换链表中的结点

#### 题目

- 给你一个链表，两两交换其中相邻的结点，并返回交换后链表的头结点。你必须在不修改结点内部的值的情况下完成本题（即，只能进行结点交换）。

  **示例 1：**

  ![img](https://assets.leetcode.com/uploads/2020/10/03/swap_ex1.jpg)

  ```
  输入：head = [1,2,3,4]
  输出：[2,1,4,3]
  ```

  **示例 2：**

  ```
  输入：head = []
  输出：[]
  ```

  **示例 3：**

  ```
  输入：head = [1]
  输出：[1]
  ```

  **提示：**

  - 链表中结点的数目在范围 `[0, 100]` 内
  - `0 <= Node.val <= 100`

#### 思路

- 建议使用虚拟头结点，这样会方便很多，要不然每次针对头结点（没有前一个指针指向头结点），还要单独处理。

  接下来就是交换相邻两个元素了，**此时一定要画图，不画图，操作多个指针很容易乱，而且要注意操作的先后顺序**：

  - 初始时，`cur`指向虚拟头结点，然后进行如下三步：

    ![](img/链表/两两交换链表中的结点1.png)

    操作之后，链表如下：

    ![](img/链表/两两交换链表中的结点2.png)

  如此循环往复即可。

  ```java
  /**
   * Definition for singly-linked list.
   * public class ListNode {
   *     int val;
   *     ListNode next;
   *     ListNode() {}
   *     ListNode(int val) { this.val = val; }
   *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
   * }
   */
  class Solution {
      public ListNode swapPairs(ListNode head) {
          ListNode dummyHead = new ListNode(-1, head); //虚拟头结点
          ListNode cur, firstNode, secondNode;
          ListNode tmp;   //临时结点用来保存要交换的两个结点的再下一个结点
          cur = dummyHead;
          while (cur.next != null && cur.next.next != null) {
              firstNode = cur.next;
              secondNode = firstNode.next;
              tmp = secondNode.next;
              cur.next = secondNode;          //步骤1
              secondNode.next = firstNode;    //步骤2
              firstNode.next = tmp;           //步骤3
              cur = firstNode;
          }
          return dummyHead.next;
      }
  }
  ```

### 5.删除链表的倒数第N个结点

#### 题目

- 给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。

   

  **示例 1：**

  ![img](https://assets.leetcode.com/uploads/2020/10/03/remove_ex1.jpg)

  ```
  输入：head = [1,2,3,4,5], n = 2
  输出：[1,2,3,5]
  ```

  **示例 2：**

  ```
  输入：head = [1], n = 1
  输出：[]
  ```

  **示例 3：**

  ```
  输入：head = [1,2], n = 1
  输出：[1]
  ```

  **提示：**

  - 链表中结点的数目为 `sz`
  - `1 <= sz <= 30`
  - `0 <= Node.val <= 100`
  - `1 <= n <= sz`

#### 思路

- 很直观的想法：先遍历一遍得到链表的长度 `sz`，然后从链表头结点开始向后找到第 `sz-n` 个结点，就是要删除的倒数第 `n` 个结点。

  考虑到有可能删除的是倒数第 `sz` 个结点，即头结点，所以添加一个虚拟头结点。

  ```java
  /**
   * Definition for singly-linked list.
   * public class ListNode {
   *     int val;
   *     ListNode next;
   *     ListNode() {}
   *     ListNode(int val) { this.val = val; }
   *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
   * }
   */
  class Solution {
      public ListNode removeNthFromEnd(ListNode head, int n) {
          //添加虚拟头结点
          ListNode dummyHead = new ListNode(-1, head);
          ListNode pre, cur;
          //计算链表结点数
          int sz = 0;
          cur = head;
          while (cur != null) {
              sz++;
              cur = cur.next;
          }
          pre = dummyHead;
          for (int i = 0; i < sz - n; i++) {
              pre = pre.next;
          }
          cur = pre.next;	//cur指向的是要删除的结点
          pre.next = cur.next;
          return dummyHead.next;
      }
  }
  ```

##### 虚拟头结点+双指针

- **进阶：**你能尝试使用一趟扫描实现吗？

  若要使用一趟扫描实现，可以用到双指针。如果要删除倒数第 `n` 个结点，让 `fast` 移动 `n` 步，然后让 `fast` 和 `slow` 同时移动，直到 `fast` 指向链表末尾。删掉 `slow` 所指向的结点就可以了。

  1. 定义 `fast` 指针和 `slow` 指针，初始值为虚拟头结点，如图：

     ![](img/链表/删除链表的倒数第N个结点1.png)

  2. `fast` 首先走 `n + 1` 步 ，为什么是 `n + 1` 呢，因为只有这样同时移动的时候 `slow` 才能指向删除结点的上一个结点（方便做删除操作），如图：

     ![](img/链表/删除链表的倒数第N个结点2.png)

     让 `slow` 指向要删除结点的上一个结点（方便删除）。

  3. `fast` 和 `slow` 同时移动，直到 `fast` 指向末尾，如图：

     ![](img/链表/删除链表的倒数第N个结点3.png)

  4. 删除 `slow` 指向的下一个结点，如图：

     ![](img/链表/删除链表的倒数第N个结点4.png)

  ```java
  /**
   * Definition for singly-linked list.
   * public class ListNode {
   *     int val;
   *     ListNode next;
   *     ListNode() {}
   *     ListNode(int val) { this.val = val; }
   *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
   * }
   */
  class Solution {
      public ListNode removeNthFromEnd(ListNode head, int n) {
          //添加虚拟头结点
          ListNode dummyHead = new ListNode(-1, head);
          //快慢指针
          ListNode fastIndex, slowIndex;
          fastIndex = dummyHead;
          slowIndex = dummyHead;
          for (int i = 0; i <= n; i++) {  //让 fastIndex 向后移动 n+1 步
              fastIndex = fastIndex.next;
          }
          while (fastIndex != null) {
              fastIndex = fastIndex.next;
              slowIndex = slowIndex.next;
          }
          //此时 slowIndex 指向的是要删除结点的前一个结点
          slowIndex.next = slowIndex.next.next;
          return dummyHead.next;
      }
  }
  ```

### 6.链表相交

#### 题目

- 给你两个单链表的头结点 `headA` 和 `headB` ，请你找出并返回两个单链表相交的起始结点。如果两个链表没有交点，返回 `null` 。

  图示两个链表在结点 `c1` 开始相交**：**

  [![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_statement.png)](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_statement.png)

  题目数据 **保证** 整个链式结构中不存在环。

  **注意**，函数返回结果后，链表必须 **保持其原始结构** 。

  **示例 1：**

  [![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_example_1.png)](https://assets.leetcode.com/uploads/2018/12/13/160_example_1.png)

  ```
  输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,0,1,8,4,5], skipA = 2, skipB = 3
  输出：Intersected at '8'
  解释：相交结点的值为 8 （注意，如果两个链表相交则不能为 0）。
  从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,0,1,8,4,5]。
  在 A 中，相交结点前有 2 个结点；在 B 中，相交结点前有 3 个结点。
  ```

  **示例 2：**

  [![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_example_2.png)](https://assets.leetcode.com/uploads/2018/12/13/160_example_2.png)

  ```
  输入：intersectVal = 2, listA = [0,9,1,2,4], listB = [3,2,4], skipA = 3, skipB = 1
  输出：Intersected at '2'
  解释：相交结点的值为 2 （注意，如果两个链表相交则不能为 0）。
  从各自的表头开始算起，链表 A 为 [0,9,1,2,4]，链表 B 为 [3,2,4]。
  在 A 中，相交结点前有 3 个结点；在 B 中，相交结点前有 1 个结点。
  ```

  **示例 3：**

  [![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_example_3.png)](https://assets.leetcode.com/uploads/2018/12/13/160_example_3.png)

  ```
  输入：intersectVal = 0, listA = [2,6,4], listB = [1,5], skipA = 3, skipB = 2
  输出：null
  解释：从各自的表头开始算起，链表 A 为 [2,6,4]，链表 B 为 [1,5]。
  由于这两个链表不相交，所以 intersectVal 必须为 0，而 skipA 和 skipB 可以是任意值。
  这两个链表不相交，因此返回 null 。
  ```

  **提示：**

  - `listA` 中结点数目为 `m`
  - `listB` 中结点数目为 `n`
  - `0 <= m, n <= 3 * 104`
  - `1 <= Node.val <= 105`
  - `0 <= skipA <= m`
  - `0 <= skipB <= n`
  - 如果 `listA` 和 `listB` 没有交点，`intersectVal` 为 `0`
  - 如果 `listA` 和 `listB` 有交点，`intersectVal == listA[skipA + 1] == listB[skipB + 1]`

#### 思路

- 简单来说，就是求两个链表交点结点的指针。 这里同学们要注意，**交点不是数值相等，而是指针相等**。

- 直观的思路：对 `listA` 中的每一个结点，在 `listB` 中都遍历一遍，看看该结点是否在 `listB` 中，若是，则该结点就是链表相交的初始结点。

  如果遍历完 `listA` 都没有结点在 `listB` 中，则这两条链表不相交。

  ```java
  /**
   * Definition for singly-linked list.
   * public class ListNode {
   *     int val;
   *     ListNode next;
   *     ListNode(int x) {
   *         val = x;
   *         next = null;
   *     }
   * }
   */
  public class Solution {
      public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
          ListNode ANode = headA;
          ListNode BNode = headB;
          while (ANode != null) {
              while (BNode != ANode && BNode != null) {
                  BNode = BNode.next;
              }
              if (BNode != null) {
                  return BNode;
              }
              ANode = ANode.next;
              BNode = headB;
          }
          return null;
      }
  }
  ```

  - 时间复杂度：`O(m * n)` 
  - 空间复杂度：`O(1)` 

- **进阶：**你能否设计一个时间复杂度 `O(n)` 、仅用 `O(1)` 内存的解决方案？

  - 如果两条链表相交，则从相交点之后两个链表的结点都是一样的、Y字形，所以直接**让长链表从后面部分的结点开始和短链表进行比较，因为交点只可能出现在这段区域**。如下图所示：

    ![](img/链表/链表相交.png)

  ```java
  /**
   * Definition for singly-linked list.
   * public class ListNode {
   *     int val;
   *     ListNode next;
   *     ListNode(int x) {
   *         val = x;
   *         next = null;
   *     }
   * }
   */
  public class Solution {
      public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
          ListNode curA, curB;
          int lenA, lenB, offset;
          curA = headA;
          curB = headB;
          lenA = lenB = 0;
          //得到链表A的长度
          while (curA != null) {
              lenA++;
              curA = curA.next;
          }
          //得到链表B的长度
          while (curB != null) {
              lenB++;
              curB = curB.next;
          }
          //让curA指向长链表的头结点, curB指向短链表的头结点
          if (lenA >= lenB) {
              curA = headA;
              curB = headB;
              offset = lenA - lenB;
          }else {
              curA = headB;
              curB = headA;
              offset = lenB - lenA;
          }
          //从长链表的后面部分开始比较
          for (int i = 0; i < offset; i++) {
              curA = curA.next;
          }
          while (curA != null) {
              if (curA == curB) {
                  return curA;
              }
              curA = curA.next;
              curB = curB.next;
          }
          return null;
      }
  }
  ```

### 7.★环形链表

#### 题目

- 给定一个链表的头结点  `head` ，返回链表开始入环的第一个结点。 如果链表无环，则返回 `null`。

  如果链表中有某个结点，可以通过连续跟踪 `next` 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 `pos` 来表示链表尾连接到链表中的位置（**索引从 0 开始**）。如果 `pos` 是 `-1`，则在该链表中没有环。**注意：`pos` 不作为参数进行传递**，仅仅是为了标识链表的实际情况。

  **不允许修改** 链表。

  **示例 1：**

  ![img](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist.png)

  ```
  输入：head = [3,2,0,-4], pos = 1
  输出：返回索引为 1 的链表结点
  解释：链表中有一个环，其尾部连接到第二个结点。
  ```

  **示例 2：**

  ![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test2.png)

  ```
  输入：head = [1,2], pos = 0
  输出：返回索引为 0 的链表结点
  解释：链表中有一个环，其尾部连接到第一个结点。
  ```

  **示例 3：**

  ![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test3.png)

  ```
  输入：head = [1], pos = -1
  输出：返回 null
  解释：链表中没有环。
  ```

  **提示：**

  - 链表中结点的数目范围在范围 `[0, 104]` 内
  - `-105 <= Node.val <= 105`
  - `pos` 的值为 `-1` 或者链表中的一个有效索引

#### 思路

- 主要考察两个知识点：
  - 判断链表是否有环
  - 如果有环，如何找到这个环的入口

##### ★判断链表是否有环

###### 快慢指针法

- 可以使用**快慢指针法**，分别定义 `fast` 和 `slow` 指针，从头结点出发，`fast` 指针每次移动两个结点，`slow` 指针每次移动一个结点，如果 `fast` 和 `slow`指针在途中相遇 ，说明这个链表有环。

  为什么 `fast` 走两个结点，`slow` 走一个结点，有环的话，一定会在环内相遇呢，而不是永远的错开呢？

  - 首先第一点：`fast` 指针一定先进入环中，如果 `fast` 指针和 `slow` 指针相遇的话，一定是在环中相遇，这是毋庸置疑的。
  - 其次，因为 `fast` 是走两步，`slow` 是走一步，**其实相对于 `slow` 来说，`fast` 是一个结点一个结点的靠近 `slow` 的，所以 `fast` 一定可以和 `slow` 重合**。

##### 如果有环，如何找到这个环的入口

- 假设从头结点到环形入口结点的结点数为 `x`，环形入口结点到 `fast` 指针与 `slow` 指针相遇结点的结点数为 `y`，从相遇结点再到环形入口结点结点数为 `z`。 如图所示：

  ![](img/链表/环形链表入口.png)

  - 那么相遇时： `slow` 指针走过的结点数为：`x + y`， `fast` 指针走过的结点数：`x + y + n (y + z)`，`n` 为 `fast` 指针在环内走了 `n` 圈才遇到 `slow` 指针，`(y + z)`为一圈内结点的个数。

  - 因为 `fast` 指针是一步走两个结点，`slow` 指针一步走一个结点， 所以 `fast` 指针走过的结点数 = `slow` 指针走过的结点数 * 2：`(x + y) * 2 = x + y + n (y + z)` ，两边消掉一个`(x + y)`： `x + y = n (y + z)` 。

    因为要找环形的入口，那么要求的是 `x`，因为 `x` 表示头结点到环形入口结点的的距离。

    所以要求 `x` ，将 `x` 单独放在左面：`x = n (y + z) - y`，再从 `n (y + z)` 中提出一个  `(y + z)` 来，整理公式之后为如下公式：`x = (n - 1) (y + z) + z` ，注意这里 `n` 一定是大于等于 `1` 的，因为 `fast` 指针至少要多走一圈才能相遇 `slow` 指针。

  现在我们得到了一个公式：`x = (n - 1) (y + z) + z, n >= 1`。

  - 以 `n = 1` 为例，意味着 `fast` 指针在环形里转了一圈之后，就遇到了 `slow` 指针了。

    这时公式化解为 `x = z`。

    这就意味着，**从头结点出发一个指针，从 `fast` 和 `slow` 相遇结点处也出发一个指针，这两个指针每次只走一个结点， 那么当这两个指针相遇的时候就是 环形入口的结点**。

    也就是在相遇结点处，定义一个指针 `index1`，在头结点处定一个指针 `index2`。让 `index1` 和 `index2` 同时移动，每次移动一个结点， 那么他们相遇的地方就是 环形入口的结点。

  - 那么 `n >= 1` 是什么情况呢，就是 `fast` 指针在环形转 `n` 圈之后才遇到 `slow` 指针。

    其实这种情况和 `n = 1` 的时候的效果是一样的，一样可以通过这个方法找到环形的入口结点，只不过，`index1` 指针在环里多转了 `(n-1)` 圈，然后再遇到 `index2`，相遇点依然是环形的入口结点。

```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode fastIndex, slowIndex;
        fastIndex = head;
        slowIndex = head;
        //得到快慢指针相遇的结点
        while (fastIndex != null && fastIndex.next != null) {
            fastIndex = fastIndex.next.next;
            slowIndex = slowIndex.next;
            if (fastIndex == slowIndex) {   //有环
                ListNode index1 = head;
                ListNode index2 = fastIndex;
                while (index1 != index2) {
                    index1 = index1.next;
                    index2 = index2.next;
                }
                return index1;
            }
        }
        return null;
    }
}
```

### 总结篇

- 链表的种类主要为：单链表，双链表，循环链表
- 链表的存储方式：链表的结点在内存中是分散存储的，通过指针连在一起。
- 链表是如何进行增删改查的。
- 数组和链表在不同场景下的性能分析。

- **虚拟头结点**：链表的一大问题就是操作当前结点必须要找前一个结点才能操作。这就造成了，头结点的尴尬，因为头结点没有前一个结点了。

  **每次对应头结点的情况都要单独处理，所以使用虚拟头结点的技巧，就可以解决这个问题**。

- **判断链表是否有环：使用快慢指针法来判断**。

![](img/链表/链表总结.png)

## 哈希表

### 哈希表理论基础

#### 哈希表

- 首先什么是哈希表，**哈希表**（英文名字为`Hash table`，国内也有一些算法书籍翻译为**散列表**，大家看到这两个名称知道都是指 `hash table` 就可以了）。

  > 哈希表是根据关键码的值而直接进行访问的数据结构。

  - 这么这官方的解释可能有点懵，其实直白来讲其实数组就是一张哈希表。

    哈希表中关键码就是数组的索引下标，然后通过下标直接访问数组中的元素。

- 那么哈希表能解决什么问题呢，**一般哈希表都是用来快速判断一个元素是否出现在集合里。**

  例如要查询一个名字是否在这所学校里。

  要枚举的话时间复杂度是`O(n)`，但如果使用哈希表的话， 只需要`O(1)`就可以做到。

  我们只需要初始化把这所学校里学生的名字都存在哈希表里，在查询的时候通过索引直接就可以知道这位同学在不在这所学校里了。

  将学生姓名映射到哈希表上就涉及到了**hash function ，也就是哈希函数**。

#### 哈希函数

- 哈希函数，把学生的姓名直接映射为哈希表上的索引，然后就可以通过查询索引下标快速知道这位同学是否在这所学校里了。

  哈希函数如下图所示，通过 `hashCode` 把名字转化为数值，一般 `hashcode` 是通过特定编码方式，可以将其他数据格式转化为不同的数值，这样就把学生名字映射为哈希表上的索引数字了。

  ![](img/哈希表/哈希函数.png)

  - 如果 `hashCode` 得到的数值大于哈希表的大小了，也就是大于 `tableSize` 了，怎么办呢？

    此时为了保证映射出来的索引数值都落在哈希表上，我们会再次对数值做一个取模的操作，就保证了学生姓名一定可以映射到哈希表上了。

    此时问题又来了，哈希表我们刚刚说过，就是一个数组。

    如果学生的数量大于哈希表的大小怎么办，此时就算哈希函数计算的再均匀，也避免不了会有几位学生的名字同时映射到哈希表 同一个索引下标的位置。

    接下来**哈希碰撞**登场。

#### 哈希碰撞

- 如图所示，小李和小王都映射到了索引下标 1 的位置，**这一现象叫做哈希碰撞**。

  ![](img/哈希表/哈希碰撞.png)

  一般哈希碰撞有两种解决方法， 拉链法和线性探测法。

- **拉链法**：

  - 既然小李和小王在索引 `1` 的位置发生了冲突，那就将发生冲突的元素都存储在链表中。 这样我们就可以通过**索引 + 链表**的方式找到小李和小王了。

    ![](img/哈希表/拉链法.png)

    其实拉链法就是要选择适当的哈希表的大小，这样既不会因为数组空值而浪费大量内存，也不会因为链表太长而在查找上浪费太多时间。

- **线性探测法**：

  - 使用线性探测法，一定要保证 `tableSize` 大于 `dataSize`。 我们需要依靠哈希表中的空位来解决碰撞问题。

    例如冲突的位置，放了小李，那么就向下找一个空位放置小王的信息。所以要求 `tableSize` 一定要大于 `dataSize`，要不然哈希表上就没有空置的位置来存放冲突的数据了。如图所示：

    ![](img/哈希表/线性探测法.png)

#### 总结

- 总结一下，**当我们遇到了要快速判断一个元素是否出现集合里的时候，就要考虑哈希法**。

  但是哈希法也是**牺牲了空间换取了时间**，因为我们要使用额外的数组，`set` 或者是 `map` 来存放数据，才能实现快速的查找。

  如果在做面试题目的时候遇到**需要判断一个元素是否出现过的场景也应该第一时间想到哈希法**！

### 1.有效的字母异位词

#### 题目

- 给定两个字符串 `s` 和 `t` ，编写一个函数来判断 `t` 是否是 `s` 的字母异位词。

  **注意：**若 `s` 和 `t` 中每个字符出现的次数都相同，则称 `s` 和 `t` 互为字母异位词。

  **示例 1:**

  ```
  输入: s = "anagram", t = "nagaram"
  输出: true
  ```

  **示例 2:**

  ```
  输入: s = "rat", t = "car"
  输出: false
  ```

  **提示:**

  - `1 <= s.length, t.length <= 5 * 104`
  - `s` 和 `t` 仅包含小写字母

#### 思路

- 定义一个数组叫做 `record` 用来记录字符串 `s` 里字符出现的次数。

  需要把字符映射到数组也就是哈希表的索引下标上，**因为字符 `a` 到字符 `z` 的 `ASCII` 是 `26` 个连续的数值，所以字符 `a` 映射为下标 `0`，相应的字符 `z` 映射为下标 `25`。** 

- 在遍历字符串 `s` 的时候，**只需要将 `record` 数组中 `s[i] - 'a'` 所在的位置的值做 `+1` 操作即可，并不需要记住字符 `a` 的ASCII，只要求出一个相对数值就可以了。** 这样就将字符串 `s` 中每个字符出现的次数，统计出来了。

  那看一下如何检查字符串 `t` 中是否出现了这些字符，同样在遍历字符串 `t` 的时候，对 `t` 中出现的字符映射哈希表索引上的数值再做 `-1` 的操作。

  那么最后检查一下，**`record` 数组如果有的元素不为零0，说明字符串 `s` 和 `t` 一定是谁多了字符或者谁少了字符，`return false`。** 

  最后如果 `record` 数组所有元素都为 `0`，说明字符串 `s` 和 `t` 是字母异位词，`return true`。

  ```java
  class Solution {
      public boolean isAnagram(String s, String t) {
          if (s.length() != t.length()) { //两个字符串长度不相等
              return false;
          }
          int[] record = new int[26]; //数组默认初始化为0
          //统计字符串 s 中各字符出现的次数
          for (int i = 0; i < s.length(); i++) {
              record[s.charAt(i) - 'a']++;
          }
          //“统计”字符串 t 中各字符出现的次数
          for (int i = 0; i < t.length(); i++) {
              record[t.charAt(i) - 'a']--;
          }
          for (int i = 0; i < record.length; i++) {
              if (record[i] != 0) {
                  return false;
              }
          }
          return true;
      }
  }
  ```

### 2.两个数组的交集

#### 题目

- 给定两个数组 `nums1` 和 `nums2` ，返回 它们的交集 。输出结果中的每个元素一定是 **唯一** 的。我们可以 **不考虑输出结果的顺序** 。

  **示例 1：**

  ```
  输入：nums1 = [1,2,2,1], nums2 = [2,2]
  输出：[2]
  ```

  **示例 2：**

  ```
  输入：nums1 = [4,9,5], nums2 = [9,4,9,8,4]
  输出：[9,4]
  解释：[4,9] 也是可通过的
  ```

  **提示：**

  - `1 <= nums1.length, nums2.length <= 1000`
  - `0 <= nums1[i], nums2[i] <= 1000`

#### 思路

- 注意题目特意说明：**输出结果中的每个元素一定是唯一的，也就是说输出的结果的去重的， 同时可以不考虑输出结果的顺序**。

  用数组来做哈希表是不错的选择，但是要注意，**使用数组来做哈希的题目，是因为题目都限制了数值的大小。**而这道题目没有限制数值的大小，就无法使用数组来做哈希表了。**如果哈希值比较少、特别分散、跨度非常大，使用数组就造成空间的极大浪费。** 

- 考虑使用 `Set` ，因为输出结果是去重的，同时可以不考虑输出顺序。

  ```java
  class Solution {
      //Set解法
      public int[] intersection(int[] nums1, int[] nums2) {
          Set<Integer> set = new HashSet<>();
          Set<Integer> resSet = new HashSet<>();
          //将数组1的元素添加到set中
          for (int i = 0; i < nums1.length; i++) {
              set.add(nums1[i]);
          }
          for (int i = 0; i < nums2.length; i++) {
              if (set.contains(nums2[i])) {   //判断set中是否含有和数组2中的元素 值相同的元素
                  resSet.add(nums2[i]);   //若有则将该元素添加到resSet中
              }
          }
          //将Set转换为数组
          int[] res = new int[resSet.size()];
          int index = 0;
          Iterator it = resSet.iterator();
          while (it.hasNext()) {
              Integer num = (Integer) it.next();
              res[index++] = num;
          }
          return res;
      }
  }
  ```

- 那有同学可能问了，遇到哈希问题我直接都用 `set` 不就得了，用什么数组啊。

  直接使用 `set` 不仅占用空间比数组大，而且速度要比数组慢，`set` 把数值映射到 `key` 上都要做`hash` 计算的。不要小瞧这个耗时，在数据量大的情况，差距是很明显的。

### 3.快乐数

#### 题目

- 编写一个算法来判断一个数 `n` 是不是快乐数。

  **「快乐数」** 定义为：

  - 对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和。
  - 然后重复这个过程直到这个数变为 1，也可能是 **无限循环** 但始终变不到 1。
  - 如果这个过程 **结果为** 1，那么这个数就是快乐数。

  如果 `n` 是 快乐数 就返回 `true` ；不是，则返回 `false` 。

  **示例 1：**

  ```
  输入：n = 19
  输出：true
  解释：
  1^2 + 9^2 = 82
  8^2 + 2^2 = 68
  6^2 + 8^2 = 100
  1^2 + 0^2 + 0^2 = 1
  ```

  **示例 2：**

  ```
  输入：n = 2
  输出：false
  ```

  **提示：**

  - `1 <= n <= 2^31 - 1`

#### 思路

- 这道题目看上去貌似一道数学问题，其实并不是！

  题目中说了会 **无限循环**，那么也就是说求和的过程中，sum 会重复出现，这对解题很重要。当我们遇到了要快速判断一个元素是否出现集合里的时候，就要考虑哈希法了。

  所以这道题目**使用哈希法，来判断这个 sum 是否重复出现**，如果重复了就是 `return false`， 否则一直找到 sum 为 1 为止。

  ```java
  class Solution {
      public boolean isHappy(int n) {
          int num, nextNum;
          Set<Integer> set = new HashSet<>();
          num = n;
          set.add(num);
          while (true) {
              nextNum = getNextNum(num);
              if (nextNum == 1) {
                  return true;
              }
              if (set.contains(nextNum)) {
                  return false;
              }
              num = nextNum;
              set.add(num);
          }
      }
  
      public int getNextNum(int num) {
          int sum = 0;
          while (num != 0) {
              sum += (num % 10) * (num % 10);
              num = num / 10;
          }
          return sum;
      }
  }
  ```

### 4.两数之和

#### 题目

- 给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 **和为目标值** `target` 的那 **两个** 整数，并返回它们的数组下标。

  你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

  你可以按任意顺序返回答案。

  **示例 1：**

  ```
  输入：nums = [2,7,11,15], target = 9
  输出：[0,1]
  解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
  ```

  **示例 2：**

  ```
  输入：nums = [3,2,4], target = 6
  输出：[1,2]
  ```

  **示例 3：**

  ```
  输入：nums = [3,3], target = 6
  输出：[0,1]
  ```

  **提示：**

  - `2 <= nums.length <= 104`
  - `-109 <= nums[i] <= 109`
  - `-109 <= target <= 109`
  - **只会存在一个有效答案**

#### 思路

- 暴力解法：直接两层 for 循环遍历求解。

- **进阶：**你可以想出一个时间复杂度小于 `O(n2)` 的算法吗？

  - 因为要返回两个整数的下标，所以不仅要存这两个整数，还要存他们的下标，用 `Map` 数据结构。

  - 判断两数之和是否为目标值，只需要判断 *目标值 - 当前数* 所得的数是否在 `Map` 中。

    ```java
    class Solution {
        public int[] twoSum(int[] nums, int target) {
            int[] result = new int[2];
            if (nums == null || nums.length < 2) {
                return result;
            }
            HashMap<Integer, Integer> map = new HashMap<>();
            for (int i = 0; i < nums.length; i++) {
                // 判断 target - nums[i] 是否在 map 中
                int another = target - nums[i];
                if (map.containsKey(another)) {
                    result[0] = i;
                    result[1] = map.get(another);
                    return result;
                }
                // 如果不在，则将当前的数保存到 map 中
                map.put(nums[i], i);
            }
            // 找不到这样的两个数
            return result;
        }
    }
    ```

    - 不是一股脑把数全部加到 Map 中，而是边判断边加入

### 5.四数相加II

#### 题目

- 给你四个整数数组 `nums1`、`nums2`、`nums3` 和 `nums4` ，数组长度都是 `n` ，请你计算有多少个元组 `(i, j, k, l)` 能满足：

  - `0 <= i, j, k, l < n`
  - `nums1[i] + nums2[j] + nums3[k] + nums4[l] == 0`

  **示例 1：**

  ```
  输入：nums1 = [1,2], nums2 = [-2,-1], nums3 = [-1,2], nums4 = [0,2]
  输出：2
  解释：
  两个元组如下：
  1. (0, 0, 0, 1) -> nums1[0] + nums2[0] + nums3[0] + nums4[1] = 1 + (-2) + (-1) + 2 = 0
  2. (1, 1, 0, 0) -> nums1[1] + nums2[1] + nums3[0] + nums4[0] = 2 + (-1) + (-1) + 0 = 0
  ```

  **示例 2：**

  ```
  输入：nums1 = [0], nums2 = [0], nums3 = [0], nums4 = [0]
  输出：1
  ```

   **提示：**

  - `n == nums1.length`
  - `n == nums2.length`
  - `n == nums3.length`
  - `n == nums4.length`
  - `1 <= n <= 200`
  - `-228 <= nums1[i], nums2[i], nums3[i], nums4[i] <= 228`

#### 思路

- 直观想法是，四层 for 循环暴力破解，但是肯定是不可能这么做的，时间复杂度会达到 O(n^4)。

- 其实和 `4.两数之和` 有点类似，可以将前两个数组 A、B 用两层 for 循环，将其之和 a+b 添加到 Map 中。

  - Map 的 key 存储的是两数之和，value 存储的是两数之和出现的次数。

  然后对另外两个数组 C、D，看看 `0-(c+d)` 是否在 Map 中有存在，有的话就说明能找到 `a+b+c+d=0`，因为只要求给出满足要求的个数，所以计数就行了。

  ```java
  class Solution {
      public int fourSumCount(int[] nums1, int[] nums2, int[] nums3, int[] nums4) {
          int result = 0;
          // map 用于存储前两个数组元素之和及其出现的次数
          HashMap<Integer, Integer> map = new HashMap<>();
          for (int i = 0; i < nums1.length; i++) {
              for (int j = 0; j < nums2.length; j++) {
                  int sum = nums1[i] + nums2[j];
                  Integer count = map.get(sum);
                  if (count == null) {
                      map.put(sum, 1);
                  } else {
                      map.put(sum, count + 1);
                  }
              }
          }
          // 遍历另外两个数组
          for (int i = 0; i < nums3.length; i++) {
              for (int j = 0; j < nums4.length; j++) {
                  int sum = nums3[i] + nums4[j];
                  if (map.containsKey(0 - sum)) {
                      result += map.get(0 - sum);
                  }
              }
          }
          return result;
      }
  }
  ```

  - 时间复杂度 O(n^2)

### 6.赎金信

#### 题目

- 给你两个字符串：`ransomNote` 和 `magazine` ，判断 `ransomNote` 能不能由 `magazine` 里面的字符构成。

  如果可以，返回 `true` ；否则返回 `false` 。

  **`magazine` 中的每个字符只能在 `ransomNote` 中使用一次**。

  **示例 1：**

  ```
  输入：ransomNote = "a", magazine = "b"
  输出：false
  ```

  **示例 2：**

  ```
  输入：ransomNote = "aa", magazine = "ab"
  输出：false
  ```

  **示例 3：**

  ```
  输入：ransomNote = "aa", magazine = "aab"
  输出：true
  ```

  **提示：**

  - `1 <= ransomNote.length, magazine.length <= 105`
  - `ransomNote` 和 `magazine` 由小写英文字母组成

#### 思路

- 和 `1.有效的字母异位词` 类似，用数组来对字母进行统计。

  ```java
  class Solution {
      public boolean canConstruct(String ransomNote, String magazine) {
          int[] array = new int[26];
          // 统计magazine中的字符
          for (int i = 0; i < magazine.length(); i++) {
              char c = magazine.charAt(i);
              array[c - 'a']++;
          }
          for (int i = 0; i < ransomNote.length(); i++) {
              char c = ransomNote.charAt(i);
              if (array[c - 'a'] == 0) {
                  // 说明magazine中的字符不足以组成ransomNote
                  return false;
              } else {
                  array[c - 'a']--;
              }
          }
          return true;
      }
  }
  ```

### 7.三数之和(去重)

#### 题目

- 给你一个整数数组 `nums` ，判断是否存在三元组 `[nums[i], nums[j], nums[k]]` 满足 `i != j`、`i != k` 且 `j != k` ，同时还满足 `nums[i] + nums[j] + nums[k] == 0` 。请

  你返回所有和为 `0` 且不重复的三元组。

  **注意：**答案中不可以包含重复的三元组。

  **示例 1：**

  ```
  输入：nums = [-1,0,1,2,-1,-4]
  输出：[[-1,-1,2],[-1,0,1]]
  解释：
  nums[0] + nums[1] + nums[2] = (-1) + 0 + 1 = 0 。
  nums[1] + nums[2] + nums[4] = 0 + 1 + (-1) = 0 。
  nums[0] + nums[3] + nums[4] = (-1) + 2 + (-1) = 0 。
  不同的三元组是 [-1,0,1] 和 [-1,-1,2] 。
  注意，输出的顺序和三元组的顺序并不重要。
  ```

  **示例 2：**

  ```
  输入：nums = [0,1,1]
  输出：[]
  解释：唯一可能的三元组和不为 0 。
  ```

  **示例 3：**

  ```
  输入：nums = [0,0,0]
  输出：[[0,0,0]]
  解释：唯一可能的三元组和为 0 。
  ```

  **提示：**

  - `3 <= nums.length <= 3000`
  - `-105 <= nums[i] <= 105`

#### 思路

- 在这里用哈希表的方法不是不行，但是会比较麻烦，因为我们需要考虑去重的问题。

  - 我们用双指针法解决该题，前提是要先把 `nums` 数组进行升序排序。

  ```java
  class Solution {
      public List<List<Integer>> threeSum(int[] nums) {
          List<List<Integer>> result = new ArrayList<>();
          int leftIndex, rightIndex;
          Arrays.sort(nums); // 将数组升序排序
          for (int i = 0; i < nums.length; i++) {
              if (nums[i] > 0) { // 剪枝
                  break;
              }
              // 求a+b+c=0，a=nums[i], b=nums[leftIndex], c=nums[rightIndex]
              if (i > 0 && nums[i] == nums[i - 1]) { // 对a去重
                  continue;
              }
              leftIndex = i + 1;
              rightIndex = nums.length - 1;
              while (leftIndex < rightIndex) { // 不可能重合，所以不是<=
                  int sum = nums[i] + nums[leftIndex] + nums[rightIndex];
                  if (sum > 0) { // 说明nums[rightIndex]大了
                      rightIndex--;
                  } else if (sum < 0) { // 说明nums[leftIndex]小了
                      leftIndex++;
                  } else { // 找到了
                      List<Integer> tmpt = new ArrayList<>();
                      tmpt.add(nums[i]);
                      tmpt.add(nums[leftIndex]);
                      tmpt.add(nums[rightIndex]);
                      result.add(tmpt);
                      leftIndex++;
                      rightIndex--;
                      while (leftIndex < rightIndex && nums[leftIndex - 1] == nums[leftIndex]) { // 对b去重
                          leftIndex++;
                      }
                      while (leftIndex < rightIndex && nums[rightIndex + 1] == nums[rightIndex]) { // 对c去重
                          rightIndex--;
                      }
                  }
              }
          }
          return result;
      }
  }
  ```

  - 要注意去重的逻辑。

### 8.★四数之和(去重)

#### 题目

- 给你一个由 `n` 个整数组成的数组 `nums` ，和一个目标值 `target` 。请你找出并返回满足下述全部条件且**不重复**的四元组 `[nums[a], nums[b], nums[c], nums[d]]` （若两个四元组元素一一对应，则认为两个四元组重复）：

  - `0 <= a, b, c, d < n`
  - `a`、`b`、`c` 和 `d` **互不相同**
  - `nums[a] + nums[b] + nums[c] + nums[d] == target`

  你可以按 **任意顺序** 返回答案 。

  **示例 1：**

  ```
  输入：nums = [1,0,-1,0,-2,2], target = 0
  输出：[[-2,-1,1,2],[-2,0,0,2],[-1,0,0,1]]
  ```

  **示例 2：**

  ```
  输入：nums = [2,2,2,2,2], target = 8
  输出：[[2,2,2,2]]
  ```

  **提示：**

  - `1 <= nums.length <= 200`
  - `-109 <= nums[i] <= 109`
  - `-109 <= target <= 109`

#### 思路

- 思路和三数之和类似，只是在外面又嵌套了一层 for 循环。

  ```java
  class Solution {
      public List<List<Integer>> fourSum(int[] nums, int target) {
          List<List<Integer>> result = new ArrayList<>();
          int leftIndex, rightIndex;
          if (nums.length < 4) {
              return result;
          }
          Arrays.sort(nums);
          // 计算a+b+c+d=target,a=nums[i], b=nums[j], c=nums[leftIndex], d=nums[rightIndex]
          for (int i = 0; i < nums.length; i++) {
              if (nums[i] > 0 && nums[i] > target) { // 剪枝
                  break;
              }
              if (i > 0 && nums[i - 1] == nums[i]) { // 对a去重
                  continue;
              }
              for (int j = i + 1; j < nums.length; j++) {
                  if (j > i + 1 && nums[j - 1] == nums[j]) { // 对b去重
                      continue;
                  }
                  leftIndex = j + 1;
                  rightIndex = nums.length - 1;
                  while (leftIndex < rightIndex) {
                      int sum = nums[i] + nums[j] + nums[leftIndex] + nums[rightIndex];
                      if (sum > target) { // nums[rightIndex]大了
                          rightIndex--;
                      } else if (sum < target) { // nums[leftIndex]小了
                          leftIndex++;
                      } else { // 找到了
                          result.add(Arrays.asList(nums[i], nums[j], nums[leftIndex], nums[rightIndex]));
                          leftIndex++;
                          rightIndex--;
                          while (leftIndex < rightIndex && nums[leftIndex - 1] == nums[leftIndex]) { // 对c去重
                              leftIndex++;
                          }
                          while (leftIndex < rightIndex && nums[rightIndex + 1] == nums[rightIndex]) { // 对d去重
                              rightIndex--;
                          }
                      }
                  }
              }
          }
          return result;
      }
  }
  ```

  - 在这里注意一下对 b 的去重，要加上 `j > i + 1` 的逻辑判断。

### 总结篇

- **一般来说哈希表都是用来快速判断一个元素是否出现集合里**。

  对于哈希表，要知道哈希函数和哈希碰撞在哈希表中的作用。

  - **哈希函数**是把传入的key映射到符号表的索引上。
  - **哈希碰撞**处理有多个key映射到相同索引上时的情景，处理碰撞的普遍方式是拉链法和线性探测法。

- 我们一般可以采用数组、Set、Map 作为 “哈希表”

  - **数组**适合作为大小受限的 “哈希表”，例如对只有 26 个英文字母做哈希表

    如果大小没有受限，就不太适合用数组来做哈希表了。

    - 一是数组的大小是有限的，受到系统栈空间（不是数据结构的栈）的限制。
    - 二是如果数组空间够大，但哈希值比较少、特别分散、跨度非常大，使用数组就造成空间的极大浪费。

  - 对于没有限制数值大小的情况，可以用 **Set** 来做哈希表。

  - **Map** 是一种 `<key, value>` 的结构，可以用 `key` 保存数值，用 `value` 保存其他有关的值。

## 字符串

### 1.反转字符串

#### 题目

- 编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组 `s` 的形式给出。

  不要给另外的数组分配额外的空间，你必须**[原地](https://baike.baidu.com/item/原地算法)修改输入数组**、使用 O(1) 的额外空间解决这一问题。

  **示例 1：**

  ```
  输入：s = ["h","e","l","l","o"]
  输出：["o","l","l","e","h"]
  ```

  **示例 2：**

  ```
  输入：s = ["H","a","n","n","a","h"]
  输出：["h","a","n","n","a","H"]
  ```

  **提示：**

  - `1 <= s.length <= 105`
  - `s[i]` 都是 [ASCII](https://baike.baidu.com/item/ASCII) 码表中的可打印字符

#### 思路

- 没啥好说的，用一个临时变量辅助对两个值进行互换。

  ```java
  class Solution {
      public void reverseString(char[] s) {
          char c;
          for (int i = 0; i < s.length / 2; i++) {
              c = s[i];
              s[i] = s[s.length - i - 1];
              s[s.length - i - 1] = c;
          }
      }
  }
  ```

### 2.反转字符串II

#### 题目

- 给定一个字符串 `s` 和一个整数 `k`，从字符串开头算起，每计数至 `2k` 个字符，就反转这 `2k` 字符中的前 `k` 个字符。

  - 如果剩余字符少于 `k` 个，则将剩余字符全部反转。
  - 如果剩余字符小于 `2k` 但大于或等于 `k` 个，则反转前 `k` 个字符，其余字符保持原样。

  **示例 1：**

  ```
  输入：s = "abcdefg", k = 2
  输出："bacdfeg"
  ```

  **示例 2：**

  ```
  输入：s = "abcd", k = 2
  输出："bacd"
  ```

  **提示：**

  - `1 <= s.length <= 104`
  - `s` 仅由小写英文组成
  - `1 <= k <= 104`

#### 思路

- 在遍历字符串的过程中，只要让 `i += (2 * k)`，i 每次移动 2 * k 就可以了，然后判断是否需要有反转的区间。

  因为要找的也就是每2 * k 区间的起点，这样写，程序会高效很多。

  **所以当需要固定规律一段一段去处理字符串的时候，要想想在在for循环的表达式上做做文章。**

  ```java
  class Solution {
      public String reverseStr(String s, int k) {
          char[] charArray = s.toCharArray();
          for (int i = 0; i < charArray.length; i += 2 * k) {
              int start = i;
              int end = Math.min(charArray.length - 1, start + k - 1);
              while (start < end) {
                  char c;
                  c = charArray[start];
                  charArray[start] = charArray[end];
                  charArray[end] = c;
                  start++;
                  end--;
              }
          }
          return new String(charArray);
      }
  }
  ```

### 3.翻转字符串里的单词

#### 题目

- 给你一个字符串 `s` ，请你反转字符串中 **单词** 的顺序。

  **单词** 是由非空格字符组成的字符串。`s` 中使用至少一个空格将字符串中的 **单词** 分隔开。

  返回 **单词** 顺序颠倒且 **单词** 之间用单个空格连接的结果字符串。

  **注意：**输入字符串 `s`中可能会存在前导空格、尾随空格或者单词间的多个空格。返回的结果字符串中，单词间应当仅用单个空格分隔，且不包含任何额外的空格。

  **示例 1：**

  ```
  输入：s = "the sky is blue"
  输出："blue is sky the"
  ```

  **示例 2：**

  ```
  输入：s = "  hello world  "
  输出："world hello"
  解释：反转后的字符串中不能存在前导空格和尾随空格。
  ```

  **示例 3：**

  ```
  输入：s = "a good   example"
  输出："example good a"
  解释：如果两个单词间有多余的空格，反转后的字符串需要将单词间的空格减少到仅有一个。
  ```

  **提示：**

  - `1 <= s.length <= 104`
  - `s` 包含英文大小写字母、数字和空格 `' '`
  - `s` 中 **至少存在一个** 单词

#### 思路

- 如果用现有的库函数的话，这题其实很简单：

  ```java
  class Solution {
      public String reverseWords(String s) {
          StringBuilder sb = new StringBuilder();
          s = s.trim(); // 处理两头的空格
          String[] array = s.split("\\s+"); // 单词分割
          for (int i = array.length - 1; i >= 0; i--) {
              sb.append(array[i]);
              if (i > 0) {
                  sb.append(' ');
              }
          }
          return sb.toString();
      }
  }
  ```

- 进阶：不要使用现有的 trim 和 split 函数，自己完成具体的细节。

  ```java
  class Solution {
      public String reverseWords(String s) {
          StringBuilder sb = new StringBuilder();
          List<String> list;
          s = trimSpace(s);
          list = splitString(s);
          while (!list.isEmpty()) {
              String word = list.remove(list.size() - 1);
              sb.append(word);
              if (!list.isEmpty()) {
                  sb.append(' ');
              }
          }
          return sb.toString();
      }
  
      // 移除两端的空格
      public String trimSpace(String s) {
          int left, right;
          left = 0;
          right = s.length() - 1;
          while (s.charAt(left) == ' ') {
              left++;
          }
          while (s.charAt(right) == ' ') {
              right--;
          }
          return s.substring(left, right + 1);
      }
  
      // 提取出空格分隔的单词
      public List<String> splitString(String s) {
          List<String> list = new ArrayList<>();
          int start, end;
          start = end = 0;
          while (start < s.length()) {
              while (s.charAt(end) != ' ') {
                  end++;
                  if (end == s.length()) {
                      break;
                  }
              }
              String word = s.substring(start, end);
              list.add(word);
              if (end == s.length()) {
                  break;
              }
              while (s.charAt(end) == ' ') {
                  end++;
                  // 因为已经移除两端的空格了，所以不需要下面的判断
                  // if (end == s.length()) {
                  //     break;
                  // }
              }
              start = end;
          }
          return list;
      }
  }
  ```

  - 实际做题还是优先用库函数。

### 4.★实现strStr(KMP)

#### 题目

- 给你两个字符串 `haystack` 和 `needle` ，请你在 `haystack` 字符串中找出 `needle` 字符串的第一个匹配项的下标（下标从 0 开始）。如果 `needle` 不是 `haystack` 的一部分，则返回 `-1` 。

  **示例 1：**

  ```
  输入：haystack = "sadbutsad", needle = "sad"
  输出：0
  解释："sad" 在下标 0 和 6 处匹配。
  第一个匹配项的下标是 0 ，所以返回 0 。
  ```

  **示例 2：**

  ```
  输入：haystack = "leetcode", needle = "leeto"
  输出：-1
  解释："leeto" 没有在 "leetcode" 中出现，所以返回 -1 。
  ```

  **提示：**

  - `1 <= haystack.length, needle.length <= 104`
  - `haystack` 和 `needle` 仅由小写英文字符组成

#### 思路

- 对于判断是否是子串的问题，很明显是用 KMP 算法来做。
  - KMP 的经典思想就是：当出现字符串不匹配时，可以记录一部分之前已经匹配的文本内容，利用这些信息避免从头再去做匹配。

- 写过 KMP 的同学，一定都写过 next 数组，那么这个 next 数组究竟是个啥呢？

  - next 数组就是一个前缀表（prefix table）。前缀表是用来回退的，它记录了模式串与主串(文本串)不匹配的时候，模式串应该从哪里开始重新匹配。
  - 首先要知道前缀表的任务是当前位置匹配失败，找到之前已经匹配上的位置，再重新匹配，此也意味着在某个字符失配时，前缀表会告诉你下一步匹配中，应该跳到哪个位置，再进行模式串匹配。

- **所以 KMP 算法分为两步：**

  - **获得 next 数组。**
  - **使用 next 数组进行匹配。**

  ```java
  class Solution {
      public int strStr(String haystack, String needle) {
          int[] next = getNext(needle);
          int i, j;
          i = j = 0;
          while (i < haystack.length() && j < needle.length()) {
              if (haystack.charAt(i) == needle.charAt(j)) {
                  i++;
                  j++;
              } else if (j > 0) {
                  j = next[j - 1] + 1;
              } else {
                  i++;	// 说明模式串的第一个字符就没匹配，所以文本串的下标向后移动一位
              }
          }
          return j == needle.length() ? (i - j) : -1;
      }
  
      // 获取模式串的next数组
      public int[] getNext(String needle) {
          int[] next = new int[needle.length()];
          next[0] = -1;
          for (int i = 1; i < needle.length(); i++) {
              int tmpt = next[i - 1];
              while (tmpt >= 0 && needle.charAt(tmpt + 1) != needle.charAt(i)) {
                  tmpt = next[tmpt];
              }
              if (needle.charAt(tmpt + 1) == needle.charAt(i)) {
                  next[i] = tmpt + 1;
              } else {
                  next[i] = -1;
              }
          }
          return next;
      }
  }
  ```

### 5.重复的子字符串

#### 题目

- 给定一个非空的字符串 `s` ，检查是否可以通过由它的一个子串重复多次构成。

  **示例 1:**

  ```
  输入: s = "abab"
  输出: true
  解释: 可由子串 "ab" 重复两次构成。
  ```

  **示例 2:**

  ```
  输入: s = "aba"
  输出: false
  ```

  **示例 3:**

  ```
  输入: s = "abcabcabcabc"
  输出: true
  解释: 可由子串 "abc" 重复四次构成。 (或子串 "abcabc" 重复两次构成。)
  ```

  **提示：**

  - `1 <= s.length <= 104`
  - `s` 由小写英文字母组成

#### 思路

- 如果一个字符串能由它的子串重复多次构成，那它的子串一定是可以从开头开始截取的，所以只需要从开头开始截取不同的子串，再进行判断。

  - 所以一个 for 循环获取子串的终止位置就行了。 而且遍历的时候都不用遍历结束，只需要遍历到中间位置，因为子串结束位置大于中间位置的话，一定不能重复组成字符串。下面就是暴力破解的代码：

    ```java
    class Solution {
        public boolean repeatedSubstringPattern(String s) {
            // 暴力解法
            for (int i = 0; i < s.length() / 2; i++) {
                String sub = s.substring(0, i + 1);
                if (s.length() % sub.length() != 0) {
                    continue;
                }
                int index = 0;
                int loop = s.length() / sub.length();
                int start;
                while (index < loop) {
                    start = index * sub.length();
                    if (s.substring(start, start + sub.length()).equals(sub)) {
                        index++;
                        continue;
                    } else {
                        break;
                    }
                }
                if (index == loop) {
                    return true;
                }
            }
            return false;
        }
    }
    ```

- 这题还能用 KMP 算法求解。

  - 主要是利用 next 数组求解。

    ```java
    class Solution {
        public boolean repeatedSubstringPattern(String s) {
            int[] next = getNext(s);
            int len = s.length();
            // (len - next[len - 1] - 1)表示周期的长度
            // 数组长度减去最长相同前后缀的长度相当于是一个周期的长度，如果这个周期长度可以被数组长度整除，
            // 就说明整个数组就是这个周期的循环。
            if (next[len - 1] >= 0 && len % (len - next[len - 1] - 1) == 0) {
                return true;
            }
            return false;
        }
    
        // 获取next数组
        public int[] getNext(String s) {
            int[] next = new int[s.length()];
            next[0] = -1;
            for (int i = 1; i < s.length(); i++) {
                int tmpt = next[i - 1];
                while (tmpt >= 0 && s.charAt(tmpt + 1) != s.charAt(i)) {
                    tmpt = next[tmpt];
                }
                if (s.charAt(tmpt + 1) == s.charAt(i)) {
                    next[i] = tmpt + 1;
                } else {
                    next[i] = -1;
                }
            }
            return next;
        }
    }
    ```

### 总结篇

- 字符串类型的题目，往往想法比较简单，但是实现起来并不容易，复杂的字符串题目非常考验对代码的掌控能力。
  - 双指针法是字符串处理的常客。
  - KMP 算法是字符串查找最重要的算法，**要知道怎么获取 next 数组，并且如何使用它**。

## 栈与队列

### 栈与队列理论基础

- 队列是先进先出，入口和出口不是同一个。
- 栈是先进后出，入口和出口是同一个。

### 1.用栈实现队列

#### 题目

- 请你仅使用两个栈实现先入先出队列。队列应当支持一般队列支持的所有操作（`push`、`pop`、`peek`、`empty`）：

  实现 `MyQueue` 类：

  - `void push(int x)` 将元素 x 推到队列的末尾
  - `int pop()` 从队列的开头移除并返回元素
  - `int peek()` 返回队列开头的元素
  - `boolean empty()` 如果队列为空，返回 `true` ；否则，返回 `false`

  **说明：**

  - 你 **只能** 使用标准的栈操作 —— 也就是只有 `push to top`, `peek/pop from top`, `size`, 和 `is empty` 操作是合法的。
  - 你所使用的语言也许不支持栈。你可以使用 list 或者 deque（双端队列）来模拟一个栈，只要是标准的栈操作即可。

  **示例 1：**

  ```
  输入：
  ["MyQueue", "push", "push", "peek", "pop", "empty"]
  [[], [1], [2], [], [], []]
  输出：
  [null, null, null, 1, 1, false]
  
  解释：
  MyQueue myQueue = new MyQueue();
  myQueue.push(1); // queue is: [1]
  myQueue.push(2); // queue is: [1, 2] (leftmost is front of the queue)
  myQueue.peek(); // return 1
  myQueue.pop(); // return 1, queue is [2]
  myQueue.empty(); // return false
  ```

  **提示：**

  - `1 <= x <= 9`
  - 最多调用 `100` 次 `push`、`pop`、`peek` 和 `empty`
  - 假设所有操作都是有效的 （例如，一个空的队列不会调用 `pop` 或者 `peek` 操作）

#### 思路

- 使用栈来模式队列的行为，如果仅仅用一个栈，是一定不行的，所以需要两个栈**一个输入栈，一个输出栈**，这里要注意输入栈和输出栈的关系。

  - 在 push 数据的时候，只要数据放进输入栈就好。
  - 在 pop 的时候，操作就复杂一些，**输出栈如果为空，就要先把输入栈的数据全部导出到输出栈中，再从输出栈弹出数据**；如果输出栈不为空，则直接从出栈弹出数据就可以了。

  ```java
  class MyQueue {
      public List<Integer> stackIn;
      public List<Integer> stackOut;
  
      public MyQueue() {
          stackIn = new ArrayList<>(); // 输入栈
          stackOut = new ArrayList<>(); // 输出栈
      }
  
      public void push(int x) {
          stackIn.add(x);
      }
  
      public int pop() {
          if (!stackOut.isEmpty()) {
              return stackOut.remove(stackOut.size() - 1);
          }
          transfer();
          return stackOut.remove(stackOut.size() - 1);
      }
  
      public int peek() {
          if (!stackOut.isEmpty()) {
              return stackOut.get(stackOut.size() - 1);
          }
          transfer();
          return stackOut.get(stackOut.size() - 1);
      }
  
      public boolean empty() {
          return stackIn.isEmpty() && stackOut.isEmpty();
      }
  
      public void transfer() {
          // 将输入栈的数据全部导出到输出栈中
          while (!stackIn.isEmpty()) {
              stackOut.add(stackIn.remove(stackIn.size() - 1));
          }
      }
  }
  
  /**
   * Your MyQueue object will be instantiated and called as such:
   * MyQueue obj = new MyQueue();
   * obj.push(x);
   * int param_2 = obj.pop();
   * int param_3 = obj.peek();
   * boolean param_4 = obj.empty();
   */
  ```

### 2.用队列实现栈

#### 题目

- 请你仅使用两个队列实现一个后入先出（LIFO）的栈，并支持普通栈的全部四种操作（`push`、`top`、`pop` 和 `empty`）。

  实现 `MyStack` 类：

  - `void push(int x)` 将元素 x 压入栈顶。
  - `int pop()` 移除并返回栈顶元素。
  - `int top()` 返回栈顶元素。
  - `boolean empty()` 如果栈是空的，返回 `true` ；否则，返回 `false` 。

  **注意：**

  - 你只能使用队列的标准操作 —— 也就是 `push to back`、`peek/pop from front`、`size` 和 `is empty` 这些操作。
  - 你所使用的语言也许不支持队列。 你可以使用 list （列表）或者 deque（双端队列）来模拟一个队列 , 只要是标准的队列操作即可。

  **示例：**

  ```
  输入：
  ["MyStack", "push", "push", "top", "pop", "empty"]
  [[], [1], [2], [], [], []]
  输出：
  [null, null, null, 2, 2, false]
  
  解释：
  MyStack myStack = new MyStack();
  myStack.push(1);
  myStack.push(2);
  myStack.top(); // 返回 2
  myStack.pop(); // 返回 2
  myStack.empty(); // 返回 False
  ```

  **提示：**

  - `1 <= x <= 9`
  - 最多调用`100` 次 `push`、`pop`、`top` 和 `empty`
  - 每次调用 `pop` 和 `top` 都保证栈不为空

#### 思路

- 和 `1.用栈实现队列` 不同，另一个队列完全是用来备份的。

  - 用两个队列 que1 和 que2 实现队列的功能，que2 其实完全就是一个备份的作用，把 que1 最后面的元素以外的元素都备份到 que2，然后弹出最后面的元素，再把其他元素从 que2 导回 que1。

    代码就不写了。

- **进阶：**用一个队列来实现栈。

  - 只需要把最后面的元素以外的元素重新导入到队列中即可。

  ```java
  class MyStack {
      public List<Integer> queue;
  
      public MyStack() {
          queue = new ArrayList();
      }
  
      public void push(int x) {
          queue.add(x);
      }
  
      public int pop() {
          transfer();
          return queue.remove(0);
      }
  
      public int top() {
          transfer();
          int r = queue.get(0);
          queue.add(queue.remove(0));
          return r;
      }
  
      public boolean empty() {
          return queue.isEmpty();
      }
  
      public void transfer() {
          int tmpt;
          for (int i = 0; i < queue.size() - 1; i++) {
              tmpt = queue.remove(0);
              queue.add(tmpt);
          }
      }
  }
  
  /**
   * Your MyStack object will be instantiated and called as such:
   * MyStack obj = new MyStack();
   * obj.push(x);
   * int param_2 = obj.pop();
   * int param_3 = obj.top();
   * boolean param_4 = obj.empty();
   */
  ```

### 3.有效的括号

#### 题目

- 给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串 `s` ，判断字符串是否有效。

  有效字符串需满足：

  1. 左括号必须用相同类型的右括号闭合。
  2. 左括号必须以正确的顺序闭合。
  3. 每个右括号都有一个对应的相同类型的左括号。

  **示例 1：**

  ```
  输入：s = "()"
  输出：true
  ```

  **示例 2：**

  ```
  输入：s = "()[]{}"
  输出：true
  ```

  **示例 3：**

  ```
  输入：s = "(]"
  输出：false
  ```

  **提示：**

  - `1 <= s.length <= 104`
  - `s` 仅由括号 `'()[]{}'` 组成

#### 思路

- **括号匹配是使用栈解决的经典问题。**

  - 建议在写代码之前要分析好有哪几种不匹配的情况，如果不在动手之前分析好，写出的代码也会有很多问题。

    - 第一种情况，字符串里左方向的括号多余了 ，所以不匹配。
      - 已经遍历完了字符串，但是栈不为空，说明有相应的左括号没有右括号来匹配，所以 return false
    - 第二种情况，括号没有多余，但是括号的类型没有匹配上。
      - 遍历字符串匹配的过程中，发现栈里没有要匹配的字符，所以 return false
    - 第三种情况，字符串里右方向的括号多余了，所以不匹配。
      - 遍历字符串匹配的过程中，栈已经为空了，没有匹配的字符了，说明右括号没有找到对应的左括号 return false

    ```java
    class Solution {
        public Stack<Character> stack = new Stack<>();
    
        public boolean isValid(String s) {
            char c;
            for (int i = 0; i < s.length(); i++) {
                c = s.charAt(i);
                if (c == '(' || c == '{' || c == '[') {
                    stack.push(c);
                } else if (stack.isEmpty()) {
                    return false;
                } else if (c == ')') {
                    if (stack.pop() != '(') {
                        return false;
                    }
                } else if (c == '}') {
                    if (stack.pop() != '{') {
                        return false;
                    }
                } else if (c == ']') {
                    if (stack.pop() != '[') {
                        return false;
                    }
                }
            }
            return stack.isEmpty();
        }
    }
    ```

    - 上面是将左括号入栈的写法，可以看到不是很简练。下面是看到左括号，就将相应的右括号入栈的写法：

      ```java
      class Solution {
          public Stack<Character> stack = new Stack<>();
      
          public boolean isValid(String s) {
              char c, tmpt;
              for (int i = 0; i < s.length(); i++) {
                  c = s.charAt(i);
                  if (c == '(') {
                      stack.push(')');
                  } else if (c == '{') {
                      stack.push('}');
                  } else if (c == '[') {
                      stack.push(']');
                  } else if (stack.isEmpty() || stack.peek() != c) {
                      return false;
                  } else {
                      stack.pop();
                  }
              }
              return stack.isEmpty();
          }
      }
      ```

- **由于栈结构的特殊性，非常适合做对称匹配类的题目**。

### 4.删除字符串中的所有相邻重复项

#### 题目

- 给出由小写字母组成的字符串 `S`，重复项删除操作会选择两个相邻且相同的字母，并删除它们。

  在 S 上反复执行重复项删除操作，直到无法继续删除。

  在完成所有重复项删除操作后返回最终的字符串。答案保证唯一。

  **示例：**

  ```
  输入："abbaca"
  输出："ca"
  解释：
  例如，在 "abbaca" 中，我们可以删除 "bb" 由于两字母相邻且相同，这是此时唯一可以执行删除操作的重复项。之后我们得到字符串 "aaca"，其中又只有 "aa" 可以执行重复项删除操作，所以最后的字符串为 "ca"。
  ```

  **提示：**

  1. `1 <= S.length <= 20000`
  2. `S` 仅由小写英文字母组成。

#### 思路

- 非常适合用栈来解决。

  ```java
  class Solution {
      public ArrayDeque<Character> deque = new ArrayDeque<>();
  
      public String removeDuplicates(String s) {
          StringBuilder sb = new StringBuilder();
          char c;
          for (int i = 0; i < s.length(); i++) {
              c = s.charAt(i);
              if (!deque.isEmpty() && c == deque.peek()) {
                  deque.pop();
              } else {
                  deque.push(c);
              }
          }
          while (!deque.isEmpty()) {
              c = deque.pop();
              sb.append(c);
          }
          return sb.reverse().toString();
      }
  }
  ```

### 5.逆波兰表达式求值

#### 题目

- 给你一个字符串数组 `tokens` ，表示一个根据 [逆波兰表示法](https://baike.baidu.com/item/逆波兰式/128437) 表示的算术表达式。

  请你计算该表达式。返回一个表示表达式值的整数。

  **注意：**

  - 有效的算符为 `'+'`、`'-'`、`'*'` 和 `'/'` 。
  - 每个操作数（运算对象）都可以是一个整数或者另一个表达式。
  - 两个整数之间的除法总是 **向零截断** 。
  - 表达式中不含除零运算。
  - 输入是一个根据逆波兰表示法表示的算术表达式。
  - 答案及所有中间计算结果可以用 **32 位** 整数表示。

  **示例 1：**

  ```
  输入：tokens = ["2","1","+","3","*"]
  输出：9
  解释：该算式转化为常见的中缀算术表达式为：((2 + 1) * 3) = 9
  ```

  **示例 2：**

  ```
  输入：tokens = ["4","13","5","/","+"]
  输出：6
  解释：该算式转化为常见的中缀算术表达式为：(4 + (13 / 5)) = 6
  ```

  **示例 3：**

  ```
  输入：tokens = ["10","6","9","3","+","-11","*","/","*","17","+","5","+"]
  输出：22
  解释：该算式转化为常见的中缀算术表达式为：
    ((10 * (6 / ((9 + 3) * -11))) + 17) + 5
  = ((10 * (6 / (12 * -11))) + 17) + 5
  = ((10 * (6 / -132)) + 17) + 5
  = ((10 * 0) + 17) + 5
  = (0 + 17) + 5
  = 17 + 5
  = 22
  ```

  **提示：**

  - `1 <= tokens.length <= 104`
  - `tokens[i]` 是一个算符（`"+"`、`"-"`、`"*"` 或 `"/"`），或是在范围 `[-200, 200]` 内的一个整数

  **逆波兰表达式：**

  逆波兰表达式是一种后缀表达式，所谓后缀就是指算符写在后面。

  - 平常使用的算式则是一种中缀表达式，如 `( 1 + 2 ) * ( 3 + 4 )` 。
  - 该算式的逆波兰表达式写法为 `( ( 1 2 + ) ( 3 4 + ) * )` 。

  逆波兰表达式主要有以下两个优点：

  - 去掉括号后表达式无歧义，上式即便写成 `1 2 + 3 4 + * `也可以依据次序计算出正确结果。
  - 适合用栈操作运算：遇到数字则入栈；遇到算符则取出栈顶两个数字进行计算，并将结果压入栈中

#### 思路

- 没啥好说的，数据结构课程的内容。

  ```java
  class Solution {
      public ArrayDeque<Integer> deque = new ArrayDeque<>();
  
      public int evalRPN(String[] tokens) {
          String s;
          Integer i1, i2, num;
          for (int i = 0; i < tokens.length; i++) {
              s = tokens[i];
              if (s.equals("+")) {
                  num = (deque.pop() + deque.pop());
                  deque.push(num);
              } else if (s.equals("-")) {
                  num = (-deque.pop() + deque.pop());
                  deque.push(num);
              } else if (s.equals("*")) {
                  num = (deque.pop() * deque.pop());
                  deque.push(num);
              } else if (s.equals("/")) {
                  i1 = deque.pop();
                  i2 = deque.pop();
                  num = (i2 / i1);
                  deque.push(num);
              } else {
                  deque.push(Integer.parseInt(s));
              }
          }
          return deque.pop();
      }
  }
  ```

### 6.滑动窗口最大值(单调队列)

#### 题目

- 给你一个整数数组 `nums`，有一个大小为 `k` 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 `k` 个数字。滑动窗口每次只向右移动一位。

  返回 滑动窗口中的最大值 。

  **示例 1：**

  ```
  输入：nums = [1,3,-1,-3,5,3,6,7], k = 3
  输出：[3,3,5,5,6,7]
  解释：
  滑动窗口的位置                最大值
  ---------------               -----
  [1  3  -1] -3  5  3  6  7       3
   1 [3  -1  -3] 5  3  6  7       3
   1  3 [-1  -3  5] 3  6  7       5
   1  3  -1 [-3  5  3] 6  7       5
   1  3  -1  -3 [5  3  6] 7       6
   1  3  -1  -3  5 [3  6  7]      7
  ```

  **示例 2：**

  ```
  输入：nums = [1], k = 1
  输出：[1]
  ```

  **提示：**

  - `1 <= nums.length <= 105`
  - `-104 <= nums[i] <= 104`
  - `1 <= k <= nums.length`

#### 思路

- 这是使用单调队列的经典题目。

  - 我们需要一个队列，这个队列放入窗口里的元素，然后随着窗口的移动，队列中的元素也一进一出，每次移动之后，队列告诉我们里面的最大值是什么。

    - 再分析一下，根据队列 FIFO 的特性，队列里的元素一定是要排序的，而且要最大值放在出队口，要不然怎么知道最大值呢。但是我们在往队列里添加元素的时候，未必能让当前窗口中最大的元素放在出队口。

    - 所以，其实**队列没有必要维护窗口里的所有元素，只需要维护有可能成为窗口里最大值的元素就可以了，保证有可能成为窗口里最大值的元素在出队口即可**。这个维护元素单调递减的队列就叫做**单调队列，即单调递减或单调递增的队列。** 

    - 举个例子，以题目示例为例，输入: nums = [1, 3, -1, -3, 5, 3, 6, 7] 和 k = 3。

      [1  3  -1] -3  5  3  6  7     3			1入队，3入队，因为 3 > 1，所以 1 出队，然后 -1 入队。最后出队口元素为 3。
       1 [3  -1  -3] 5  3  6  7     3			因为窗口滑动，所以 1 出队，但 1 已经被出队了，接着 -3 入队。最后出队口元素为 3。
       1  3 [-1  -3  5] 3  6  7     5			因为窗口滑动，所以 3 出队，接着 5 入队，因为 5 > -1，5 > -3，所以 -1、-3 出队。最后出队口元素为 5。
       1  3  -1 [-3  5  3] 6  7     5			因为窗口滑动，所以 -1 出队，但 -1 已经被出队了，接着 3 入队。最后出队口元素为 5。
       1  3  -1  -3 [5  3  6] 7     6			因为窗口滑动，所以 -3 出队，但 -3 已经被出队了，接着 6 入队，因为 6 > 5，6 > 3，所以 5、3 出队。最后出队口元素为 6。
       1  3  -1  -3  5 [3  6  7]    7			因为窗口滑动，所以 5 出队，但 5 已经被出队了，接着 7 入队，因为 7 > 6，所以 6 出队。最后出队口元素为7。

  ```java
  class Solution {
      public int[] maxSlidingWindow(int[] nums, int k) {
          int[] result = new int[nums.length - k + 1];
          int index = 0;
          MyQueue queue = new MyQueue();
          // 初始化queue
          for (int i = 0; i < k; i++) {
              queue.push(nums[i]);
          }
          result[index++] = queue.peek();
          for (int i = k; i < nums.length; i++) {
              // 窗口开始滑动
              queue.pop(nums[i - k]);
              queue.push(nums[i]);
              result[index++] = queue.peek();
          }
          return result;
      }
  }
  
  // 自己实现单调队列
  class MyQueue {
      private Deque<Integer> deque = new ArrayDeque<>();
  
      public void push(Integer elem) {
          // 在push新元素之前，将队列中所有小于它的都先从队列中去除
          // 从后面remove，因为存在这种情况：比如此时队列元素为3,1，然后2将要入队，比1大，所以1弹出，此时队列：3,2
          while (!deque.isEmpty() && deque.peekLast() < elem) {
              deque.removeLast();
          }
          deque.addLast(elem);
      }
  
      public Integer pop(Integer elem) {
          Integer i = this.peek();
          if (i.equals(elem)) { // 因为元素有可能在push方法中被提前pop掉了
              return deque.removeFirst();
          }
          return null;
      }
  
      public Integer peek() {
          return deque.peekFirst();
      }
  }
  ```

  - **要注意在新元素入队之前，将队列中小于它的都先从队列中去除，而且是从后面 remove**。

### 7.★前 K 个高频元素(堆)

#### 题目

- 给你一个整数数组 `nums` 和一个整数 `k` ，请你返回其中出现频率前 `k` 高的元素。你可以按 **任意顺序** 返回答案。

  **示例 1:**

  ```
  输入: nums = [1,1,1,2,2,3], k = 2
  输出: [1,2]
  ```

  **示例 2:**

  ```
  输入: nums = [1], k = 1
  输出: [1]
  ```

  **提示：**

  - `1 <= nums.length <= 105`
  - `k` 的取值范围是 `[1, 数组中不相同的元素的个数]`
  - 题目数据保证答案唯一，换句话说，数组中前 `k` 个高频元素的集合是唯一的

  **进阶：**你所设计算法的时间复杂度 **必须** 优于 `O(n log n)` ，其中 `n` 是数组大小。

#### 思路

- 这道题目主要涉及到如下三块内容：

  1. 统计元素出现频率

     - 首先统计元素出现的频率，这一类的问题可以使用 map 来进行统计。

  2. 对频率排序

     - 然后是对频率进行排序，这里我们可以使用一种容器适配器就是**优先级队列**。
     - 优先级队列其实**就是一个披着队列外衣的堆**，因为优先级队列对外接口只是从队头取元素，从队尾添加元素，再无其他取元素的方式，看起来就是一个队列。
       - 优先级队列内部元素是自动依照元素的权值排列。

  3. 找出前K个高频元素

     - 此时要思考一下，是使用小顶堆呢，还是大顶堆？

       - 有的同学一想，题目要求前 K 个高频元素，那么果断用大顶堆啊。

         那么问题来了，定义一个大小为 k 的大顶堆，在每次移动更新大顶堆的时候，每次弹出都把最大的元素弹出去了，那么怎么保留下来前 K 个高频元素呢。而且使用大顶堆就要把所有元素都进行排序，那能不能只排序 k 个元素呢？

       - **所以我们要用小顶堆，因为要统计最大前k个元素，小顶堆每次将最小的元素弹出，最后小顶堆里积累的就是前k个最大元素。** 

  ```java
  class Solution {
      public int[] topKFrequent(int[] nums, int k) {
          int[] result = new int[k];
          int index = 0;
          Map<Integer, Integer> map = new HashMap<>();
          Queue<Map.Entry<Integer, Integer>> pq = new PriorityQueue<>(new Comparator<Map.Entry<Integer, Integer>>() {
              @Override
              public int compare(Map.Entry<Integer, Integer> e1, Map.Entry<Integer, Integer> e2) {
                  return e1.getValue() - e2.getValue(); // 用小顶堆
              }
          });
          // 1.统计元素的频率
          for (int i = 0; i < nums.length; i++) {
              map.put(nums[i], map.getOrDefault(nums[i], 0) + 1);
          }
          // 2.根据元素频率对元素排序
          Set<Map.Entry<Integer, Integer>> set = map.entrySet();
          Iterator<Map.Entry<Integer, Integer>> it = set.iterator();
          while (it.hasNext()) {
              pq.offer(it.next());
              if (pq.size() > k) {
                  pq.poll();
              }
          }
          // 3.取出前k个高频元素
          while (!pq.isEmpty()) {
              Map.Entry<Integer, Integer> entry = pq.poll();
              result[index++] = entry.getKey();
          }
          return result;
      }
  }
  ```

### 8.简化路径

#### 题目

- 给你一个字符串 `path` ，表示指向某一文件或目录的 Unix 风格 **绝对路径** （以 `'/'` 开头），请你将其转化为更加简洁的规范路径。

  在 Unix 风格的文件系统中，一个点（`.`）表示当前目录本身；此外，两个点 （`..`） 表示将目录切换到上一级（指向父目录）；两者都可以是复杂相对路径的组成部分。任意多个连续的斜杠（即，`'//'`）都被视为单个斜杠 `'/'` 。 对于此问题，任何其他格式的点（例如，`'...'`）均被视为文件/目录名称。

  请注意，返回的 **规范路径** 必须遵循下述格式：

  - 始终以斜杠 `'/'` 开头。
  - 两个目录名之间必须只有一个斜杠 `'/'` 。
  - 最后一个目录名（如果存在）**不能** 以 `'/'` 结尾。
  - 此外，路径仅包含从根目录到目标文件或目录的路径上的目录（即，不含 `'.'` 或 `'..'`）。

  返回简化后得到的 **规范路径** 。

  **示例 1：**

  ```
  输入：path = "/home/"
  输出："/home"
  解释：注意，最后一个目录名后面没有斜杠。 
  ```

  **示例 2：**

  ```
  输入：path = "/../"
  输出："/"
  解释：从根目录向上一级是不可行的，因为根目录是你可以到达的最高级。
  ```

  **示例 3：**

  ```
  输入：path = "/home//foo/"
  输出："/home/foo"
  解释：在规范路径中，多个连续斜杠需要用一个斜杠替换。
  ```

  **示例 4：**

  ```
  输入：path = "/a/./b/../../c/"
  输出："/c"
  ```

  **提示：**

  - `1 <= path.length <= 3000`
  - `path` 由英文字母，数字，`'.'`，`'/'` 或 `'_'` 组成。
  - `path` 是一个有效的 Unix 风格绝对路径。

#### 思路

- 一开始想的是用栈，后面发现不用这么麻烦，用列表就行。

  ```java
  class Solution {
      public String simplifyPath(String path) {
          StringBuilder sb = new StringBuilder();
          LinkedList<String> list = new LinkedList<>();
          //1.将路径上的任意多个连续斜杠转换为单斜杠
          path = path.replaceAll("//+", "/");
          //2.根据单斜杠对路径进行拆分
          String[] split = path.split("/");
          //3.利用LinkedList完成解题
          for (int i = 0; i < split.length; i++) {
              if (split[i].equals("") || split[i].equals(".")) {
                  continue;
              } else if (split[i].equals("..")) {
                  if (list.isEmpty()) {
                      continue;
                  } else {
                      list.removeLast();
                  }
              } else {
                  list.addLast(split[i]);
              }
          }
          if (list.isEmpty()) {
              return "/";
          }
          while (!list.isEmpty()) {
              sb.append("/");
              sb.append(list.removeFirst());
          }
          return sb.toString();
      }
  }
  ```

## 二叉树

### 二叉树理论基础篇

#### 二叉树的种类

- 在我们解题过程中二叉树有两种主要的形式：满二叉树和完全二叉树。

  - 满二叉树：如果一棵二叉树只有度为 0 的结点和度为 2 的结点，并且度为 0 的结点在同一层上，则这棵二叉树为满二叉树。

    ![img](https://code-thinking-1253855093.file.myqcloud.com/pics/20200806185805576.png)

  - 完全二叉树的定义如下：在完全二叉树中，除了最底层结点可能没填满外，其余每层结点数都达到最大值，并且最下面一层的结点都集中在该层最左边的若干位置。若最底层为第 h 层（h从1开始），则该层包含 1~ 2^(h-1) 个结点。

    ![img](https://code-thinking-1253855093.file.myqcloud.com/pics/20200920221638903.png)

- 前面介绍的树，都没有数值的，而二叉搜索树是有数值的了，**二叉搜索树是一个有序树**。

  - 若它的左子树不空，则左子树上所有结点的值均小于它的根结点的值；
  - 若它的右子树不空，则右子树上所有结点的值均大于它的根结点的值；
  - 它的左、右子树也分别为二叉排序树

  ![img](https://code-thinking-1253855093.file.myqcloud.com/pics/20200806190304693.png)
  
- 平衡二叉搜索树：又被称为 AVL 树，且具有以下性质：它是一棵空树或它的**左右两个子树的高度差的绝对值不超过 1**，并且左右两个子树都是一棵平衡二叉树。如图：

  ![img](https://code-thinking-1253855093.file.myqcloud.com/pics/20200806190511967.png)

#### 二叉树的遍历方式

- 二叉树主要有两种遍历方式：
  - 深度优先遍历：先往深走，遇到叶子结点再往回走。
    - **前序遍历**（递归法，迭代法）
    - **中序遍历**（递归法，迭代法）
    - **后序遍历**（递归法，迭代法）
  - 广度优先遍历：一层一层的去遍历。
    - **层次遍历**（迭代法）
- 最后再说一说二叉树中深度优先和广度优先遍历实现方式，我们做二叉树相关题目，**经常会使用递归的方式来实现深度优先遍历**，也就是实现前中后序遍历，使用递归是比较方便的。
  - 之前我们讲栈与队列的时候，就说过栈其实就是递归的一种实现结构，也就说前中后序遍历的逻辑其实都是可以**借助栈使用递归的方式**来实现的。
  - 而广度优先遍历的实现一般使用**队列**来实现

#### 二叉树的定义

```java
public class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;

    TreeNode() {}
    TreeNode(int val) { this.val = val; }
    TreeNode(int val, TreeNode left, TreeNode right) {
        this.val = val;
        this.left = left;
        this.right = right;
    }
}
```

### 1.二叉树的递归遍历

- 这里帮助大家确定下来递归算法的三个要素。**每次写递归，都按照这三要素来写，可以保证大家写出正确的递归算法！**

  1. **确定递归函数的参数和返回值：** 确定哪些参数是递归的过程中需要处理的，那么就在递归函数里加上这个参数， 并且还要明确每次递归的返回值是什么进而确定递归函数的返回类型。
  2. **确定终止条件：** 写完了递归算法，运行的时候，经常会遇到栈溢出的错误，就是没写终止条件或者终止条件写的不对，操作系统也是用一个栈的结构来保存每一层递归的信息，如果递归没有终止，操作系统的内存栈必然就会溢出。
  3. **确定单层递归的逻辑：** 确定每一层递归需要处理的信息。在这里也就会重复调用自己来实现递归的过程。

- 前序遍历：

  ```java
  class Solution {
      private List<Integer> result = new ArrayList<>();
  
      public List<Integer> preorderTraversal(TreeNode root) {
          // 递归终止条件
          if (root == null) {
              return result;
          }
          // 前序遍历：中左右
          result.add(root.val);
          result = preorderTraversal(root.left);
          result = preorderTraversal(root.right);
          return result;
      }
  }
  ```

- 中序遍历：

  ```java
  class Solution {
      private List<Integer> result = new ArrayList<>();
  
      public List<Integer> inorderTraversal(TreeNode root) {
          // 递归终止条件
          if (root == null) {
              return result;
          }
          // 中序遍历：左中右
          result = inorderTraversal(root.left);
          result.add(root.val);
          result = inorderTraversal(root.right);
          return result;
      }
  }
  ```

- 后序遍历：

  ```java
  class Solution {
      private List<Integer> result = new ArrayList<>();
  
      public List<Integer> postorderTraversal(TreeNode root) {
          // 递归终止条件
          if (root == null) {
              return result;
          }
          // 后序遍历：左右中
          result = postorderTraversal(root.left);
          result = postorderTraversal(root.right);
          result.add(root.val);
          return result;
      }
  }
  ```

### 2.二叉树的迭代遍历(栈)

- **递归的实现就是：每一次递归调用都会把函数的局部变量、参数值和返回地址等压入调用栈中**，然后递归返回的时候，从栈顶弹出上一次递归的各项参数，所以这就是递归为什么可以返回上一层位置的原因。那用栈也是可以实现二叉树的前中后序遍历的。

- 前序遍历：

  - 先看一下前序遍历。

    - 前序遍历是中左右，每次先处理的是中间结点，那么先将根结点放入栈中，然后将右孩子加入栈，再加入左孩子。
    - 为什么要先加入右孩子，再加入左孩子呢？ 因为这样出栈的时候才是中左右的顺序。

    ![二叉树前序遍历（迭代法）](https://code-thinking.cdn.bcebos.com/gifs/%E4%BA%8C%E5%8F%89%E6%A0%91%E5%89%8D%E5%BA%8F%E9%81%8D%E5%8E%86%EF%BC%88%E8%BF%AD%E4%BB%A3%E6%B3%95%EF%BC%89.gif)

  ```java
  class Solution {
      private List<Integer> result = new ArrayList<>();
      private Deque<TreeNode> deque = new ArrayDeque<>();
  
      public List<Integer> preorderTraversal(TreeNode root) {
          if (root == null) { // 如果root为null，直接返回
              return result;
          }
          deque.push(root);
          while (!deque.isEmpty()) {
              // 根结点先出栈
              TreeNode node = deque.pop();
              result.add(node.val);
              // 先压栈右结点，再压栈左结点，才能保证前序遍历的顺序
              if (node.right != null) {
                  deque.push(node.right);
              }
              if (node.left != null) {
                  deque.push(node.left);
              }
          }
          return result;
      }
  }
  ```

- 中序遍历：

  - 中序遍历的逻辑和前序遍历不同，在前序遍历中，先访问的元素是中间结点，要处理的元素也是中间结点，所以刚刚才能写出相对简洁的代码，**因为要访问的元素和要处理的元素顺序是一致的，都是中间结点。**
    - 中序遍历是左中右，先访问的是二叉树顶部的结点，然后一层一层向下访问，直到到达树左面的最底部，再开始处理结点（也就是在把结点的数值放进result数组中），这就造成了**处理顺序和访问顺序是不一致的。** 
  - 我们可以这样做：
    - 先把根结点入栈，然后每次结点出栈的时候，如果是第一次出栈，则按照**右结点、当前结点、左结点**的顺序入栈，如果是第二次出栈，则输出。

  ```java
  class Solution {
      private List<Integer> result = new ArrayList<>();
      private List<TreeNode> tmpt = new ArrayList<>(); // 用于查看结点是否是第一次出栈
      private Deque<TreeNode> deque = new ArrayDeque<>();
  
      public List<Integer> inorderTraversal(TreeNode root) {
          if (root == null) { // 先排除特殊情况
              return result;
          }
          deque.push(root);
          while (!deque.isEmpty()) {
              TreeNode node = deque.pop();
              if (!tmpt.contains(node)) { // 结点是第一次出栈
                  tmpt.add(node);
                  // 如果结点是第一次出栈，就按照node.right，node，node.left的顺序入栈
                  if (node.right != null) {
                      deque.push(node.right);
                  }
                  deque.push(node);
                  if (node.left != null) {
                      deque.push(node.left);
                  }
              } else { // 结点是第二次出栈
                  result.add(node.val);
              }
          }
          return result;
      }
  }
  ```

- 后序遍历：

  - 后序遍历和中序遍历大同小异，它的处理顺序和访问顺序也是不一致的。
  - 我们可以这样做：
    - 先把根结点入栈，然后每次结点出栈的时候，如果是第一次出栈，则按照**当前结点、右结点、左结点**的顺序入栈，如果是第二次出栈，则输出。

  ```java
  class Solution {
      private List<Integer> result = new ArrayList<>();
      private List<TreeNode> tmpt = new ArrayList<>(); // 用于查看结点是否是第一次出栈
      private Deque<TreeNode> deque = new ArrayDeque<>();
  
      public List<Integer> postorderTraversal(TreeNode root) {
          if (root == null) { // 先排除特殊情况
              return result;
          }
          deque.push(root);
          while (!deque.isEmpty()) {
              TreeNode node = deque.pop();
              if (!tmpt.contains(node)) { // 结点是第一次出栈
                  tmpt.add(node);
                  // 如果结点是第一次出栈，就按照node，node.right，node.left的顺序入栈
                  deque.push(node);
                  if (node.right != null) {
                      deque.push(node.right);
                  }
                  if (node.left != null) {
                      deque.push(node.left);
                  }
              } else { // 结点是第二次出栈
                  result.add(node.val);
              }
          }
          return result;
      }
  }
  ```

  - 还有第二种解法：

    - 后序遍历是：左右中；前序遍历是：中左右。

      - 那我们可以按前序遍历的解法，先把 `result` 中的顺序调整为 “中右左”，然后再把 result 反转就变为了 “左右中”。

        ![前序到后序](https://code-thinking-1253855093.file.myqcloud.com/pics/20200808200338924.png)

  ```java
  class Solution {
      private List<Integer> result = new ArrayList<>();
      private Deque<TreeNode> deque = new ArrayDeque<>();
  
      public List<Integer> postorderTraversal(TreeNode root) {
          // 先排除特殊情况
          if (root == null) {
              return result;
          }
          deque.push(root);
          while (!deque.isEmpty()) {
              TreeNode node = deque.pop();
              result.add(node.val);
              if (node.left != null) {
                  deque.push(node.left);
              }
              if (node.right != null) {
                  deque.push(node.right);
              }
          }
          Collections.reverse(result);
          return result;
      }
  }
  ```

- 总结：
  - 可以看出前序和中序是完全两种代码风格，并不像递归写法那样代码稍做调整，就可以实现前后中序。
    - **这是因为前序遍历中访问节点（遍历节点）和处理节点（将元素放进result数组中）可以同步处理，但是中序就无法做到同步！**

### 3.二叉树的统一迭代法

- 二叉树的统一迭代法就在于判断当前弹栈元素是不是第一次弹栈，如果是第一次弹栈，就要进行相应的再压栈处理，否则就输出到结果集中。

- 可以看到前中后序遍历的实现代码都可以是大同小异的：

  - 前序遍历：

    ```java
    class Solution {
        private List<Integer> result = new ArrayList<>();
        private List<TreeNode> tmpt = new ArrayList<>(); // 用于查看结点是否是第一次出栈
        private Deque<TreeNode> deque = new ArrayDeque<>();
    
        public List<Integer> preorderTraversal(TreeNode root) {
            if (root == null) { // 先排除特殊情况
                return result;
            }
            deque.push(root);
            while (!deque.isEmpty()) {
                TreeNode node = deque.pop();
                if (!tmpt.contains(node)) { // 结点是第一次出栈
                    tmpt.add(node);
                    // 如果结点是第一次出栈，就按照node.right，node.left，node的顺序入栈
                    if (node.right != null) {
                        deque.push(node.right);
                    }
                    if (node.left != null) {
                        deque.push(node.left);
                    }
                    deque.push(node);
                } else { // 结点是第二次出栈
                    result.add(node.val);
                }
            }
            return result;
        }
    }
    ```

  - 中序遍历：

    ```java
    class Solution {
        private List<Integer> result = new ArrayList<>();
        private List<TreeNode> tmpt = new ArrayList<>(); // 用于查看结点是否是第一次出栈
        private Deque<TreeNode> deque = new ArrayDeque<>();
    
        public List<Integer> inorderTraversal(TreeNode root) {
            if (root == null) { // 先排除特殊情况
                return result;
            }
            deque.push(root);
            while (!deque.isEmpty()) {
                TreeNode node = deque.pop();
                if (!tmpt.contains(node)) { // 结点是第一次出栈
                    tmpt.add(node);
                    // 如果结点是第一次出栈，就按照node.right，node，node.left的顺序入栈
                    if (node.right != null) {
                        deque.push(node.right);
                    }
                    deque.push(node);
                    if (node.left != null) {
                        deque.push(node.left);
                    }
                } else { // 结点是第二次出栈
                    result.add(node.val);
                }
            }
            return result;
        }
    }
    ```

  - 后序遍历：

    ```java
    class Solution {
        private List<Integer> result = new ArrayList<>();
        private List<TreeNode> tmpt = new ArrayList<>(); // 用于查看结点是否是第一次出栈
        private Deque<TreeNode> deque = new ArrayDeque<>();
    
        public List<Integer> postorderTraversal(TreeNode root) {
            if (root == null) { // 先排除特殊情况
                return result;
            }
            deque.push(root);
            while (!deque.isEmpty()) {
                TreeNode node = deque.pop();
                if (!tmpt.contains(node)) { // 结点是第一次出栈
                    tmpt.add(node);
                    // 如果结点是第一次出栈，就按照node，node.right，node.left的顺序入栈
                    deque.push(node);
                    if (node.right != null) {
                        deque.push(node.right);
                    }
                    if (node.left != null) {
                        deque.push(node.left);
                    }
                } else { // 结点是第二次出栈
                    result.add(node.val);
                }
            }
            return result;
        }
    }
    ```

### 4.二叉树的层序遍历(队列)

- 层序遍历一个二叉树。就是从左到右一层一层的去遍历二叉树。这种遍历的方式和我们之前讲过的都不太一样。

  - 需要借用一个辅助数据结构即队列来实现，**队列先进先出，符合一层一层遍历的逻辑，而用栈先进后出适合模拟深度优先遍历也就是递归的逻辑。**


#### 层序遍历的万用模板

```java
class Solution {
    private List<List<Integer>> result = new ArrayList<>();
    private ArrayDeque<TreeNode> deque = new ArrayDeque<>(); // 辅助队列

    public List<List<Integer>> levelOrder(TreeNode root) {
        if (root == null) { // 排除特殊情况
            return result;
        }
        deque.addLast(root);
        while (!deque.isEmpty()) {
            List<Integer> itemList = new ArrayList<>(); // 保存每一层的结果
            int len = deque.size(); // 用于判断当前层是不是遍历完了
            while (len > 0) {
                TreeNode node = deque.removeFirst();
                itemList.add(node.val);
                if (node.left != null) {
                    deque.addLast(node.left);
                }
                if (node.right != null) {
                    deque.addLast(node.right);
                }
                len--;
            }
            result.add(itemList);
        }
        return result;
    }
}
```

#### 4.1 二叉树的层次遍历II

##### 题目

- 给你二叉树的根节点 `root` ，返回其节点值 **自底向上的层序遍历** 。 （即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历）

  **示例 1：**

  ![img](https://assets.leetcode.com/uploads/2021/02/19/tree1.jpg)

  ```
  输入：root = [3,9,20,null,null,15,7]
  输出：[[15,7],[9,20],[3]]
  ```

  **示例 2：**

  ```
  输入：root = [1]
  输出：[[1]]
  ```

  **示例 3：**

  ```
  输入：root = []
  输出：[]
  ```

  **提示：**

  - 树中节点数目在范围 `[0, 2000]` 内
  - `-1000 <= Node.val <= 1000`

##### 思路

- 就是在 `4.二叉树的层序遍历` 的基础上，增加一个 `Collections.reverse()` 方法而已。

  ```java
  class Solution {
      private List<List<Integer>> result = new ArrayList<>();
      private ArrayDeque<TreeNode> deque = new ArrayDeque<>();
  
      public List<List<Integer>> levelOrderBottom(TreeNode root) {
          if (root == null) { // 排除特殊情况
              return result;
          }
          deque.addLast(root);
          while (!deque.isEmpty()) {
              List<Integer> itemList = new ArrayList<>();
              int len = deque.size();
              while (len > 0) {
                  TreeNode node = deque.removeFirst();
                  itemList.add(node.val);
                  if (node.left != null) {
                      deque.add(node.left);
                  }
                  if (node.right != null) {
                      deque.add(node.right);
                  }
                  len--;
              }
              result.add(itemList);
          }
          Collections.reverse(result);
          return result;
      }
  }
  ```

#### 4.2 二叉树的右视图

##### 题目

- 给定一个二叉树的 **根节点** `root`，想象自己站在它的右侧，按照从顶部到底部的顺序，返回从右侧所能看到的节点值。

  **示例 1:**

  ![img](https://assets.leetcode.com/uploads/2021/02/14/tree.jpg)

  ```
  输入: [1,2,3,null,5,null,4]
  输出: [1,3,4]
  ```

  **示例 2:**

  ```
  输入: [1,null,3]
  输出: [1,3]
  ```

  **示例 3:**

  ```
  输入: []
  输出: []
  ```

  **提示:**

  - 二叉树的节点个数的范围是 `[0,100]`
  - `-100 <= Node.val <= 100` 

##### 思路

- 层序遍历的时候，判断是否遍历到单层的最后面的元素，如果是，就放进 result 数组中，随后返回 result 就可以了。

  ```java
  class Solution {
      public List<Integer> rightSideView(TreeNode root) {
          List<Integer> result = new ArrayList<>();
          if (root == null) { // 排除特殊情况
              return result;
          }
          ArrayDeque<TreeNode> deque = new ArrayDeque<>();
          deque.addLast(root);
          while (!deque.isEmpty()) {
              int len = deque.size(); // 记录当前层级的元素个数
              while (len > 0) {
                  TreeNode node = deque.removeFirst();
                  if (node.left != null) {
                      deque.addLast(node.left);
                  }
                  if (node.right != null) {
                      deque.addLast(node.right);
                  }
                  len--;
                  if (len == 0) { // len为0表示是当前层级的最后一个元素
                      result.add(node.val);
                  }
              }
          }
          return result;
      }
  }
  ```

#### 4.3 二叉树的层平均值

##### 题目

- 给定一个非空二叉树的根节点 `root` , 以数组的形式返回每一层节点的平均值。与实际答案相差 `10^(-5)` 以内的答案可以被接受。

  **示例 1：**

  ![img](https://assets.leetcode.com/uploads/2021/03/09/avg1-tree.jpg)

  ```
  输入：root = [3,9,20,null,null,15,7]
  输出：[3.00000,14.50000,11.00000]
  解释：第 0 层的平均值为 3,第 1 层的平均值为 14.5,第 2 层的平均值为 11 。
  因此返回 [3, 14.5, 11] 。
  ```

  **示例 2:**

  ![img](https://assets.leetcode.com/uploads/2021/03/09/avg2-tree.jpg)

  ```
  输入：root = [3,9,20,15,7]
  输出：[3.00000,14.50000,11.00000]
  ```

  **提示：**

  - 树中节点数量在 `[1, 104]` 范围内
  - `-2^31 <= Node.val <= 2^31 - 1`

##### 思路

- 本题就是层序遍历的时候把一层求个总和在取一个均值。

  ```java
  class Solution {
      public List<Double> averageOfLevels(TreeNode root) {
          List<Double> result = new ArrayList<>();
          ArrayDeque<TreeNode> deque = new ArrayDeque<>();
          if (root == null) { // 排除特殊情况
              return result;
          }
          deque.addLast(root);
          while (!deque.isEmpty()) {
              int len = deque.size(); // 用于判断当前层级是否遍历完
              int num = len; // 用于计算平均数
              double sum = 0; // 用于计算当前层的总和
              while (len > 0) {
                  TreeNode node = deque.removeFirst();
                  sum += node.val;
                  if (node.left != null) {
                      deque.addLast(node.left);
                  }
                  if (node.right != null) {
                      deque.addLast(node.right);
                  }
                  len--;
              }
              result.add(sum / num);
          }
          return result;
      }
  }
  ```

#### 4.4 N叉树的层序遍历

##### 题目

- 给定一个 N 叉树，返回其节点值的层序遍历。（即从左到右，逐层遍历）。

  树的序列化输入是用层序遍历，每组子节点都由 null 值分隔（参见示例）。

  **示例 1：**

  ![img](https://assets.leetcode.com/uploads/2018/10/12/narytreeexample.png)

  ```
  输入：root = [1,null,3,2,4,null,5,6]
  输出：[[1],[3,2,4],[5,6]]
  ```

  **示例 2：**

  ![img](https://assets.leetcode.com/uploads/2019/11/08/sample_4_964.png)

  ```
  输入：root = [1,null,2,3,4,5,null,null,6,7,null,8,null,9,10,null,null,11,null,12,null,13,null,null,14]
  输出：[[1],[2,3,4,5],[6,7,8,9,10],[11,12,13],[14]]
  ```

  **提示：**

  - 树的高度不会超过 `1000`
  - 树的节点总数在 `[0, 10^4]` 之间

##### 思路

- 这道题依旧是模板题，只不过一个节点有多个孩子了。

  ```java
  class Solution {
      public List<List<Integer>> levelOrder(Node root) {
          List<List<Integer>> result = new ArrayList<>();
          ArrayDeque<Node> deque = new ArrayDeque<>();
          if (root == null) { // 排除特殊情况
              return result;
          }
          deque.addLast(root);
          while (!deque.isEmpty()) {
              List<Integer> itemList = new ArrayList<>();
              int len = deque.size();
              while (len > 0) {
                  Node node = deque.removeFirst();
                  itemList.add(node.val);
                  List<Node> children = node.children;
                  for (int i = 0; i < children.size(); i++) {
                      deque.addLast(children.get(i));
                  }
                  len--;
              }
              result.add(itemList);
          }
          return result;
      }
  }
  ```

#### 4.5 在每个树行中找最大值

##### 题目

- 给定一棵二叉树的根节点 `root` ，请找出该二叉树中每一层的最大值。

  **示例1：**

  ![img](https://assets.leetcode.com/uploads/2020/08/21/largest_e1.jpg)

  ```
  输入: root = [1,3,2,5,3,null,9]
  输出: [1,3,9]
  ```

  **示例2：**

  ```
  输入: root = [1,2,3]
  输出: [1,3]
  ```

  **提示：**

  - 二叉树的节点个数的范围是 `[0,104]`
  - `-2^31 <= Node.val <= 2^31 - 1`

##### 思路

- 层序遍历，取每一层的最大值。

  ```java
  class Solution {
      public List<Integer> largestValues(TreeNode root) {
          List<Integer> result = new ArrayList<>();
          ArrayDeque<TreeNode> deque = new ArrayDeque<>();
          if (root == null) { // 排除特殊情况
              return result;
          }
          deque.addLast(root);
          while (!deque.isEmpty()) {
              int max = Integer.MIN_VALUE;
              int len = deque.size(); // 用于判断当前层是否遍历完
              while (len > 0) {
                  TreeNode node = deque.removeFirst();
                  if (node.val > max) {
                      max = node.val;
                  }
                  if (node.left != null) {
                      deque.addLast(node.left);
                  }
                  if (node.right != null) {
                      deque.addLast(node.right);
                  }
                  len--;
              }
              result.add(max);
          }
          return result;
      }
  }
  ```

#### 4.6 填充每个节点的下一个右侧节点指针II

##### 题目

- 给定一个二叉树：

  ```
  struct Node {
    int val;
    Node *left;
    Node *right;
    Node *next;
  }
  ```

  填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 `NULL` 。

  初始状态下，所有 next 指针都被设置为 `NULL` 。

  **示例 1：**

  ![img](https://assets.leetcode.com/uploads/2019/02/15/117_sample.png)

  ```
  输入：root = [1,2,3,4,5,null,7]
  输出：[1,#,2,3,#,4,5,7,#]
  解释：给定二叉树如图 A 所示，你的函数应该填充它的每个 next 指针，以指向其下一个右侧节点，如图 B 所示。序列化输出按层序遍历顺序（由 next 指针连接），'#' 表示每层的末尾。
  ```

  **示例 2：**

  ```
  输入：root = []
  输出：[]
  ```

  **提示：**

  - 树中的节点数在范围 `[0, 6000]` 内
  - `-100 <= Node.val <= 100`

##### 思路

- 依然套用层序遍历的模板，只不过要注意当 len 为 0 时，要让当前出队的结点的 next 指向 null。

  ```java
  class Solution {
      public Node connect(Node root) {
          ArrayDeque<Node> deque = new ArrayDeque<>();
          if (root == null) { // 排除特殊情况
              return null;
          }
          deque.addLast(root);
          while (!deque.isEmpty()) {
              int len = deque.size();
              while (len > 0) {
                  Node n1 = deque.removeFirst();
                  Node n2 = deque.peekFirst();
                  if (n1.left != null) {
                      deque.addLast(n1.left);
                  }
                  if (n1.right != null) {
                      deque.addLast(n1.right);
                  }
                  len--;
                  if (len == 0) {
                      n1.next = null;
                  } else {
                      n1.next = n2;
                  }
              }
          }
          return root;
      }
  }
  ```

#### 4.7 二叉树的最大深度

##### 题目

- 给定一个二叉树 `root` ，返回其最大深度。

  二叉树的 **最大深度** 是指从根节点到最远叶子节点的最长路径上的节点数。

  **示例 1：**

  ![img](https://assets.leetcode.com/uploads/2020/11/26/tmp-tree.jpg)

   

  ```
  输入：root = [3,9,20,null,null,15,7]
  输出：3
  ```

  **示例 2：**

  ```
  输入：root = [1,null,2]
  输出：2
  ```

  **提示：**

  - 树中节点的数量在 `[0, 10^4]` 区间内。
  - `-100 <= Node.val <= 100`

##### 思路

- 使用迭代法的话，使用层序遍历是最为合适的，因为最大的深度就是二叉树的层数，和层序遍历的方式极其吻合。

  - 在二叉树中，一层一层的来遍历二叉树，记录一下遍历的层数就是二叉树的深度。

  ```java
  class Solution {
      public int maxDepth(TreeNode root) {
          if (root == null) { // 排除特殊情况
              return 0;
          }
          int result = 0;
          ArrayDeque<TreeNode> deque = new ArrayDeque<>();
          deque.addLast(root);
          while (!deque.isEmpty()) {
              int len = deque.size();
              while (len > 0) {
                  TreeNode node = deque.removeFirst();
                  if (node.left != null) {
                      deque.addLast(node.left);
                  }
                  if (node.right != null) {
                      deque.addLast(node.right);
                  }
                  len--;
                  if (len == 0) {
                      result += 1;
                  }
              }
          }
          return result;
      }
  }
  ```

#### 4.8 二叉树的最小深度

##### 题目

- 给定一个二叉树，找出其最小深度。

  最小深度是从根节点到最近叶子节点的最短路径上的节点数量。

  **说明：**叶子节点是指没有子节点的节点。

  **示例 1：**

  ![img](https://assets.leetcode.com/uploads/2020/10/12/ex_depth.jpg)

  ```
  输入：root = [3,9,20,null,null,15,7]
  输出：2
  ```

  **示例 2：**

  ```
  输入：root = [2,null,3,null,4,null,5,null,6]
  输出：5
  ```

  **提示：**

  - 树中节点数的范围在 `[0, 105]` 内
  - `-1000 <= Node.val <= 1000`

##### 思路

- 相对于 `4.7 二叉树的最大深度`，本题还也可以使用层序遍历的方式来解决，思路是一样的。

  - **需要注意的是，只有当左右孩子都为空的时候，才说明遍历的最低点了。如果其中一个孩子为空则不是最低点**

  ```java
  class Solution {
      public int minDepth(TreeNode root) {
          if (root == null) {
              return 0;
          }
          int result = 0;
          ArrayDeque<TreeNode> deque = new ArrayDeque<>();
          deque.addLast(root);
          while (!deque.isEmpty()) {
              int len = deque.size();
              while (len > 0) {
                  TreeNode node = deque.removeFirst();
                  // 左右子结点都为空，表示已经到最小深度处了
                  if (node.left == null && node.right == null) {
                      result++;
                      return result;
                  }
                  if (node.left != null) {
                      deque.addLast(node.left);
                  }
                  if (node.right != null) {
                      deque.addLast(node.right);
                  }
                  len--;
                  if (len == 0) {
                      result++;
                  }
              }
          }
          return result;
      }
  }
  ```

### 5.翻转二叉树

#### 题目

- 给你一棵二叉树的根节点 `root` ，翻转这棵二叉树，并返回其根节点。

  **示例 1：**

  ![img](https://assets.leetcode.com/uploads/2021/03/14/invert1-tree.jpg)

  ```
  输入：root = [4,2,7,1,3,6,9]
  输出：[4,7,2,9,6,3,1]
  ```

  **示例 2：**

  ![img](https://assets.leetcode.com/uploads/2021/03/14/invert2-tree.jpg)

  ```
  输入：root = [2,1,3]
  输出：[2,3,1]
  ```

  **示例 3：**

  ```
  输入：root = []
  输出：[]
  ```

  **提示：**

  - 树中节点数目范围在 `[0, 100]` 内
  - `-100 <= Node.val <= 100`

#### 思路

- 想要翻转二叉树，其实就把每一个节点的左右孩子交换一下就可以了。遍历的过程中去翻转每一个节点的左右孩子就可以达到整体翻转的效果。

  - 关键在于遍历顺序，前中后序或层序应该选哪一种遍历顺序？
  - **这道题目使用前序遍历、后序遍历、层序遍历都可以，唯独中序遍历不方便，因为中序遍历会把某些节点的左右孩子翻转了两次！建议拿纸画一画，就理解了**。

  我这里用的是层序遍历：

  ```java
  class Solution {
      public TreeNode invertTree(TreeNode root) {
          if (root == null) {
              return null;
          }
          ArrayDeque<TreeNode> deque = new ArrayDeque<>(); // 用于判断树是否遍历完了
          deque.addLast(root);
          while (!deque.isEmpty()) {
              TreeNode node = deque.removeFirst();
              TreeNode left = null;
              TreeNode right = null;
              if (node.left != null) {
                  left = node.left;
                  deque.addLast(left);
              }
              if (node.right != null) {
                  right = node.right;
                  deque.addLast(right);
              }
              node.left = right;
              node.right = left;
          }
          return root;
      }
  }
  ```

  - 用递归也能做：

    ```java
    class Solution {
        public TreeNode invertTree(TreeNode root) {
            // 递归三要素：
            //  1.确定传入的参数和返回值
            //  2.确定递归终止条件
            //  3.确定单层业务逻辑
            // 递归终止条件
            if (root == null) {
                return root;
            }
            // 这里用的是后序遍历
            TreeNode tmpt = root.right;
            root.right = invertTree(root.left);
            root.left = invertTree(tmpt);
            return root;
        }
    }
    ```

    - 但是递归比较玄学，我还是喜欢用迭代。
