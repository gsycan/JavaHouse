---

typora-copy-images-to: 刷刷刷IMG
typora-root-url: 刷刷刷IMG
---

# 刷刷刷-niuke

# 分类：

## 一、查找

### 1、整数中1出现的次数   #牛客题号JZ31

**（从1到n整数中1出现的次数）**

**题目描述：**

求出1~13的整数中1出现的次数,并算出100~1300的整数中1出现的次数？为此他特别数了一下1~13中包含1的数字有1、10、11、12、13因此共出现6次,但是对于后面问题他就没辙了。ACMer希望你们帮帮他,并把问题更加普遍化,可以很快的求出任意非负整数区间中1出现的次数（从1 到 n 中1出现的次数）。

**思路：**假设n=31419，m=1 每次乘10，将n分为a b两个部分，a=n/m前半部分,b=n%m后半部分, 

#m=1时，a=31419，b=0，再判断a%10 == 0或1，发现等于9，走else，表示个位为1时，有[0，3141] 3142组1，即：(31419/10+1)*1个1=3142. 

eg:31411,31401,31391,31381....00001

#m=10时，a=3141，b=9，再判断a%10 == 0或1，发现等于1，走else if，表示个位为1时，最高位只能由b=9+1个为1，除了最高位其余有[0，314] 314组1，即：(3141/10)*10+(9+1)个1.

eg:31410~31419（10个1）,31310~31319（10个1）.... 00010~00019（10个1） 共0~313=314组10个1加上（3141b 31419） (b+1=9+1)个1

#依次类推。注意 a%10=0时，表示此位上没有1.所以去掉这个高位，计算其余位上的1的个数。即a/10*m

**代码：**

```java
public class Solution {
    public int NumberOf1Between1AndN_Solution(int n) {
        int count = 0;//计数器
        for(long m=1; m<=n; m*=10){        //这里m n a b必须是long类型
            long a = n/m;// n除以10前半部分
            long b = n%m;// n除以10后半部分
            // 判断当前位上的数组是否为0、1或大于1
            if(a%10 == 0){
                //表示此时数的最高位不包括1，只有a/(10*m)组 为1
                count += a/10*m;
            }else if(a%10 == 1){
                //表示此时数的最高位1的个数由1后面的数字决定，有a/10*m + (b+1)组为1
                count += (a/10*m) + (b+1);
            }else{
                //表示此时数的最高位1的个数是满进制的 有（a/10+1）*m组1
                count += (a/10+1)*m;
            }
        }
        return count;
    }
}
```

## **2.、二分查找   高频

**查找一个数组元素的下标**。

二分法适用于**已经排好序**的数组，定义两个变量，一个low,一个high,则mid=low+(low+high)/2 //防止下标溢出
**算法核心：**

如果 value==arr[mid],中间值正好等于要查找的值，则返回下标，return mid;

如果 value<arr[mid],要找的值小于中间的值，则再往数组的小端找，high=mid-1;

如果 value>arr[mid],要找的值大于中间的值，则再往数组的大端找，low=mid+1;

**NB作者：liweiwei1419  下面文章详细介绍了二分法！！！**
链接：https://leetcode-cn.com/problems/search-insert-position/solution/te-bie-hao-yong-de-er-fen-cha-fa-fa-mo-ban-python-/

**思路1：在循环体内部查找元素**

```xml
while(left  <=  right) 这种写法表示在  循环体内部  直接查找元素；
退出循环的时候 left 和 right 不重合，区间 [left, right] 是空区间。
```

```java
public static int binarySearch(int[] arr,int k){
    int low = 0;
    int high = arr.length-1;
    while(low <= high){
        int mid = low+(high-low)/2;
        if(arr[mid] == k){
            return mid;
        }else if(k > arr[mid]){
            low = mid + 1;
        }else{
            high = mid - 1;
        }
    }
    return -1;
}
```

思路 2：在循环体内部排除元素（重点）

```xml
while(left < right) 这种写法表示在 循环体内部 排除元素；
退出循环的时候 left 和 right 重合，区间 [left, right] 只剩下成 1 个元素，这个元素有可能就是我们要找的元素。最后return时候在做一步判断 是否为target。
```



#### 2.0、搜索插入位置

描述：给定一个**排序数组**和一个目标值，在数组中找到目标值，并返回其索引。**如果目标值不存在于数组中，返回它将会被按顺序插入的位置**。你可以假设数组中无重复元素。

**示例 1:**

```
输入: [1,3,5,6], 5
输出: 2
```

**示例 2:**

```
输入: [1,3,5,6], 2
输出: 1
```

思路：使用二分查找的第二种思路(见上述二分查找思路2)，**即在循环体内排除元素**

```java
public class Solution {
    public int searchInsert(int[] nums, int target) {
        int len = nums.length;
        if (len == 0) return 0;
        // 特判  是否target大于nums中最大的元素 直接放在最后
        if (nums[len - 1] < target) {
            return len;
        }
        int left = 0;
        int right = len - 1;
        //**在循环体内部排除元素写法，最后会剩下一个元素 有可能使我们需要的元素
        while (left < right) {
            int mid = left + (right - left) / 2;
            // 严格小于 target 的元素一定不是解
            if (nums[mid] < target) {
                // 下一轮搜索区间是 [mid + 1, right]
                left = mid + 1;
            } else {
                // 下一轮搜索区间是 [left, mid]
                right = mid;
            }
        }
        return left;
    }
}
```

复杂度分析：

    时间复杂度：O(log⁡N)，N是数组的长度，每一次都将问题的规模缩减为原来的一半，因此时间复杂度是对数级别的；
    空间复杂度：O(1)，使用到常数个临时变量。





#### 2.1、旋转数组的最小数字   #JZ6

**题目描述：**

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。 输入一个非递减排序的数组的一个旋转，输出旋转数组的最小元素。例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。
NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。

**思路：** **二分查找的变种**，没有具体的值用来比较，可以用中间值和高低位索引进行比较，判断中间值处于递增还是递减序列，进行缩小范围。**如果low==high 退出循环**

- 处于递增：low加一 上移
- 处于递减：high减一 下移（如果这里写high=mid-1，可能会错过最小值，因为找的就是最小值）
- 其余情况：low++ 上移，缩小范围

Note：如果是1011这种数组，判断arr[low]<arr[high]? 成立则说明0<1 找到最小值

<img src="C:\Users\guosiyu8\AppData\Roaming\Typora\typora-user-images\image-20200622172434948.png" alt="image-20200622172434948" style="zoom:50%;" />

<img src="C:\Users\guosiyu8\AppData\Roaming\Typora\typora-user-images\image-20200622172537820.png" alt="image-20200622172537820" style="zoom:50%;" />

<img src="C:\Users\guosiyu8\AppData\Roaming\Typora\typora-user-images\image-20200622172708769.png" alt="image-20200622172708769" style="zoom: 50%;" />



**代码：**

```java
import java.util.ArrayList;
/**
分析：二分查找变种，没有具体的值用来比较。那么用中间值和高低位进行比较，看处于递增还是递减序列，进行操作缩小范围。
    处于递增：low上移
    处于递减：high下移（如果是high-1，则可能会错过最小值，因为找的就是最小值）
    其余情况：low++缩小范围
*/
public class Solution {
    public int minNumberInRotateArray(int [] array) {
        if(array.length == 0) return 0;
        int low = 0;
        int high = array.length-1;
        int mid = 0;
        while(low < high){
            //额外考虑：子数组是非递减的数组 如10111
            if(array[low] < array[high]){
                return array[low];
            }
            mid = low+(high-low)/2;//避免数组溢出
            if(array[mid] > array[low]){
                low = mid+1;
            }else if(array[mid] < array[high]){
                high = mid;//注意这里不是mid-1，避免错过最小值。反过来arr是递减的 就需要mid-1
            }else{
                low++;
            }
        }
        return array[low];
    }
}
```



#### 2.2 搜索旋转排序数组 leet33

**描述**：假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。

搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。

你可以假设数组中不存在重复的元素。

你的算法**时间复杂度必须是 O(log n)** 级别   ----  **有序并且要求世家复杂度 O(log n) 就是二分法**。

> 示例 1:
>
> 输入: nums = [4,5,6,7,0,1,2], target = 0
> 输出: 4



**思路**：

题目要求算法**时间复杂度**必须是 **O(log⁡n)**的级别，这提示我们可以使用**二分搜索**的方法。

但是数组本身不是有序的，进行旋转后只保证了**数组的局部是有序的，这还能进行二分搜索吗**？答案是可以的。

可以发现的是，我们将数组从中间分开成左右两部分的时候，**一定有一部分的数组是有序的**。拿示例来看，我们从 6 这个位置分开以后数组变成了 [4, 5, 6] 和 [7, 0, 1, 2] 两个部分，其中左边 [4, 5, 6] 这个部分的数组是有序的，其他也是如此。

这启示我们可以在常规二分搜索的时候**查看当前 mid 为分割位置分割出来的两个部分 [l, mid] 和 [mid + 1, r] 哪个部分是有序的，并根据有序的那个部分确定我们该如何改变二分搜索的上下界，因为我们能够根据有序的那部分判断出 target 在不在这个部分**：

    如果 [l, mid - 1] 是有序数组，且 target 的大小满足 在此区间内，则我们应该将搜索范围缩小至 [l, mid - 1]，否则在 [mid + 1, r] 中寻找。
    如果 [mid, r] 是有序数组，且 target 的大小满足 在此区间内，则我们应该将搜索范围缩小至 [mid + 1, r]，否则在 [l, mid - 1] 中寻找。

```java
class Solution {
    public int search(int[] nums, int target) {
        if(nums==null || nums.length==0) return -1;
        int l=0, r=nums.length-1, mid=0;
        while(l <= r){
            mid = l + (r-l)/2;
            if(nums[mid] == target) return mid;
            // 0到mid位置是递增的 此区间有序
            if(nums[0]<=nums[mid]){
                // 判断target是否在0到mid 或者 mid到最后
                if(nums[0]<=target && target<nums[mid]) r=mid-1;
                else l=mid+1;
            }
            // mid到最后位置部分是递增的
            else{
                //判断target是否在mid到最后
                if(nums[mid]< target && target<= nums[nums.length-1]) l=mid+1;
                else r=mid-1;
            }
        }
        return -1;
    }
}
```





### 3、二维数组中的查找(数组有规律)   #JZ1

**题目描述：**

在一个二维数组中（每个一维数组的长度相同），每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

**思路：** 利用该二维数组的性质：

- 每一行从左到右递增排序，每一列从上到下递增

即对于左下角的值m，m是该行最小的数，同时是该列最大的数，每次将m和目标值比较：

- 当m<target,由于m是该列最大的元素，要想更大的只有从这行其他的考虑，所以向右遍历查找
- 当m>target,由于m是该行最小的元素，要想更小的只有从上一行考虑，所以向上遍历查找
- 如果m=target 则找到值，返回true

**Note：**用某行最小或者某列最大与target比较  每次可以剔除掉一整行或一整列

**时间复杂度：O（行高＋列宽）**

**空间复杂度：O（1）**

```java
public class Solution {
    public boolean Find(int target, int [][] array) {
        // 有效性检查
        int rows = array.length;
        if(rows == 0) return false;
        int cols = array[0].length;
        if (cols == 0) return false;
        //从左下开始寻找 向上或向右走 可以节省一行或一列
        int row = rows-1;
        int col = 0;
        while(row >= 0 && col < cols){
            if(target > array[row][col]){
                col++;
            }else if(target < array[row][col]){
                row--;
            }else{
                return true;
            }
        }
        return false;
    }
}
```





## 二、排序

### 1、数据流中的中位数   #JZ63

**题目描述：**如何得到一个数据流中的中位数？如果从数据流中读出奇数个数值，那么中位数就是所有数值排序之后位于中间的数值。如果从数据流中读出偶数个数值，那么中位数就是所有数值排序之后中间两个数的平均值。我们使用Insert()方法读取数据流，使用GetMedian()方法获取当前读取数据的中位数。

**解决思路：**

- 先用java集合**PriorityQueue来设置一个小顶堆和大顶堆**  

-  主要的思想是：因为要求的是中位数，那么这两个堆，**大顶堆用来存较小的一系列数，从大到小排列**；  

-   ***小顶堆存较大的数**，从小到大的顺序排序**，

  显然中位数就是大顶堆的根节点与小顶堆的根节点和的平均数。  

-    ⭐保证：小顶堆中的元素都大于等于大顶堆中的元素，所以每次塞值，并不是直接塞进去，而是从另一个堆中poll出一个最大（最小）的塞值  

-    ⭐当数目为偶数的时候，将这个值插入大顶堆中，再将大顶堆中根节点（即最大值）插入到小顶堆中；  

-    ⭐当数目为奇数的时候，将这个值插入小顶堆中，再讲小顶堆中根节点（即最小值）插入到大顶堆中；  

-    ⭐**取中位数的时候，如果当前个数为偶数，显然是取小顶堆和大顶堆根结点的平均值；如果当前个数为奇数，显然是取小顶堆的根节点**  

  理解了上面所述的主体思想，下面举个例子辅助验证一下。 

  例如，传入的数据为：[5,2,3,4,1,6,7,0,8],那么按照要求，输出是"5.00 3.50 3.00 3.50 3.00 3.50 4.00 3.50 4.00 " 

  那么整个程序的执行流程应该是（用min表示小顶堆，max表示大顶堆）： 

-    5先进入大顶堆，然后将大顶堆中最大值放入小顶堆中，此时min=[5],max=[无]，avg=[5.00]  
-    2先进入小顶堆，然后将小顶堆中最小值放入大顶堆中，此时min=[5],max=[2],avg=[(5+2)/2]=[3.50]  
-    3先进入大顶堆，然后将大顶堆中最大值放入小顶堆中，此时min=[3,5],max=[2],avg=[3.00]  
-    4先进入小顶堆，然后将小顶堆中最小值放入大顶堆中，此时min=[4,5],max=[3,2],avg=[(4+3)/2]=[3.50]  
-    1先进入大顶堆，然后将大顶堆中最大值放入小顶堆中，此时min=[3,4,5],max=[2,1]，avg=[3/00]  
-    6先进入小顶堆，然后将小顶堆中最小值放入大顶堆中，此时min=[4,5,6],max=[3,2,1],avg=[(4+3)/2]=[3.50]  
-    7先进入大顶堆，然后将大顶堆中最大值放入小顶堆中，此时min=[4,5,6,7],max=[3,2,1],avg=[4]=[4.00]  
-    0先进入小顶堆，然后将小顶堆中最小值放入大顶堆中，此时min=[4,5,6,7],max=[3,2,1,0],avg=[(4+3)/2]=[3.50]  
-    8先进入大顶堆，然后将大顶堆中最大值放入小顶堆中，此时min=[4,5,6,7,8],max=[3,2,1,0],avg=[4.00]  

```java
import java.util.PriorityQueue;
import java.util.Comparator;
public class Solution {
    // 创建小顶堆  堆顶最小
    private PriorityQueue<Integer> minHeap = new PriorityQueue<Integer>();
    // 创建大顶堆  堆顶最大
    private PriorityQueue<Integer> maxHeap = new PriorityQueue<Integer>(15,
            new Comparator<Integer>() {
                public int compare(Integer o1, Integer o2){
                return o2 - o1;
                }
            });
    
    // 记录偶数还是奇数个数
    int count = 0;
    // 每次插入小顶堆的是当前大顶堆中最大的数，每次插入大顶堆的是当前小顶堆中最小的数
    // *************这样可以保证小顶堆中的数永远大于等于大顶堆中的数***********
    // 中位数就可以根据个数奇偶 从两个堆的根节点获取了
    public void Insert(Integer num) {
        //个数为偶数时，则插入到大顶堆，****然后将大顶堆中的最大的数插入到小顶堆中****
        if(count % 2 == 0){
            maxHeap.offer(num);
            int max = maxHeap.poll();
            minHeap.offer(max);
        }else{
        //个数为奇数时，则插入到小顶堆，*****然后将小顶堆中的最小的数插入到大顶堆中****
            minHeap.offer(num);
            int min = minHeap.poll();
            maxHeap.offer(min);
        }
            count++; //*** 全局遍历！！！ 记录当前个数****
    }
    //获得每次的中位数
    public Double GetMedian() {
        // 当前为偶数个时，则取小顶堆和大顶堆的堆顶元素并求平均值
        if(count % 2 == 0){
            return new Double(minHeap.peek() + maxHeap.peek()) / 2; //除以2在里面就按照int计算了  结果就是对int型进行强制转换 就错误了！
        }else{
            return new Double(minHeap.peek());
        }
    }
}
```

### **2、快速排序

**思路：**对于一组数，任意选择一个数作为中枢pivot，数组中比pivot大的数放在它的右边，比pivot小的数放在它的左边，一边排序之后，第一个pivot就放在了整体排序后正确的位置处了；接下来分别对pivot左边和右边的数组再循环上述过程，最终得到排序后的数组。

```java
@Test
public static void quickSort(int[] arr, int left, int right){
    if (left < right){
        int mid = getMidQucikSort(arr, left, right);
        quickSort(arr, left, mid-1);
        quickSort(arr, mid+1, right);
    }
}
@Test
public static int getMidQucikSort(int[] arr, int left, int right){
    //int pivot = arr[left]; // 默认数组最左边是中枢值
    if (left < right){ // 改进版本，随机选择中枢值
        int randomIndex = left + 1 + random.nextInt(right-left);
        int temp = arr[left];
        arr[left] = arr[randomIndex];
        arr[randomIndex] = temp;
    }
    int pivot = arr[left]; //改进后的中枢值

    while (left < right){
        while (left < right && pivot <= arr[right]) right--;
        if (left < right) arr[left++] = arr[right];
        while (left < right && pivot > arr[left]) left++;
        if (left < right) arr[right--] = arr[left];
    }
    arr[left] = pivot;//把中枢值放在正确的位置上
    return left; //返回中枢系下标
}
```

### **2.1 无序数组中寻找倒数第K大的数  高频考点

**思路：** 手撕算法经典题目，考虑使用快排思想(分治法)，**随机**取某个数p，数组中小于p的放在左边，大于p的放在右边，一次排序后，p所在位置就是最终的位置，然后看index=（数组长度-倒数第k的数下标）== p的位置，成立则找到了，如果index>p的位置，就从p的右边去继续寻找。

**注意:** 本题注重考查方法的时间复杂度，此方法的**时间复杂度是O(N)，空间复杂度O(1)   最好的结果！！** 

```java
public static Random random = new Random();
@Test
public static int searchNumK(int[] arr, int k){
    //有效性判断
    if(arr.length == 0 || k<0 || k>arr.length){
        return Integer.MIN_VALUE;
    }
    int left = 0, right = arr.length-1;
    int index = arr.length - k; // 第k大值对应的数组下标
    while (true){
        int mid = getMid(arr, left, right);// 获得调整后每次位置
        if (index == mid) return arr[mid];// 如果mid和待求下标k相同 则找到
        else if (index > mid) left = mid + 1;// 左指针缩小范围
        else right = mid - 1;
    }
}
//获得当前数在数组中排好序的索引位置
public static int getMid(int[] arr, int left, int right){
    // **随机设置pivot 枢纽值，否则对于有序数组会退化为O(n^2)
    if (left < right){
        int randomIndex = left + 1 + random.nextInt(right - left);// 加1保证不会与自身交换
        int temp = arr[left];
        arr[left] = arr[randomIndex];
        arr[randomIndex] = temp;
    }
    int pivot = arr[left];// 设置基准值
    while (left < right){
        while (left < right && pivot <= arr[right]) right--;
        if (left < right) arr[left++] = arr[right]; // 在右边找到比基准值小的 与左边交换位置
        while (left < right && pivot > arr[left]) left++;
        if (left < right) arr[right--] = arr[left];
    }
    arr[left] = pivot; // 把基准值放在正确的位置left
    return left; // 返回基准值
}
```

### **2.2 无序数组中寻找第K大的数  #leet215 堆&快排分治

**堆：找最小的数 使用大顶堆(把最大的交换出去)；找最大的数 使用小顶堆(把最小的交换出去)**

描述：在**未排序的数组**中找到**第 k 个最大**的元素。请注意，你需要**找的是数组排序后的第 k 个最大的元素**，而不是第 k 个不同的元素。

示例 1:输入: [3,2,1,5,6,4] 和 k = 2           输出: 5

示例 2:输入: [3,2,3,1,2,4,5,5,6] 和 k = 4     输出: 4

说明:你可以假设 k 总是有效的，且 1 ≤ k ≤ 数组的长度。

思路：

1.使用**快排的分治思想**，详见2.1的方法    **时间复杂度最小 推荐的**

> 时间复杂度： **O (N )**，算法只需要扫描所有的数据一次
>
> 空间复杂度： O (1 )。

2.使用**堆**的思想，

> 时间复杂度： **O (N * log2 K )**，算法只需要扫描所有的数据一次
>
> 空间复杂度：大小为K的数组，只需要存储一个容量为K 的堆。也可以维护部分

创建**大顶堆(降序)** 所有(部分)元素进堆，排好序后取出前(k-1)个元素，下一个就是第k大元素

创建**小顶堆(升序)部分元素k个进堆**，维护k大小的堆，后面元素有比当前堆最小值大的则入堆并删除堆当前最小的元素，直到所有元素遍历完，这样k个大小的堆保存的就是第k大到最大的几个元素，此时直接peek()就是第k大。

Note：**使用原生方法建堆时间复杂度比PriorityQueue快！**

```java
class Solution {
    public int findKthLargest(int[] nums, int k) { 
// ********  方法1 1.1和方法2都是构建小顶堆  也可以使用大顶堆-方法3！ *********
         int len = nums.length;
        //**方法1： 最小堆-使用优先级队列 内部也是堆  
        //思路 默认小顶堆升序 遍历数据 出现比堆最小数大的 就替换，这样最后堆剩下的是升序的第k大到最大的数
         PriorityQueue<Integer> priorityQueue = new PriorityQueue<>(k + 1);
           // 只放前k个元素构造堆  节省空间时间
         for (int i = 0; i < k; i++) {
             priorityQueue.add(nums[i]);
         }
         for (int i = k; i < len; i++) {
             priorityQueue.add(nums[i]);
             priorityQueue.poll();
         }
         return priorityQueue.peek();
     }
        
        //方法1.1：最小堆 变种
        //就是在 for 循环里面判断小顶堆里面的 size() 是否大于 k 个数，是的话就 poll() 出去；整个 for 循环结束之后剩下来的就是 k 个数的小顶堆。堆顶即第 k 大的数。
        public int findKthLargest(int[] nums, int k) {
        	PriorityQueue<Integer> heap = new PriorityQueue<>();
        	for (int num : nums) { // 遍历全部元素
            	heap.add(num);
            	if (heap.size() > k) { //*每次poll出去当前k个元素中最小的 剩下的就是第k大到最大的
             	   heap.poll();
            	}
        	}
        	return heap.peek(); //此时小顶堆 堆顶就是第k大
    	}
        
        //方法3：大顶堆降序 lamda表达式，容量默认11. 
        //思路：大顶堆 降序，全部元素进堆，最大的在前面 先把k-1输出，则下一个就是第k大的数，
        // PriorityQueue<Integer> queue = new PriorityQueue<>((a,b)->b-a); 
        // 放入全部元素构建大顶堆  时间稍长
        // for(int n : nums){
        //     queue.offer(n);
        // }
        // for(int i=0; i<k-1; i++){
        //     queue.poll();
        // }
      
    
//    **推荐  方法2：使用原生建立堆(小顶堆)的方法   时间复杂度低很多****
        int[] temp = new int[k]; //临时结果
        for(int i=0; i<k; i++){
            temp[i] = nums[i];
        }
        //默认对前k个数建堆 xiao顶堆  取出堆顶就是最xiao元素！！！
        for(int i=k/2; i>=0; i--){ // 注意 i--  ***
            adjustHeap(temp, i, k-1); //arr.parent节点，len长度
        }
        for(int i=k; i<len; i++){
            int t = temp[0]; //最小的
            if(nums[i] > t){ //把更大的数放进来 最后是k到最大的升序
                temp[0] = nums[i];
                adjustHeap(temp, 0, k-1);
            }
        }
        return temp[0];//返回第k大的  temp保存的第k大到最大的k个数
    }
// **调整为最小堆**
    public void adjustHeap(int[] arr, int parent, int len){
        int temp = arr[parent];
        int child = 2*parent+1;
        while(child <= len){
            if(child+1 <= len && arr[child+1]<arr[child]){ //**这里是最小堆写法
                child++;
            }
            if(arr[child] > temp){//**这里是最小堆写法
                break;
            }else{
                arr[parent] = arr[child];
                parent = child;
                child = 2*parent+1;
            }
        }
        arr[parent] = temp;
    }

}
```





### *3、堆排序

堆排序就是利用大顶堆或者小顶堆，先来介绍大顶堆和小顶堆的概念：

堆：是一棵顺序存储的完全二叉树。完全二叉树中所有非终端节点的值均不大于（或不小于）其左、右孩子节点的值。

**大顶堆就是堆顶元素最大，其中每个节点的值大于等于其左、右孩子的值，这样的堆称为大根堆，每次取出堆顶最大元素，所以大顶堆是降序**

**小顶堆就是堆顶元素最小，其中每个节点的值小于等于其左、右孩子的值，这样的堆称为小根堆，每次取出堆顶最小元素，所以小顶堆是升序**

Note：**PriorityQueue默认是自然排序，即每次最小的在堆顶 升序**；

如果想降序 每次堆顶是最大的数，需要实现Comparator接口。

**思路：** 

1. 将数组构造成初始堆,比如大顶堆（**若想升序则建立小顶堆 每次堆顶取出最小值**，若想降序，则建立大根堆 每次堆顶取出最大值) 

   从最后一个节点开始调整，得到初始堆。

2. 此时数组的最大值就是堆顶的根节点，将其与数组末尾交换，数组最后一个为最大值

3. 将数组剩余的元素依次执行第1和第2步骤，最终得到一个升序数组

```java
/**
     * 堆排序 不稳定排序 O(nlonn) 适合大量数据下排序
     * 而且堆排序每一轮可以找出当前数据中最大（大顶堆）或最小（小顶堆）的数
     */
// 堆排序
public static void heapSort(int[] arr){
    int len = arr.length;
    //1.bulid heap 建堆 第一次循环就找到最大值放在根节点上
    // length/2是最下面一个非叶子结点，一直到i=0根节点
    for(int i=len/2; i>=0; i--){
        //从下向上建堆
        adjustHeap(arr, i, len-1);
    }
    //2.exchange, adjust heap
    //把根节点最大值和最后一个节点交换位置，在对除了最后一个节点的其他节点排序
    for(int i=arr.length-1; i>0; i--){
        // 交换根节点和最后一个元素 swap
        int temp = arr[0];//获取第一个结点 即当前最大值
        arr[0] = arr[i];//把最后一个结点交换到首位
        arr[i] = temp; //把最大值交换到最后一个位置上
        // 继续调整除了最后一个位置的其他的数组 从上到下寻找最大值
        adjustHeap(arr, 0, i-1);// i-1表示把第一次排好的最大值(数组最后一个)排除了
    }
}

// 调整堆- 对于大顶堆 每次把最大值放在堆顶
public static void adjustHeap(int[] arr, int parent, int len){
    int tmp = arr[parent]; //获取当前父节点
    int child = 2*parent+1；//当前父节点的左孩子结点索引
    while(child <= len){
        //说明右孩子比左孩子数值大 准备把右孩子和父节点交换
        //%%%%%%%%%%%%%%%%%%%%%%%%%%  1 小顶堆改为arr[child+1] < arr[child]
        if(child +1 <= len && arr[child+1] > arr[child]){  
            child++;
        }
        //如果所有的孩子节点都小于父节点，就结束循环 
         //%%%%%%%%%%%%%%%%%%%%  2 小顶堆改为arr[child] > tmp  (only two point rewrite)
        if(arr[child] < tmp){
            break;
        }else{
        // 出现孩子结点值大于父节点 则交换父节点和孩子结点
            arr[parent] = arr[child];
            parent = child; //迭代 此时父节点变成孩子结点下标 向下遍历
            child = 2*parent+1; //孩子结点的下标
        }
    }// end while
    arr[parent] = tmp;//最终把原来父节点的值赋值给调整后的孩子节点
}
```



#### **3.1 无序数组中寻找最小的k个数  高频考点

**堆：找最小的数 使用大顶堆(把最大的交换出去)；找最大的数 使用小顶堆(把最小的交换出去)**

**描述：** 使用堆排序，找出无序数组中最小的k个数   

比如：王者荣耀用户上亿，如何快速的从亿级数据量中找出排名榜首的3位玩家？

对于这种**数据量比较大的情况，堆排序比较合适** **时间复杂度O（nlogk），而且**堆排序每一轮可以找出当前数据中最大（大顶堆）或最小（小顶堆）的数**，所以对于以上问题，用堆排序是不错的选择。

**Note：****PriorityQueue默认是自然排序，即每次最小的在堆顶 升序**；默认o1.compareTo(o2)是递增

如果想降序 每次堆顶是最大的数，需要实现Comparator接口 ；o2.compareTo(o1);。

```java
// 降序 需要实现接口 定义return o2.compareTo(o1); 
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(k, new Comparator<Integer>() {
    @Override
    public int compare(Integer o1, Integer o2) {
        return o2.compareTo(o1); //降序 
    }
});
```



**思路：**本题解答基于上述的堆排序(大顶堆) ， **时间复杂度O（nlogk）适合处理海量数据**

这里思路是 

1.假定前3个就是最小的数 定义为数组temp，然后在temp利用大顶堆找出最大的数m，

2.m依次和数组剩余的数比较，发现有比m还小的数就把这个数替换到temp中，

3.然后在对新组成的temp进行大顶堆排序 找出最大值，并且和数组剩余的数比较

4.重复步骤2和3 直到找出最小的三个数

这样动态维护堆中这k个数，以保证它只储存输入数组中的前k个最小的数，最后输出堆即可。

**提供了两种方法：  1 原生建堆方法   2 利用优先级队列实现**

```java
public static void main(String[] args) { //测试
    int []array = {1,5,3,9,7,8,6,2,4};
    ArrayList<Integer> res = findLeastK(array, 3);
    System.out.println(res.toString());
    ArrayList<Integer> res2 = findLeastK_priorityQueue(array, 3);
    System.out.println(res2.toString());
}

//***************************方法1：堆排序实现  不借助优先级队列**************************
public static ArrayList<Integer> findLeastK(int[] arr, int k){
    ArrayList<Integer> res = new ArrayList<>();//存放结果
    if(k==0 ||k>arr.length) return res;//有效性检验
    int[] temp = new int[k]; //存放临时结果
    for(int i=0; i<k; i++){
        temp[i] = arr[i];
    }
    //默认对前k个数建堆 大顶堆  取出堆顶就是最大元素！！！
    for(int i=k/2; i>=0; i--){
        //建堆 *****注意 这里是对temp进行初始建堆 最大值放在堆顶。这样可以通过牛客！！！！*****
        adjustHeap(temp, i, k-1); 
    }
    //temp中最大的和后面数比较，如果有更小的则交换 并重新建堆
    for(int j=k; j<arr.length; j++){
        if(temp[0] > arr[j]){ //存在更小的数
            temp[0] = arr[j];
            adjustHeap(temp, 0, k-1);
        }
    }
    //存结果到List
    for(int i=0; i<k; i++){
        res.add(0，temp[i]); // **temp中存储的是大顶堆即降序，如5，3，1. add(0,temp)就是升序***
    }
    return res;
} 
// 创建大顶堆 降序
public static void adjustHeap(int[] arr, int parent, int len){
    int temp = arr[parent];
    int child = parent*2+1;
    while(child <= len){
        if(child+1 <= len && arr[child+1]>arr[child]){
            child++;
        }
        if(arr[child] < temp){
            break;
        }else{
            arr[parent] = arr[child];
            parent = child;
            child = parent*2+1;
        }
    }
    arr[parent] = temp;
}
```

```java
// *****************方法2:使用priorityQueue 实现寻找最小的k个数*****************************
@Test
public static  ArrayList<Integer> findLeastK_priorityQueue(int[] arr, int k){
    ArrayList<Integer> result = new ArrayList<>();
    int len = arr.length;
    if (k > len || k == 0) return result;
    //1 创建大顶堆。而priorityQueue 默认位小顶堆
    //****** 创建降序的lambad简单方式！！ new PriorityQueue((a,b)->b-a); *****
    PriorityQueue<Integer> maxHeap = new PriorityQueue<>(k, new Comparator<Integer>() {
        @Override
        public int compare(Integer o1, Integer o2) {
            // ***数值型使用 o2-o1 字符型使用o2.compareTo(o1)***
            return o2.compareTo(o1); //本方法使用大根堆 所以降序 。o1.compareTo(o2)默认升序
        }
    });
    
    //2 利用大顶堆排序。把堆中最大的值与数组其余最小的值交换，最后剩下就是最小的k个数
    // 方法1
/*    for (int i=0; i<len; i++){
        if (maxHeap.size() != k){ // 默认放入前三个数 进行建堆
            maxHeap.offer(arr[i]);
        }else if (maxHeap.peek() > arr[i]){ //大顶堆每次取出堆顶最大值 判断是否存在更小的数
            Integer temp = maxHeap.poll();
            temp = null;
            maxHeap.offer(arr[i]);
        }
    } */
    //方法2  利用堆自身的性质维护前k个最小的数
    for(int n : arr){
        maxHeap.offer(n);
        if(maxHeap.size()>k){
            maxHeap.poll(); //弹出此时最大的根节点 剩下就是最小的k个数
        }
    }
    
    //3 存放结果。由于使用大顶堆 每次poll都是最大值
    //for (Integer integer: maxHeap){   这样得到的结果并不是按照从小到大排序的。。。
    //    result.add(integer);
    //}
    while(maxHeap.peek() != null) {   // 这样是从小到大排序的！！！！！！！！！！
        result.add(0,maxHeap.poll());
    }
    return result;
}
```

#### **3.3 最后一块石头的重量 #leet1046

**描述：** 有一堆石头，每块石头的重量都是正整数。

每一回合，从中选出两块 最重的 石头，然后将它们一起粉碎。假设石头的重量分别为 x 和 y，且 x <= y。那么粉碎的可能结果如下：

    如果 x == y，那么两块石头都会被完全粉碎；
    如果 x != y，那么重量为 x 的石头将会完全粉碎，而重量为 y 的石头新重量为 y-x。

最后，最多只会剩下一块石头。返回此石头的重量。如果没有石头剩下，就返回 0。

示例：

输入：[2,7,4,1,8,1]
输出：1
解释：
先选出 7 和 8，得到 1，所以数组转换为 [2,4,1,1,1]，
再选出 2 和 4，得到 2，所以数组转换为 [2,1,1,1]，
接着是 2 和 1，得到 1，所以数组转换为 [1,1,1]，
最后选出 1 和 1，得到 0，最终数组转换为 [1]，这就是最后剩下那块石头的重量。

思路：**使用堆是首选，建立大顶堆 降序，每次取出最大的两个数，注意判断数组为空的情况 直接返回**

```java
class Solution {
    public int lastStoneWeight(int[] stones) {
        //if(stones==null || stones.length==0) return 0;
        int len = stones.length;
        //建立大顶堆
        PriorityQueue<Integer> heap = new PriorityQueue<>(len,(a,b)->b-a); //默认小顶堆 升序
        for(int n : stones){
            heap.offer(n);
        }
        while(heap.size() != 1){
            int y = heap.poll();
            int x = heap.poll();
            if(y > x) {
                heap.offer(y-x);
            }
            if(heap.isEmpty()) return 0; //**注意需要判断没有元素情况 直接返回0**
        }
        return heap.peek();

    }
}
```



#### *3.4 根据字符出现的频率排序 Comparable #leet451 

**利用到排序接口  Comparable<T>**

描述：给定一个字符串，请将字符串里的**字符**按照出现的**频率**降序排列。

示例 1:输入:"tree"  输出:"eert"

解释:    'e'出现两次，'r'和't'都只出现一次。
因此'e'必须出现在'r'和't'之前。此外，"eetr"也是一个有效的答案。

示例 2:输入:"cccaaa"  输出:"cccaaa"

解释: 'c'和'a'都出现三次。此外，"aaaccc"也是有效的答案。
注意"cacaca"是不正确的，因为相同的字母必须放在一起。

示例 3:输入:"Aabb"  输出:"bbAa"

解释:此外，**"bbaA"也是一个有效的答案**，但"Aabb"是不正确的。
注意'A'和'a'被认为是两种不同的字符。

**思路：**

**1 利用priorityQueue构建大顶堆，大顶堆存放的是一个实现了Comparable的数组对象(字符和次数)，堆根据这个自定义规则进行降序排序**

**2. 这个大顶堆根据对象的次数属性降序，在遍历每一个节点的字符和出现次数，使用StringBuilder进行拼接字符**

```java
class Solution {
    public String frequencySort(String s) {
       if("".equals(s) || s==null) return "";
       //1 初始化字母数组 利用int数组存储字符串 
        int[] latter = new int[256]; //字符最大个数
        //2. ****填充数组**
        for(char c : s.toCharArray()){
           latter[c]++; //例如 'a'对应数字是97 所以这里是找到字母对应的数字，在数组97位置加1，即对应字母位置记录出现的次数
        }
        //3 利用大顶堆排序 自定义排序对象
        PriorityQueue<Latter> queue = new PriorityQueue<>();
        for(int i=0; i<latter.length; i++){
            // 利用堆存储对象  数组中对应的位置不为0 说明之前有填充的字符
            if(latter[i] != 0){ // 次数不为0才存储
                // 储存出现过的字符和对应的次数  
                queue.offer(new Latter((char)i, latter[i]));//***char int 自动转为字符**
            }
        }
        //4 拼接字符串，此时大顶堆已经根据出现次数降序排列，只需要拼接字符串即可
        StringBuilder sb = new StringBuilder();
        while(!queue.isEmpty()){
            Latter temp = queue.poll();
            for(int i=0; i<temp.count; i++){
                sb.append(temp.latter);
            }
        }
        return sb.toString();
    }

    // ***自定义排序对象 内部排序   实现Comparable接口说明当前的类自身有排序功能***
    // comparator是外部排序 需要自定义外部排序器
    public class Latter implements Comparable<Latter>{
        public char latter = '0'; //****字符 不能用双引号 只能单引号表示字符****
        public int count = 0; //字符出现次数
        public Latter(char latter, int count){
            this.latter = latter;
            this.count = count;
        }
        public int compareTo(Latter o){ //**和当前对象比较 传递一个参数
            // 根据count的大小 从大到小排序
            return o.count - this.count;// //****降序（大顶堆） 形参减去this***
            //return this.count - o.count; //升序
        }
    }
}
```

#### **3.5 前k个高频元素  Comparator #leet347

**利用到排序接口  Comparator<T>**

**描述：**给定一个**非空的整数数组**，返回其中**出现频率**前 **k** 高的元素。

**示例 1**:输入: nums = [1,1,1,2,2,3], k = 2
输出: [1,2]

**示例 2**:输入: nums = [1], k = 1
输出: [1]

提示：

    你可以假设给定的 k 总是合理的，且 1 ≤ k ≤ 数组中不相同的元素的个数。
    你的算法的时间复杂度必须优于 O(n log n) , n 是数组的大小。
    题目数据保证答案唯一，换句话说，数组中前 k 个高频元素的集合是唯一的。
    你可以按任意顺序返回答案。

**思路：** 

1.利用**hashmap存储数字和对应的频率**，建立映射关系

2.利用**小顶堆 对map中的频率排序**，升序，后续遇见频率更大的就把当前最小的频率和它交换 最后就剩下频率最大的k个数。 注意：这**里如何对map的value进行排序是个sao操作**，详见代码

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        if(nums.length==0 || nums==null || k<1 || k>nums.length) return null;

        //1 利用hashmap 存储出现数字和对应次数，元素为key 出现的次数为value
        HashMap<Integer,Integer> hs = new HashMap<>();
        for(int i=0; i<nums.length; i++){
            if(hs.containsKey(nums[i])){
                int count = hs.get(nums[i]);
                hs.put(nums[i], count+1);
            }else{
                hs.put(nums[i], 1);
            }
        }
        //2 遍历map 使用最小堆保存频率最大的k个元素，这里堆排序使用自定义comparator接口
        // lambda表达式简洁写法 
        //PriorityQueue<Integer> queue = new PriorityQueue<>((Integer a,Integer b)->hs.get(a)-hs.get(b));
        PriorityQueue<Integer> queue = new PriorityQueue<>(new Comparator<Integer>(){
            @Override
            public int compare(Integer a, Integer b){
                //sao操作 利用map获取key对应的value 实际上就是根据value排序 升序--小顶堆
                return hs.get(a) - hs.get(b);
            }
        });
        for(Integer key : hs.keySet()){ //遍历map的key
            // 简洁写法
            queue.offer(key); //因为堆已经自定义是根据map.get(key)排序 这里直接添加key就行
            if(queue.size() > k) queue.poll();
            // if(queue.size() < k){
            //     //** 这里直接存key就行，因为队列中比较器是通过map.gey(key) 所以传递key就行**
            //     queue.offer(key); 
            // }else if(hs.get(key) >  hs.get(queue.peek())){ //如果出现值比最小堆堆顶小 交换进去
            //     queue.poll();
            //     queue.offer(key);
            // }
        }
        //3 最后取出最小堆中的最大的k个元素
        int[] res = new int[k];
        for(int i=0; i<k; i++){
            res[i] = queue.poll();
        }
        return res;
    }
}
```



#### **3.6 前k个高频单词  #leet692

**和上题 3.5思路一直，唯一注意这里是多个字符串，而不是单个数组或字符！所以牵扯到字符串首字母的排序**

描述：给一非空的单词列表，返回前 k 个出现次数最多的单词。

返回的答案应该按单词出现频率由高到低排序。如果不同的单词有相同出现频率，按字母顺序排序。

示例 1：

输入: ["i", "love", "leetcode", "i", "love", "coding"], k = 2
输出: ["i", "love"]
解析: "i" 和 "love" 为出现次数最多的两个单词，均为2次.  注意，按字母顺序 "i" 在 "love" 之前。

示例 2：

输入: ["the", "day", "is", "sunny", "the", "the", "the", "sunny", "is", "is"], k = 4
输出: ["the", "is", "sunny", "day"]
解析: "the", "is", "sunny" 和 "day" 是出现次数最多的四个单词，出现次数依次为 4, 3, 2 和 1 次。

**思路：**   

 **思路和题目“寻找出现频率最高的前k个字符”思路一样，**
 **首先 通过map映射单词和频率的关系，然后使用小顶堆对频率建堆(使用comparator比较器)** 
 **最后剩下的k个频率就是最高的，在找到对应的单词。**

```java
class Solution {
    public List<String> topKFrequent(String[] words, int k) {
        List<String> res = new ArrayList<>();
        if(words==null || words.length==0 || k<1 || k>words.length) return res;
        //一 使用map建立单词和频率的映射关系
        HashMap<String, Integer> map = new HashMap<>();
        for(String s : words){
            if(map.containsKey(s)){
                map.put(s, map.get(s)+1);
            }else{
                map.put(s, 1);
            }
        }
        //二 使用小顶堆 
        //方式1 自定义使用频率排序  也可以使用lamdab表达式实现排序接口
        //如果频率不相同 则按照升序s1-s2, 如果频率相同 则头字母在前的放后面 逆序
        // 因为结果需要是出现频率从大到小，所以对上述结果统一进行反转 reverse（res）
        PriorityQueue<String> queue = new PriorityQueue<>(new Comparator<String>(){
            public int compare(String s1, String s2){
                return map.get(s1).equals(map.get(s2)) ? 
                s2.compareTo(s1) : map.get(s1) - map.get(s2);
            }
        });
        //方式2 lambda 表达式 实现排序的小顶堆
        // PriorityQueue<String> queue = new PriorityQueue<>(
        //     (s1,s2)->map.get(s1).equals(map.get(s2)) ? 
        //     s2.compareTo(s1) : map.get(s1)-map.get(s2)
        // );

        // 三 储出数据 把比堆顶大的元素替换进去
        for(String temp : map.keySet()){
            //推荐********利用堆自身特性 维持前k个元素 等价下面代码******
            queue.offer(temp);
            if(queue.size() > k) queue.poll(); //因为是小顶堆 每次交换出去堆顶最小的数 剩下前k大
            
            //不推荐 时间慢 而且牵扯字符串头字母问题 elseif中需要>=  然后先添加进去在poll出去
            // if(queue.size()<k){
            //     queue.offer(temp);
            // }else if(map.get(temp) >= map.get(queue.peek())){
            //     queue.offer(temp);
            //     queue.poll();
            // }
        }
        while(!queue.isEmpty()){
            res.add(queue.poll());
        }
        // 注意本题需要反转链表！！
        Collections.reverse(res);
        return res;
    }
}
```



#### 3.7最接近原点的k个点

描述：我们有一个由平面上的点组成的列表 points。需要从中找出 K 个距离原点 (0, 0) 最近的点。

（这里，平面上两点之间的距离是欧几里德距离。）

你可以按任何顺序返回答案。除了点坐标的顺序之外，答案确保是唯一的。

示例 1：  输入：points = [[1,3],[-2,2]], K = 1       输出：[[-2,2]]
解释： 
(1, 3) 和原点之间的距离为 sqrt(10)，
(-2, 2) 和原点之间的距离为 sqrt(8)，
由于 sqrt(8) < sqrt(10)，(-2, 2) 离原点更近。
我们只需要距离原点最近的 K = 1 个点，所以答案就是 [[-2,2]]。

思路：

```java
class Solution {
    //思路还是 使用排序接口Comparator (b1^2+b2^2)-(a1^2+a2^2)然后用大顶堆 交换并把最小的k个数存起来
    //但是这里有一个出奇制胜的想法 也是利用排序 直接堆二维数组排序
    public int[][] kClosest(int[][] points, int K) {
        int[][] res = new int[K][2];
        Arrays.sort(points,(int[] q1,int[] q2)->
        (q1[0]*q1[0]+q1[1]*q1[1] - q2[0]*q2[0]-q2[1]*q2[1]));
        System.arraycopy(points,0,res,0,K);//复制数组到一个新数组  0是开始下标，k是新数组的长度
        return res;
    }
}
```













## 三、递归

### 1、矩形覆盖 #JZ10

**题目描述：**我们可以用2*1的小矩形横着或者竖着去覆盖更大的矩形。请问用n个2*1的小矩形无重叠地覆盖一个2*n的大矩形，总共有多少种方法？ 。

**解决思路：**找规律是斐波那契数列，k=1 fun=1，k=2 fun=2 ，k=3 fun=fun(1)+fun(2)=3 .....

```java
public class Solution {
    public int RectCover(int target) {
        if(target <= 0) return 0; 
        if(target == 1){//只有一种方法
            return 1;
        }
        else if(target == 2){
             return 2;
        }else{
            return RectCover((target-1))+RectCover((target-2));
        }
    }
}
```

### *2、斐波那契数列  #JZ7

**描述：** f(0)=0,  f(1)=1，f(2)=1，f(3)=f(2)+f(1)=1+1=2,  f(4)=f(3)+f(2)=3..... **fn = f(n-1)+f(n-2) n>=3**

```java
public class Solution {
    public int Fibonacci(int n) {
         //递归方法1
        if(n == 0) return 0;
        if(n == 1) return 1;
        return Fibonacci(n-1) + Fibonacci(n-2);
         //递归方法2
        //if(n < 2) return n;
        //else return Fibonacci(n-1) +Fibonacci(n-2);
        
        //*************迭代方法************************** 考察时让写迭代的！！！
        if(n == 0) return 0;
        if(n == 1) return 1;
        int f1 = 1, f2=0, f=0; //**** f是每回合结果  f1表示f(1)=1,f2等于上一轮的f1
        for(int i=1; i<n; i++){ //i从1开始
            f = f1 + f2;// f是每回合结果
            f2 = f1;//f2 等于上一回合f1
            f1 = f;//f1等于新的f
        }
        return f;
    }
}
```



### *3、跳台阶-斐波那契数列  #JZ8 leet

**题目描述：**一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法（先后次序不同算不同的结果）。**1,2,3,5,8,13,21....就是斐波那契数列的规律**

**解决思路：** **迭代方法**比递归时间复杂度低得多

**时间复杂度 O(N)** ： 计算 f(n) 需循环 n 次，每轮循环内计算操作使用 O(1) 。
空间复杂度 O(1) ： 几个标志变量使用常数大小的额外空间。

设跳上 n 级台阶有 f(n) 种跳法。在所有跳法中，青蛙的最后一步只有两种情况： 跳上 1 级或 2 级台阶。

    当为 1 级台阶： 剩 n−1 个台阶，此情况共有 f(n−1) 种跳法；
    当为 2 级台阶： 剩 n−2 个台阶，此情况共有 f(n−2) 种跳法。

)f(n) 为以上两种情况之和，即 f(n)=f(n−1)+f(n−2 ，以上递推性质为斐波那契数列。本题可转化为 求斐波那契数列第 n 项的值 ，与 面试题10- I. 斐波那契数列 等价，唯一的不同在于起始数字不同。

    青蛙跳台阶问题： f(0)=1 , f(1)=1 , f(2)=2 ；
    斐波那契数列问题： f(0)=0 , f(1)=1 , f(2)=1 。
    链接：https://leetcode-cn.com/problems/qing-wa-tiao-tai-jie-wen-ti-lcof/solution/mian-shi-ti-10-ii-qing-wa-tiao-tai-jie-wen-ti-dong/

```java
public class Solution {
    public int JumpFloor(int target) {
        //递归方法
        //if(target <= 0) return 0;
        //if(target == 1) return 1;
        //if(target == 2) return 2;
        //return JumpFloor(target -1) + JumpFloor(target -2);
        
        // *****青蛙跳问题解决方法！leetcode 迭代方法******
        public int numWays(int n) {
            int a = 1, b = 1, sum;// 对应着f(0)=1 , f(1)=1 , f(2)=2
            for(int i = 0; i < n; i++){
                sum = (a + b) % 1000000007;//避免超过int界限
                a = b;
                b = sum;
            }
            return a;
        }
        
        //*********迭代方法**************  时间复杂度很低
        //利用斐波那契数列从下往上算，避免重复计算，提高效率
        if(target <= 0) return 0;
        if(target <= 2) return target;//** 这里f(1)=1  f(2)=2 可以跳一节或二节
        int f1 = 1, f2 = 0, f = 0;  //**** f是每回合结果  f1表示f(1)=1,f2等于上一轮的f1
        for(int i=1; i<=target; i++){  //i从1开始 青蛙可达到target
            f = f1 + f2;
            f2 = f1;
            f1 = f;
        }
        return f;
    }
}
```



### 4、变态跳台阶

**题目描述：**一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。

```java
得出最终结论,在n阶台阶，一次有1、2、...n阶的跳的方式时，总得跳法为：

           | 1       ,(n=0 ) 
f(n) =     | 1       ,(n=1 )
           | 2*f(n-1),(n>=2)

public class Solution {
    public int JumpFloorII(int target) {
        if (target <= 0) {
            return -1;
        } else if (target == 1) {
            return 1;
        } else {
            return 2 * JumpFloorII(target - 1);
        }
    }
}
```



## 四、贪心

### ?1、剪绳子  JZ67

给你一根长度为n的绳子，请把绳子剪成整数长的m段（m、n都是整数，n>1并且m>1，m<=n），每段绳子的长度记为k[1],...,k[m]。请问k[1]x...xk[m]可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。	

```java
public class Solution {
    public int cutRope(int target) {
        if(target<=0) return 0;
        if(target==1 || target == 2) return 1;
        if(target==3) return 2;
        int m = target % 3;
        switch(m){
            case 0 :
                return (int) Math.pow(3, target / 3);
            case 1 :
                return (int) Math.pow(3, target / 3 - 1) * 4;
            case 2 :
                return (int) Math.pow(3, target / 3) * 2;
        }
        return 0;
    }
}
```

## 五、动态规划

### **1、连续子数组的最大和  #JZ30

描述：例如:{6,-3,-2,7,-15,1,2,2},连续子向量的最大和为8(从第0个开始,到第3个为止)。给一个数组，返回它的最大连续子序列的和，你会不会被他忽悠住？(子向量的长度至少是1)

思路：典型的动态规划。

dp[n]代表以当前元素为截止点的连续子序列的最大和，**如果dp[n-1]>0，dp[n]=dp[n]+dp[n-1]，因为当前数字加上一个正数一定会变大**；**如果dp[n-1]<0，dp[n]加上0 即不变，因为当前数字加上一个负数一定会变小**。使用一个变量max记录最大的dp值返回即可。

```java
public int FindGreatestSumOfSubArray(int[] array) {
	// 因为题目保证数组长度至少为1  所以不许有效性检验
    // if(array.length == 0) return;
    int res = array[0]; //保存最大子数组之和
    for(int i=1; i<array.length; i++){
        array[i] += Math.max(array[i-1], 0);//计算dp[n]=dp[n]+dp[n-1] 如果dp[n-1]<0 则加0
        //array[i] += array[i-1]>0? array[i-1]:0; //等同于上一句
        res = Math.max(array[i], res); //判断是res大还是新的连续子序列和大array[i]
    }
    return res;
}
//test  
//1.输入array={6,-3,-2,7,-15,1,2,2} 计算后array={6,3,1,8,-7,1,3,5} 所以最大连续是从下标0到3 res=8
//2.输入array={6,-3,-2,7,-15,1,2,2,11} 计算后array={6,3,1,8,-7,1,3,5,16} 所以最大连续是从5到8 res=16
```



### **2、最长连续序列  leet128

给定一个**未排序**的整数数组，找出**最长连续序列的长度**。

要求算法的时间复杂度为 O(n)。

示例:  输入: [100, 4, 200, 1, 3, 2]
输出: 4
解释: 最长连续序列是 [1, 2, 3, 4]。它的长度为 4

**思路：**

方法一：**哈希表**

思路和算法

我们考虑枚举数组中的每个数 x，考虑以其为起点，不断尝试匹配 x+1,x+2,⋯x+1, x+2, \cdotsx+1,x+2,⋯ 是否存在，假设最长匹配到了 x+yx+yx+y，那么以 x 为起点的最长连续序列即为 x,x+1,x+2,⋯ ,x+yx, ，其长度为 y+1，我们不断枚举并更新答案即可。

对于匹配的过程，暴力的方法是 O(n) 遍历数组去看是否存在这个数，但其实更高效的方法是用一个哈希表存储数组中的数，这样**查看一个数是否存**在即能优化至 O(1) 的时间复杂度。

仅仅是这样我们的算法时间复杂度最坏情况下还是会达到 O(n2)（即外层需要枚举 O(n) 个数，内层需要暴力匹配 O(n)次），无法满足题目的要求。但仔细分析这个过程，**我们会发现其中执行了很多不必要的枚举**，如果已知有一个 x,x+1,x+2,⋯ ,x+yx, x+1, x+2, \cdots, x+yx,x+1,x+2,⋯,x+y 的连续序列，而我们却重新从 x+1x+1x+1，x+2x+2x+2 或者是 x+yx+yx+y 处开始尝试匹配，那么得到的结果肯定不会优于枚举 x 为起点的答案，因此我们在外层循环的时候碰到这种情况跳过即可。

**那么怎么判断是否跳过呢？**由于我们要枚举的数 xxx 一定是在数组中不存在前驱数 x−1的，不然按照上面的分析我们会从 x−1开始尝试匹配，因此我们每次在哈希表中检查是否存在 x−1即能判断是否需要跳过了。

**增加了判断跳过的逻辑之后，时间复杂度是多少呢？**外层循环需要 O(n) 的时间复杂度，只有当一个数是连续序列的第一个数的情况下才会进入内层循环，然后在内层循环中匹配连续序列中的数，因此数组中的每个数只会进入内层循环一次。根据上述分析可知，总**时间复杂度为 O(n)**，符合题目要求

```java
class Solution {
    public int longestConsecutive(int[] nums) {
        Set<Integer> num_set = new HashSet<Integer>();
        for (int num : nums) { //1 利用hashset可以去重  秒啊！
            num_set.add(num);
        }
        int longestStreak = 0; //存放结果
        // 遍历所有数据
        for (int num : num_set) {
            //2 寻找序列的起点，如果没有上一个元素则说明当前元素可以作为起点
            if (!num_set.contains(num - 1)) {
                int currentNum = num; // 临时变量
                int currentStreak = 1; // 临时结果
				//3 判断当前元素有没有下一个连续的数 累加结果
                while (num_set.contains(currentNum + 1)) {
                    currentNum += 1;
                    currentStreak += 1;
                }
				// 4 寻找最长的那个序列
                longestStreak = Math.max(longestStreak, currentStreak);
            }
        }
        return longestStreak;
    }
}
```





## 六、回溯

### ?1、矩阵中的路径 DFS #JZ65

描述：请设计一个函数，用来判断在一个矩阵中是否存在一条包含某字符串所有字符的路径。路径可以从矩阵中的任意一个格子开始，每一步可以在矩阵中向左，向右，向上，向下移动一个格子。如果一条路径经过了矩阵中的某一个格子，则该路径不能再进入该格子。      例如<img src="/Snipaste_2020-07-29_15-01-47.png" style="zoom:50%;" />   矩阵中包含一条字符串"bcced"的路径，但是矩阵中不包含"abcb"路径，因为字符串的第一个字符b占据了矩阵中的第一行第二个格子之后，路径不能再次进入该格子。

思路：

​	这种题目就不能再依靠高级数据结构的思想来帮助我们解决问题，要纯靠自己的思维能力来进行解决了。

​	这道题目自己的思维局限是没有理解广度优先遍历要掌握的诀窍导致写代码的时候出现了死循环，而且有一个误区是，如果当前节点和路径上的下一个节点不相等时，此时我要不要直接返回false断绝此路径上继续下探的可能，答案是要，因为只有这样找到的路径才是连续的正确的。
​	还有一个疑问是我擅自在代码中写了如果当前节点和路径上的下一个节点不相等时，并且匹配的是第一个字符时，就允许范围其周围的节点进行结果遍历，但是这样会导致当第一个字符和0，0坐标不匹配时代码陷入死循环，因此是错误的，所以还是要老实使用下面的遍历方法。



```java
public class Solution {
    public boolean hasPath(char[] matrix, int rows, int cols, char[] str) {
            int[] flag = new int[rows * cols];
            for (int i = 0; i < rows; i++) {
                for (int j = 0; j < cols; j++) {
                    if (help(matrix, rows, cols, str, 0, flag, i, j))
                        return true;
                }
            }
            return false;


        }

    public boolean help(char[] matrix, int rows, int cols, char[] str, int cur, int[] flag, int r, int c) {
            //int row, int col表明当前的坐标的行列值
            //flag表明那些曾经在路径中出现过的节点的坐标
            //cur表示当前需要匹配的字符的位置，是下一个待访问的节点，不需关系其有效性，因为最后一个元素是‘、0’
            int index = cols * r + c;
            if (r >= 0 && r < rows && c >= 0 && c < cols && flag[index] == 0) {
                if (matrix[index] == str[cur]) {
                    cur = cur + 1;
                    if (cur >= str.length) return true;
                    flag[index] = 1;
                    if (help(matrix, rows, cols, str, cur, flag, r - 1, c) ||
                            help(matrix, rows, cols, str, cur, flag, r + 1, c) ||
                            help(matrix, rows, cols, str, cur, flag, r, c - 1) ||
                            help(matrix, rows, cols, str, cur, flag, r, c + 1))
                        return true;
                    flag[index] = 0;
                    return false;
                }
                return false;
            }
            return false;
        }

}
```





## 字符串

### 1、字符串中的顺子  #JZ45

描述：一副扑克牌,发现里面居然有2个大王,2个小王(一副牌原本是54张^_^)...他随机从中抽出了5张牌,想测测自己的手气,看看能不能抽到顺子,如果抽到的话,他决定去买体育彩票,嘿嘿！！“红心A,黑桃3,小王,大王,方片5”,“Oh My God!”不是顺子.....LL不高兴了,他想了想,决定大\小  王可以看成任何数字,并且A看作1,J为11,Q为12,K为13。上面的5张牌就可以变成“1,2,3,4,5”(大小王分别看作2和4),“So  Lucky!”。LL决定去买体育彩票啦。 现在,要求你使用这幅牌模拟上面的过程,然后告诉我们LL的运气如何，  如果牌能组成顺子就输出true，否则就输出false。为了方便起见,你可以认为大小王是0。

思路：

可以这么理解，简单来说就是要是5个数字，**最大和最小差值在5以内**，**并且没有重复数值**。用一个set来填充数据，**0不要放进去**。**set的大小加上0的个数必须为5个。此外set中数值差值在5以内**。代码如下：

```java
import java.util.*;
public class Solution {
    public boolean isContinuous(int [] numbers) {
        int len = numbers.length;
        if(len >5 || len <5) return false;
        
        TreeSet<Integer> hs = new TreeSet<>();// 存相同数会覆盖 TreeSet有first()方法获取第一个元素
        int zeroCount = 0;
        for(int i=0; i<len; i++){
            if(numbers[i] == 0) zeroCount++; //抽到大小王
            else hs.add(numbers[i]);
        }
        if(zeroCount + hs.size() != 5) return false; //存在重复
        if((hs.last() - hs.first()) < 5) return true;
        return false;
    }
}
```



### **2、字符串找出IP地址    回溯 #leet93

**描述:**  给定一个只包含数字的字符串，复原它并返回所有可能的 IP 地址格式。

有效的 IP 地址 正好由四个整数（每个整数位于 0 到 255 之间组成，且不能含有前导 0），整数之间用 '.' 分隔。

例如："0.1.2.201" 和 "192.168.1.1" 是 有效的 IP 地址，但是 "0.011.255.245"、"192.168.1.312" 和 "192.168@1.1" 是 无效的 IP 地址。

示例 1：

输入：s = "25525511135"
输出：["255.255.11.135","255.255.111.35"]

示例 5：

输入：s = "101023"
输出：["1.0.10.23","1.0.102.3","10.1.0.23","10.10.2.3","101.0.2.3"]

**思路:**

回溯的要点2——选择

    做第一步时我们有几种 选择 ？以 "25525511135" 为例：
        选 "2" 作为第一个片段
        选 "25" 作为第一个片段
        选 "255" 作为第一个片段
    可以选择切出三种长度，接下来，切第二个片段，又面临三种选择。
    这会像一棵树一样向下分支，我们用 DFS 去遍历所有选择，并且是回溯，为什么是回溯？
    因为某一步的选择可能来到一个错误的状态，得不到正确的结果，不要往下做了，并撤销最后一个选择，回到选择前的状态，去试另一个选择。
    有一个「选择、探索、撤销选择」的过程。
    好，我们现在了解了回溯的要点1——选择。

回溯的要点2——约束

    约束限制了我们的搜索分支，这道题的约束条件是：
        一个片段的长度是 1~3
        片段的值范围是 0~255
        不能是 "0x"、"0xx" 形式（这个是测试用例告诉我们的）
    我们要用这些约束在DFS中充分地剪枝，规避掉一些选择，避免去搜索一些不会产生正确答案的分支。

回溯的要点3——目标

    我们的目标决定了我们 DFS 什么时候捕捉答案，什么时候砍掉死支，不继续走了返回出来。
    **我们目标是生成 4 个有效片段，并且要用光 IP 字符串的字符。
    当遍历的节点满足该条件时，说明已经生成了一个有效组合，推入结果数组。然后回溯，继续探索。
    生成了4个有效片段，但没用光所有字符，不是有效的组合，不继续往下递归，直接返回，提前回溯。

定义dfs函数

    dfs函数传什么，用什么代表一个节点的状态？
    选择切出一个长度的片段后，剩余的子串继续递归，可以传子串，也可以传指向起始位置的指针，加上当前已经生成的片段数组，作为状态，传入 dfs 函数。
    dfs 函数做的是「按一定规则，切割从指针 start 开始的子串」。

我把递归树画出来，回溯的细节显而易见：





```java
// 方法一  回溯+迭代
public class stringToIP {
    public static void main(String[] args) {
        String s = "25525511135";
        stringToIP stringToIP = new stringToIP();
        List res = stringToIP.restoreIpAddresses(s);
        System.out.println(res); //[255.255.11.135, 255.255.111.35]
    }
    
    // 全局变量存储结果。使用到递归 需要全局变量存储结果.使用双端队列 可以再队首队尾增删元素
    List<String> res = new LinkedList<>();//存放结果
    LinkedList<String> segment = new LinkedList<>();//存放ip分片段
    public List<String> restoreIpAddresses(String s) {
        helper(s, 0);
        return res;
    }
    // 回溯方法。s字符串  指针start从头开始的下标位置 不断向后遍历直到结束
    public void helper(String s, int start){
        // 0 找到并存储合理的结果
        if(start == s.length() && segment.size() == 4){ //start最后等于字串长度 且片段是4
            StringBuilder t = new StringBuilder(); //拼接ip结果
            for (String se: segment) t.append(se).append(".");
            t.deleteCharAt(t.length() - 1); //删除最后一个逗号
            res.add(t.toString()); //存储结果
            return; //下标start已经走到头了,返回当前搜索结果
        }
        //剪枝1 已经分出了4段ip地址,但是没有用完所有字符,不符合要求
        if(start < s.length() && segment.size() == 4)return; 
        //三种长度的选择,ip可以为1位 2位 3位  其中3位时候判断数值是否大于255
        for(int i = 1;i <= 3; i++){ 
            if(start + i > s.length()) return;//剪枝2 当前长度大于字符串长度 则越界了
            if(i != 1 && s.charAt(start) == '0') return;//剪枝3 首位判断 不能是 0x  0xx格式
            String str = s.substring(start, start + i);//当前选择切出的ip片段
            if(i == 3 && Integer.parseInt(str) > 255) return; //剪枝4 不能超过255.i=3说明当前字串长度是3了 需要判断数值是否大于255
            segment.addLast(str);//添加到队列队尾
            helper(s, start + i);//基于深度优先遍历思想 沿着一条分支一直向下搜索,
            segment.removeLast();//删除队尾.上面的递归有return说明存在不符合要求的,则撤销最后的选择,回到上一个状态重新选择
        }
    }
}

//方法二  迭代
    public List<String> restoreIpAddresses(String s) {
        List<String> ret = new ArrayList<>();
        StringBuilder ip = new StringBuilder();
        for(int a = 1; a < 4 ; ++ a)
            for(int b = 1; b < 4 ; ++ b)
                for(int c = 1; c < 4 ; ++ c)
                    for(int d = 1; d < 4 ; ++ d)
                    {
                        if(a + b + c + d == s.length() )
                        {
                            int n1 = Integer.parseInt(s.substring(0, a));
                            int n2 = Integer.parseInt(s.substring(a, a+b));
                            int n3 = Integer.parseInt(s.substring(a+b, a+b+c));
                            int n4 = Integer.parseInt(s.substring(a+b+c));
                            if(n1 <= 255 && n2 <= 255 && n3 <= 255 && n4 <= 255)
                            {
                                ip.append(n1).append('.').append(n2)
                                        .append('.').append(n3).append('.').append(n4);
                                if(ip.length() == s.length() + 3) {
                                    ret.add(ip.toString());//满足要求存储结果
                                }
                                ip.delete(0, ip.length());// 都清空StringBuilder
                            }
                        }
                    }
        return ret;
    }
```



### **3、无重复字符串最长字串 let3

**标签：滑动窗口**

给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。

> 示例 1:
>
> 输入: "abcabcbb"
> 输出: 3 
> 解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
>
> 示例 2:
>
> 输入: "bbbbb"
> 输出: 1
> 解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
>
> 示例 3:
>
> 输入: "pwwkew"
> 输出: 3
> 解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
>      请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。

```java
import java.util.HashSet;
import java.util.Set;
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int len = s.length();
        int ans=0; //记录最长子串的长度
        int i = 0; //记录滑动窗口的左边
        int j = 0; //记录滑动窗口的右边
        //滑动窗口 利用hashSet 存数据并判断是否重复
        Set<Character> set = new HashSet<>();
        // 当窗口的左边或者右边到达尾端了 结束
        while(i< len && j<len){
            if(!set.contains(s.charAt(j))){
                set.add(s.charAt(j++)); //添加不重复的子串并向后移动
                ans = Math.max(ans,j-i);//找最大的子串长度
            }else{
                set.remove(s.charAt(i++));//窗口左边往右走 并删除元素
            }
        }
        return ans;
        
    }
}
```



### **4、反转字符串里的单词 let151

给定一个字符串，逐个翻转字符串中的每个单词。

> 示例 1：
>
> 输入: "the sky is blue"
> 输出: "blue is sky the"
>
> 示例 2：
>
> 输入: "  hello world!  "
> 输出: "world! hello"
> 解释: 输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。
>
> 示例 3：
>
> 输入: "a good   example"
> 输出: "example good a"
> 解释: 如果两个单词间有多余的空格，将反转后单词间的空格减少到只含一个。

```java
class Solution {
    public String reverseWords(String s) {
        int left = 0, right = s.length() - 1;
        // 去掉字符串开头的空白字符 String.trim();去掉首尾空格
        while (left <= right && s.charAt(left) == ' ') ++left;
        // 去掉字符串末尾的空白字符
        while (left <= right && s.charAt(right) == ' ') --right;

        Deque<String> d = new LinkedList(); //可替代为List<String> d = new ArrayList<>();
        StringBuilder word = new StringBuilder();
        while (left <= right) {
            char c = s.charAt(left);
            if ((word.length() != 0) && (c == ' ')) {
                // 将单词 push 到队列的头部 头插法 倒序
                d.offerFirst(word.toString()); //可替代为  d.add(0, word.toString());
                word.setLength(0); //将stringbuild清空 继续下一轮使用
            } else if (c != ' ') {
                word.append(c);
            }
            ++left;
        }
        d.offerFirst(word.toString());
        return String.join(" ", d);
    }
}

```

### 5、有效的括号 leet20

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效。

有效字符串需满足：

    左括号必须用相同类型的右括号闭合。
    左括号必须以正确的顺序闭合。

注意空字符串可被认为是有效字符串。

```java
//1 利用栈实现
public boolean isValid(String s) {
        if(s.isEmpty())
            return true;
        Stack<Character> stack=new Stack<Character>();
        for(char c:s.toCharArray()){
            if(c=='(')
                stack.push(')');
            else if(c=='{')
                stack.push('}');
            else if(c=='[')
                stack.push(']');
            else if(stack.empty()||c!=stack.pop()) //这里判断存在对应括号后就pop删除了
                return false;
        }
        if(stack.empty())//等价于 satck.isEmpty()
            return true;
        return false;
}


//2利用hashmap和队列实现
class Solution {
    private static final Map<Character,Character> map = new HashMap<Character,Character>(){{
        put('{','}'); put('[',']'); put('(',')'); put('?','?');
    }};
    public boolean isValid(String s) {
        if(s.length() > 0 && !map.containsKey(s.charAt(0))) return false;
        LinkedList<Character> stack = new LinkedList<Character>() {{ add('?'); }};
        for(Character c : s.toCharArray()){
            if(map.containsKey(c)) stack.addLast(c);
            else if(map.get(stack.removeLast()) != c) return false;
        }
        return stack.size() == 1;
    }
}
链接：https://leetcode-cn.com/problems/valid-parentheses/solution/valid-parentheses-fu-zhu-zhan-fa-by-jin407891080/
```

### 6、最小覆盖子串 leet76 美团

描述：给你一个**字符串 S**、一个**字符串 T** 。请你设计一种算法，可以在 **O(n)** 的时间复杂度内，**从字符串 S 里面找出：包含 T 所有字符的最小子串**。

> 示例：
>
> 输入：S = "ADOBECODEBANC", T = "ABC"
> 输出："BANC"

提示：

    如果 S 中不存这样的子串，则返回空字符串 ""。
    如果 S 中存在这样的子串，我们保证它是唯一的答案。

思路：使用滑动窗口，右指针向右走判断是否包含了T，若包含了则左指针向左找最小长度子串。

```java
class Solution {
    public String minWindow(String s, String t) {
         if (s == null || s.length() == 0 || t == null || t.length() == 0){
            return "";
        }
        int[] need = new int[128];
        //记录需要的字符的个数
        for (int i = 0; i < t.length(); i++) {
            need[t.charAt(i)]++;
        }
        //l是当前左边界，r是当前右边界，size记录窗口大小，count是需求的字符个数，start是最小覆盖串开始的index
        int l = 0, r = 0, size = Integer.MAX_VALUE, count = t.length(), start = 0;
        //遍历所有字符
        while (r < s.length()) {
            char c = s.charAt(r);
            if (need[c] > 0) {//需要字符c
                count--;
            }
            need[c]--;//把右边的字符加入窗口
            if (count == 0) {//窗口中已经包含所有字符
                while (l < r && need[s.charAt(l)] < 0) {
                    need[s.charAt(l)]++;//释放右边移动出窗口的字符
                    l++;//指针右移
                }
                if (r - l + 1 < size) {//不能右移时候挑战最小窗口大小，更新最小窗口开始的start
                    size = r - l + 1;
                    start = l;//记录下最小值时候的开始位置，最后返回覆盖串时候会用到
                }
                //l向右移动后窗口肯定不能满足了 重新开始循环
                need[s.charAt(l)]++;
                l++;
                count++;
            }
            r++;
        }
        return size == Integer.MAX_VALUE ? "" : s.substring(start, start + size);
    }
}
```

### 7、最长公共前缀 leet14

描述：编写一个函数来查找字符串数组中的**最长公共前缀**。

如果不存在公共前缀，返回空字符串 ""。

> 示例 1:
>
> 输入: ["flower","flow","flight"]
> 输出: "fl"

```java
//1 暴力求解 用任一个字符串的首字符 依次和其他字符串比较 只要不同则返回
public String longestCommonPrefix(String[] strs) {
    if (strs == null || strs.length == 0) return "";
    for (int i = 0; i < strs[0].length() ; i++){
        char c = strs[0].charAt(i);
        for (int j = 1; j < strs.length; j ++) {
            if (i == strs[j].length() || strs[j].charAt(i) != c)
                return strs[0].substring(0, i);             
        }
    }
    return strs[0];
}

//2 水平扫描fa
public String longestCommonPrefix(String[] strs) {
   if (strs.length == 0) return "";
   String prefix = strs[0];
   for (int i = 1; i < strs.length; i++)
       while (strs[i].indexOf(prefix) != 0) {
           prefix = prefix.substring(0, prefix.length() - 1);
           if (prefix.isEmpty()) return "";
       }        
   return prefix;
}
```

### 8、罗马数字转整数 leet13

思路：罗马数字包含以下七种字符: `I`， `V`， `X`， `L`，`C`，`D` 和 `M`。

例如， 罗马数字 2 写做 II ，即为两个并列的 1。12 写做 XII ，即为 X + II 。 27 写做  XXVII, 即为 XX + V + II 。

通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 IIII，而是 IV。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 IX。这个特殊的规则只适用于以下六种情况：

    I 可以放在 V (5) 和 X (10) 的左边，来表示 4 和 9。
    X 可以放在 L (50) 和 C (100) 的左边，来表示 40 和 90。 
    C 可以放在 D (500) 和 M (1000) 的左边，来表示 400 和 900。

```java
//思路： 保留前一个元素和后面元素比较，
// 前一个元素大于等于后一个则加上前一个元素
// 前一个元素小于后一个 则减去前一个元素
class Solution {
    public int romanToInt(String s) {
        int sum=0; //最后结果和
        int preNum = getValue(s.charAt(0)); //前一个数
        for(int i=1; i<s.length(); i++){
            int curNum = getValue(s.charAt(i));//后一个数
            if(curNum>preNum){
                sum -= preNum;
            }else{
                sum += preNum;
            }
            preNum = curNum; //前一个元素向后移动
        }
        sum += preNum; //最后一个元素一定就是想加的
        return sum;
    }
    
    //利用switch判断罗马字符的数值
    private int getValue(char ch){
        switch(ch){
            case 'I': return 1;
            case 'V': return 5;
            case 'X': return 10;
            case 'L': return 50;
            case 'C': return 100;
            case 'D': return 500;
            case 'M': return 1000;
            default: return 0;
        }
    }
}
```

9、电话号码的字母组合

描述：给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

```xml
输入："23"
输出：["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
```



```java
// 回溯方法
class Solution {
	//一个映射表，第二个位置是"abc“,第三个位置是"def"。。。
	//这里也可以用map，用数组可以更节省点内存
	String[] letter_map = {" ","*","abc","def","ghi","jkl","mno","pqrs","tuv","wxyz"};
	//最终输出结果的list
	List<String> res = new ArrayList<>();
    public List<String> letterCombinations(String digits) {
		//注意边界条件
		if(digits==null || digits.length()==0) {
			return new ArrayList<>();
		}
		iterStr(digits, new StringBuilder(), 0);
		return res;
	}

	//递归函数
	void iterStr(String str, StringBuilder letter, int index) {
		//递归的终止条件，注意这里的终止条件看上去跟动态演示图有些不同，主要是做了点优化
		//动态图中是每次截取字符串的一部分，"234"，变成"23"，再变成"3"，最后变成""，这样性能不佳
		//而用index记录每次遍历到字符串的位置，这样性能更好
		if(index == str.length()) {
			res.add(letter.toString());
			return;
		}
		//获取index位置的字符，假设输入的字符是"234"
		//第一次递归时index为0所以c=2，第二次index为1所以c=3，第三次c=4
		//subString每次都会生成新的字符串，而index则是取当前的一个字符，所以效率更高一点
		char c = str.charAt(index);
		//map_string的下表是从0开始一直到9， c-'0'就可以取到相对的数组下标位置
		//比如c=2时候，2-'0'，获取下标为2,letter_map[2]就是"abc"
		int pos = c - '0';
		String map_string = letter_map[pos];
		//遍历字符串，比如第一次得到的是2，页就是遍历"abc"
		for(int i=0;i<map_string.length();i++) {
			//调用下一层递归，用文字很难描述，请配合动态图理解
            letter.append(map_string.charAt(i));
            //如果是String类型做拼接效率会比较低
			//iterStr(str, letter+map_string.charAt(i), index+1);
            iterStr(str, letter, index+1);
            letter.deleteCharAt(letter.length()-1);
		}
	}

}
```







## 数组

### **1、调整数组顺序，使得奇数位于偶数前面  #JZ13

描述：输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有的奇数位于数组的前半部分，所有的偶数位于数组的后半部分，**并保证奇数和奇数，偶数和偶数之间的相对位置不变**。

思路：1.如果**没有保证奇数和奇数，偶数和偶数之间的相对位置不变**，可以使用**快排**的思路 如下：

**时间复杂度 O(N)**

```java
public int[] exchange(int[] arr){
    int left=0;
    int right = arr.length-1;
    while(left < right){
        // 从左往右找偶数 找不到往右走 找到偶数停止
        while(left<right && arr[left] % 2 !=0) left ++;
        // 从右往左找奇数 找不到往左走 找到奇数停止
        while(left<right && arr[right] % 2 !=1) right--;
        // 把每一轮找到的奇数偶数交换位置
        if(left < right){
            int temp = arr[left]; //偶数
            arr[left] = arr[right];
            arr[right] = temp;
        }
    }
    return arr; //返回交换后的数组
}
```

2.但是对于本题 思路如下： **因为需要保证奇偶之间的相对位置 就需要使用顺序遍历比较了**

 * 1.要想保证原有次序，则只能**顺次往后移动**或相邻交换。

 * 2.i从左向右遍历，找第一个偶数,找不到则后移。

 * 3.**j从i+1开始向后找**，直到找到第一个奇数。

 * 4.**将[i,...,j-1]的元素整体后移一位**，最后将找到的奇数放入i位置，然后i++。

 * 5.終止條件：j向後遍歷查找失敗。

 <img src="C:\Users\GSY\Desktop\刷刷刷IMG\Picture_2020-07-13_00-46-37.png" style="zoom:50%;" />

 ```java
public class Solution {
    public void reOrderArray(int [] array) {
        int len = array.length;
        if(len == 0 || array == null) return;
        int i=0, j=0;
        while(i < len){
            //找到偶数 当前不是则往后移动
            while(i < len && array[i]%2 != 0) i++;
            j = i+1; // j在i后面找奇数
            while(j < len && array[j]%2 != 1) j++; //直到找到奇数
            //交换奇数到前面偶数的位置 偶数整体后移
            if(j < len){ // *终止条件之一！！！ j<len
                int temp = array[j]; //先保留奇数zhi
                // **所有偶数向后移动一位 这里就是保持原有顺序不变！！！!!!!
                for(int j2=j-1; j2>=i; j2--)
                {  
                    array[j2+1] = array[j2];
                }
                array[i++] = temp; //奇数放前面i位置
            }else break; //终止条件！！！！！！！跳出大循环
        } //while
    }
}
 ```

 

### ***2、找众数-数组中出现次数超过一半的数字  #JZ28

描述：数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。例如输入一个长度为9的数组{1,2,3,2,2,2,5,4,2}。由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。如果不存在则输出0。

思路：链接：https://www.nowcoder.com/questionTerminal/e8a1b01a2df14cb2b228b30ee6a92163?answerType=1&f=discussion

用一般的排序也可以完成这道题目，但是如果那样完成的话就可能太简单了。
用preValue记录上一次访问的值，count表明当前值出现的次数，如果下一个值和当前值相同那么count++；如果不同count--，**减到0的时候就要更换新的preValue值了**，**因为如果存在超过数组长度一半的值，那么最后preValue一定会是该值。**

​	**如果有符合条件的数字，则它出现的次数比其他所有数字出现的次数和还要多。** 

   在遍历数组时保存两个值：一是数组中一个数字，一是次数。遍历下一个数字时，若它与之前保存的数字相同，则次数加1，否则次数减1；若次数为0，则保存下一个数字，并将次数置为1。遍历结束后，所保存的数字即为所求。然后再判断它是否符合条件即可。 

```java
public class Solution {
    public int MoreThanHalfNum_Solution(int [] array) {
        if(array == null || array.length == 0) return 0;
        int len = array.length;
        int pre = array[0]; //记录上一次的数值
        int count = 1; //pre出现过的次数 
        for(int i=1; i<len; i++){
            if(pre == array[i]){
                count++; //相同数则加一
            }else{
                count--; //不相同数则加一
                if(count == 0){ // **** 减到0后 以当前i位置的新数值继续找
                    pre = array[i];
                    count =1; //** 计数器重新为1
                }
            }
        }
		//判断是否真的大于长度的一般，因为上一次遍历只是保证如果存在超过一半的数就是preValue，但不代表preValue一定会超过一半
        int num = 0; 
        for(int i=0; i<len; i++){
            if(array[i] == pre) num++;
        }
        return (num > len/2) ? pre : 0; //大于数组三分之一的就是len/3
    }
}
```



### 3、顺时针打印矩阵  #JZ19

题目：输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字，例如，如果输入如下4 X 4矩阵： 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 则依次打印出数字1,2,3,4,8,12,16,15,14,13,9,5,6,7,11,10.

思路：刷 LeetCode 看到的大神题解，感觉容易理解且好写
简单来说，就是不断地收缩矩阵的边界
定义四个变量代表范围，up、down、left、right

1. 向右走存入整行的值，当存入后，该行再也不会被遍历，代表上边界的 up 加一，同时判断是否和代表下边界的 down 交错 
2. 向下走存入整列的值，当存入后，该列再也不会被遍历，代表右边界的 right 减一，同时判断是否和代表左边界的 left 交错 
3. 向左走存入整行的值，当存入后，该行再也不会被遍历，代表下边界的 down 减一，同时判断是否和代表上边界的 up 交错 
4. 向上走存入整列的值，当存入后，该列再也不会被遍历，代表左边界的 left 加一，同时判断是否和代表右边界的 right 交错 

```java
import java.util.ArrayList;
public class Solution {
    public ArrayList<Integer> printMatrix(int [][] arr) {
        ArrayList<Integer> list = new ArrayList<>();
       if(arr == null || arr.length == 0 || arr[0].length ==0) return null;
        int up = 0;
        int down = arr.length-1;
        int left = 0;
        int right = arr[0].length-1;
        while(true){
            for(int col=left; col<=right; col++){ //存储最上面一行
                list.add(arr[up][col]); //****** 列改变 行不变
            }
            up++; //向下一步
            if(up > down) break; //判断是否越界
            for(int row=up; row<=down; row++){//存储最右边一行
                list.add(arr[row][right]); //****** 行改变 列不变
            }
            right--; //向左移动
            if(right < left) break;
            for(int col=right; col>=left; col--){//存储最下边一行*** col是大值>left
                list.add(arr[down][col]); //****** 列改变 行不变
            }
            down--;
            if(down < up) break; 
            for(int row=down; row>=up; row--){ //存储最左边一行 ***row是大值>up
                list.add(arr[row][left]); //****** 行改变 列不变
            }
            left++;
            if(left > right) break;
        }
        return list;
    }
}
```

### 4、把数组排成最小的数  #JZ32

题目：输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3，32，321}，则打印出这三个数字能排成的最小数字为321323。

思路：1. 把数组放入一个列表中 2.利用Collections.sort 重写Comparator方法 实现递增排序 小的在前

**Note：Arrays.sort()对数组排序；Collections.sort()对集合进行排序 但是底层也是用的Arrays.sort()；**

```java
import java.util.*;

public class Solution {
    public String PrintMinNumber(int [] numbers) {
        String res = ""; 
        ArrayList<Integer> list = new ArrayList<>();
        int len = numbers.length;
        for(int i =0; i<len; i++){ //存入集合中
            list.add(numbers[i]);
        }
        Collections.sort(list, new Comparator<Integer>(){ // 利用集合排序
           public int compare(Integer s1, Integer s2){
               String ss1 = s1+""+s2;  // **** 比较的是字符串
               String ss2 = s2+""+s1;
               return ss1.compareTo(ss2);
           } 
        });
        for(int j: list){
            res+=j;
        }
        return res;
    }
}
```



### 5、数字在排序数组中出现的次数  #JZ37

题目：统计一个数字在排序数组中出现的次数。

思路：二分的前提：有序（**一提到有序，必须立马想到二分！**）

```java
int[] array = {1, 2, 3, 3, 3, 5} k=3
=> lowerIndex=2 upperIndex=5   res = 5 - 2 =3 出现了3此
int[] array = {1, 2, 4, 5} k=3
=> lowerIndex=2 upperIndex=2
```

复习一下二分查找:

```java
public static binarySearch(int[] arr, int k){
    if(arr == null || arr.length == 0) return -1;
    int l=0, r=arr.length-1;
    while(l <= r){
        int mid = l+ (r-l)/2;
        if(arr[mid] == k) return mid;
        else if(arr[mid] > k) r = mid-1;
        else l = mid+1
    }
    return -1;
}
```



```java
public static int getNumberOfK(int[] arr, int k){
    if(arr.length == 0 || arr == null) return 0;
    int lowerIndex = lowerBound(arr, k); //获取重复元素第一次出现的位置
    int upperIndex = upperBound(arr, k); //最后一次出现的位置
    return upperIndex - lowerIndex;
}
public static int lowerBound(int[] arr, k){
    int l=0, r=arr.length-1, mid=0;
    while(l <= r){
        mid = (l+r) >> 1; //等同于 l+(r-l)/2
        if(arr[mid] < k) l = mid+1;  //注意这里是k>arr[mid]  不考虑相等情况
        else r = mid -1;
    }
    return l;
}
public static int upperBound(int[] arr, k){
    int l=0, r=arr.length-1, mid=0;
    while(l <= r){
        mid = (l+r) >> 1; //等同于 l+(r-l)/2
        if(arr[mid] <= k) l = mid+1;  //注意这里是k=>arr[mid]  考虑相等情况
        else r = mid -1;
    }
    return l;
}

```

### **6、数组中只出现一次的数字  #JZ40

描述:一个整型数组里**除了两个数字之外**，其他的数字**都出现了两次**。请写程序找出**这两个**只出现一次的数字。

leetcode： 给定一个非空整数数组，**除了某个元素只出现一次以外**，其余每个元素均出现两次。找出那个**只出现了一次**的元素(**见下方代码**)

思路: gsy：利用HashSet的哈希表不能存放相同元素的特征,如果存入相同元素则删除 最后留下就是不重复的数字，时间空间复杂度都是O(N)

思路2：利用异或操作！时间复杂度很低!  异或相同为0  不同为1，所以其余元素相同结果为0，而0^任意元素=任意元素

```java
import java.util.HashSet;
import java.util.Iterator;

public class FindNumsAppearOnce {
    public static void main(String[] args) {
        int arr[] = {3,4,5,1,2,3,4,5};
        int[] n1= new int[1];
        int[] n2= new int[1];
        FindNumsAppearOnce(arr,n1,n2);
    }
    //******************  解题方法******************
    public static void FindNumsAppearOnce(int [] array,int num1[] , int num2[]) {
        HashSet<Integer> hs = new HashSet<Integer>();
        for (int i=0; i<array.length; i++){ //所有元素放入HashSet
            if (!hs.add(array[i])){  //没有重复的=true则add进去,有重复的=false则删除
                hs.remove(array[i]); //删除指定的数字!!!
            }
        }
//        Iterator<Integer> iterator = hs.iterator();  //存放结果方式1  迭代器
//        if (iterator.hasNext()){
//            num1[0] = iterator.next();
//        }
//        if (iterator.hasNext()){
//            num2[0] = iterator.next();
//        }
        for (int i=0; i<hs.size(); i++){   //存放结果方式2 集合转为数组取下标  效率高
            num1[0] = (Integer) hs.toArray()[0];// 集合对象.toArray()[i] 转为数组
            num2[0] = (Integer) hs.toArray()[1];
        }
        System.out.println(num1[0]);
        System.out.println(num2[0]);
    }
}
```

```java
//扩展：leetcode 给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。
class Solution {
    public int singleNumber(int[] nums) {
        // 方法1 利用HashSet
        // HashSet<Integer> hs = new HashSet<>();
        // for(int i=0; i<nums.length; i++){
        //     if(!hs.add(nums[i])){
        //         hs.remove(nums[i]);
        //     }
        // }
        // Integer res = (Integer)hs.toArray()[0];
        // return res;
        
    // 方法2 利用异或方法
    int ans = nums[0];
    if (nums.length > 1) {
        for (int i = 1; i < nums.length; i++) {
            ans = ans ^ nums[i]; //异或相同为0  不同为1，所以其余元素相同结果为0，而0^任意元素=任意元素
        }
    }
    return ans;

    }
}
```





### 7、寻找和为S的两个数  #JZ42

描述：输入一个**递增排序**的数组和一个数字S，在数组中查找两个数，使得他们的和正好是S，如果有多对数字的和等于S，**输出两个数的乘积最小的**。

输出描述: 对应每个测试案例，输出两个数，小的先输出。

思路: gsy：利用HashMap,key存放数,value存放下标,利用hashMap判断是否以及有相同的key方法(hs.containsKey)来判断hashmap中是否已经有和为s的另一个数.

思路来源于leetcode的和为s的两个数.但这个不是最优的方法!

首先 回顾一下 **leetcode中题目"两数之和"**:给一个整数数组和一个目标值s,找出和为s的**两个整数 返回下标**

```java
public int[] twoSum(int[] arr, int t){
    if(arr==null || arr.length==0) return null;
    Map<Integer,Integer> map = new HashMap<>();
    for(int i=0; i<arr.length; i++){
        int temp = t - arr[i];
        if(map.containsKey(temp)){return new int[]{map.get(temp), i};}
        map.put(arr[i], i);
    }
    return null;
}
```

其次,利用上面的思路 我们可以使用相同的方法解决 时间复杂度：O(n)空间复杂度：O(n)  如下:

```java
import java.util.*;
public class Solution { 
    public ArrayList<Integer> FindNumbersWithSum(int [] array,int sum) {
        ArrayList<Integer> list = new ArrayList<Integer>();
        if(array == null || array.length==0) return list;
        HashMap hm = new HashMap();
        int minRes = 0; //保存成绩最小的两个数
        for(int i=0; i<array.length; i++){
            int target = sum - array[i];
            if(hm.containsKey(target)){
                if(minRes==0){
                    list.add(target);
                    list.add(array[i]);
                    minRes = target*array[i];
                }
                if(minRes!=0 && minRes > target*array[i]){ //判断是否有乘积更小的两个数
                    list.clear();
                    list.add(target);
                    list.add(array[i]);
                    minRes = target*array[i];
                }
            }
            hm.put(array[i],i);
        }
        return list;
    }
}
```

**(最优方法)最后**,大佬们的思路如下:  **双指针+最外层乘积最小**

因为**数组是有序的**，所以可以用**双指针**，指向数组的**首尾**，具体步骤如下：
1.初始化：指针i指向数组首， 指针j指向数组尾部

2.如果arr[i] + arr[j] == sum , 说明是可能解

3.否则如果arr[i] + arr[j] > sum, 说明和太大，所以--j**

4.否则如果arr[i] + arr[j] < sum, 说明和太小，所以++i

时间复杂度：O(n)   空间复杂度：O(1)

```java
import java.util.ArrayList;
public class Solution{
    public ArrayList<Integer> FindNumberWithSum(int[] arr, int sum){
        ArrayList<Integer> list = new ArrayList<>();
        if(arr==null || arr.length==0) return list;
        int smallIndex = 0, bigIndex=arr.length-1; //首尾指针
        while(smallIndex < bigIndex){
            //如果相同就放进去返回结果 因为最外层乘积最小  就不要往里计算了
            if(arr[smallIndex]+arr[bigIndex] == sum){
                list.add(arr[smallIndex]);
                list.add(arr[bigIndex]);
                break; //直接跳出循环 结束 因为最外层乘积最小  
            }else if(arr[smallIndex]+arr[bigIndex] < sum){ //由于数组是递增的
                smallIndex++; 
            }else{bigIndex--;}
        }
        return list;
    }
}
```



#### 7.1、三数之和 #leet15

给你一个包含 n 个整数的数组 nums，判断 nums 中**是否存在三个元素 a，b，c ，使得 a + b + c = 0** ？请你找出**所有满足条件且不重复**的三元组。

**思路：**遍历所有数字，以当前数子和左右指针的数子相加 判断和是否为0

注意：答案中不可以包含重复的三元组

> 示例：
>
> 给定数组 nums = [-1, 0, 1, 2, -1, -4]，
>
> 满足要求的三元组集合为：
> [
>   [-1, 0, 1],
>   [-1, -1, 2]
> ]

```java
public List<List<Integer>> threeSum(int[] nums) {
    List<List<Integer>> ans = new ArrayList<>();
    int len = nums.length;
    if(nums == null || len < 3) return ans;
    Arrays.sort(nums); // 排序 升序
    for(int i=0; i<len; i++){
        //因为是升序并且要求和=0，所以当前数字大于0 三数之和就一定大于0了 退出结束
        if(nums[i] > 0) break; 
        if(i>0 && nums[i]==nums[i-1]) continue; // 去重
        int l = i+1;  //i的左边位置
        int r = len -1;//i的右边位置
        while(l < r){
            int sum = nums[i]+nums[l]+nums[r];
            if(sum == 0){
                ans.add(Arrays.asList(nums[i],nums[l],nums[r]));
                while(l<r && nums[l] == nums[l+1]) l++; // 去重
                while(l<r && nums[r] == nums[r-1]) r--; //去重
                l++;
                r--;
            }
            else if(sum < 0) l++; //三数和小于0,l向左走 找更大的数
            else if(sum > 0) r--;
        }
    }
    return ans;
}
```



#### 7.2、和为S的连续整数序列 

输入一个正整数 target ，输出所有和为 target 的**连续正整数序列**（至少含有两个数）。

序列内的数字由小到大排列，不同序列按照首个数字从小到大排列。

 思路：**滑动窗口**



```java
public int[][] findContinuousSequence(int target) {
    int i = 1; // 滑动窗口的左边界
    int j = 1; // 滑动窗口的右边界
    int sum = 0; // 滑动窗口中数字的和
    List<int[]> res = new ArrayList<>();//泛型错误编译不通过！使用List<ArrayList>

    while (i <= target / 2) {
        if (sum < target) {
            // 右边界向右移动
            sum += j;
            j++;
        } else if (sum > target) {
            // 左边界向右移动
            sum -= i;
            i++;
        } else {
            // 记录结果
            int[] arr = new int[j-i];
            for (int k = i; k < j; k++) {
                arr[k-i] = k;
            }
            res.add(arr);
            // 左边界向右移动
            sum -= i;
            i++;
        }
    }
    return res.toArray(new int[res.size()][]);
}
```





### 8、数组中重复的数字 #JZ50

描述：在一个长度为n的数组里的所有数字都在0到n-1的范围内。       数组中某些数字是重复的，但不知道有几个数字是重复的。也不知道每个数字重复几次。请找出数组中任意一个重复的数字。       例如，如果输入长度为7的数组{2,3,1,0,2,5,3}，那么对应的输出是第一个重复的数字2。

思路：利用排序最简单，但是这里**利用boolean类型的数组去判断**，时间复杂度O(n)  空间占用1byte

```JAVA
//boolean 只占一位 相对比较省空间
public boolean duplicate(int[] num, int len, int[]dup){
    boolean[] k = new boolean[len]; //声明boolen数组 存在num中的值 如果遇到相同的则为true 
    for(int i=0; i<len; i++){
        if(k[num[i]] == true){
            dup[0] = num[i]; //存储并返回结果
            return true;
        }
        k[num[i]] = true; // 存储num中的这个数值设置为true
    }
    return false;
}
```

### 9、构建乘机数组 #JZ51

描述：给定一个数组A[0,1,...,n-1],请构建一个数组B[0,1,...,n-1],其中B中的元素B[i]=A[0]*A[1]*...*A[i-1]*A[i+1]*...*A[n-1]。不能使用除法。（注意：规定B[0] = A[1] * A[2] * ... * A[n-1]，B[n-1] = A[0] * A[1] * ... * A[n-2];）

剑指的思路： **B[i]的值可以看作下图的矩阵中每行的乘积。**

 	下三角用连乘可以很容求得，上三角，从下向上也是连乘。 

 	因此我们的思路就很清晰了，先算下三角中的连乘，即我们先算出B[i]中的一部分，然后倒过来按上三角中的分布规律，把另一部分也乘进去。 

<img src="/构建数组乘积_2020-07-21_14-23-04.png" style="zoom:50%;" />

```java
//B[i]=A[0]*A[1]*...*A[i-1]*A[i+1]*...*A[n-1]  结合上图
//上三角  从左到右算 B[i]=A[0]*A[1]*...*A[i-1]
//下三角  从右到左算B[i]*=A[i+1]*...*A[n-1]
public int[] multipy(int[] A){
    int len = A.length;
    int[] B = new int[len];
    if(len != 0){
        int res = 1;
        // 从左往右计算
        for(int  i=0; i<len; i++){
            B[i] = res;
            res *= A[i];
        }
        // 从右往左计算
        res = 1;
        for(int j=len-1; j>=0; j--){
            B[j] *= res;
            res *= A[j];
        }
    }
    return B;
}
```



### **10、滑动窗口的最大值 #JZ64

**标签：堆**  |  双端队列

描述：给定一个数组和滑动窗口的大小，找出所有滑动窗口里数值的最大值。例如，如果输入数组{2,3,4,2,6,2,5,1}及滑动窗口的大小3，那么一共存在6个滑动窗口，他们的最大值分别为{4,4,6,6,6,5}； 针对数组{2,3,4,2,6,2,5,1}的滑动窗口有以下6个：  {[2,3,4],2,6,2,5,1}， {2,[3,4,2],6,2,5,1}，{2,3,[4,2,6],2,5,1}，{2,3,4,[2,6,2],5,1}， {2,3,4,2,[6,2,5],1}，  {2,3,4,2,6,[2,5,1]}。

思路：

**方法一  主要就是1.控制窗口的移动  2.每次把窗口里的数进行排序获得最大值，取出来放在list中。**

而对于**无序大量的数组排序** 最好的排序方法是**堆排序** O(nLog)

时间复杂度：*O*(*N*log(*k*))。在大小为 `k` 的堆中插入一个元素消耗log(*k*) 时间，

**note：注意自定义堆的写法 new Comparator<Integer>(){-----}**

```JAVA
import java.util.PriorityQueue;
import java.util.Comparator;
import java.util.ArrayList;
public class Solution{
    public ArrayList<Integer> maxInWindows(int [] num, int size){
        ArrayList<Integer> res = new ArrayList<>();
    	if(num==null || num.length==0 || size>num.length ||size<=0) return res;
        //1 利用优先级队列 内部实现排序接口 创建大顶堆 从大到小排序
        //************注意 自定义堆的写法*************** new Comparator<Integer>()
        PriorityQueue<Integer> heap = new PriorityQueue<>(new Comparator<Integer>(){
           public int compare(Integer o1, Integer o2){
               return o2-o1; //降序 每次取出最小值放最后  头位置就是最大的数
           } 
        });
        //2  存放第一个滑动窗口
        for(int i=0; i<size; i++){
            heap.offer(num[i]);
        }
        res.add(heap.peek());
        //3 存放后续的滑动窗口最大的值
        for(int j=1; j<num.length-size+1; j++){
            heap.remove(num[j-1]); //每次移动前一个数 相当于窗口向后移动
            heap.offer(num[j+size-1]);//获得下一个窗口 多加的一个数
            res.add(heap.peek());//peek获取首元素不删除
        }
        return res;
	}
}
```

**方法二  利用双端队列  时间复杂度是线性阶O(n)  较好**

```java
/*
  思路： 遍历数组 L R 为滑窗左右边界 只增不减
        双向队列保存当前窗口中最大的值的数组下标 双向队列中的数从大到小排序，
        新进来的数如果大于等于队列中的数 则将这些数弹出 再添加
        当R-L+1=k 时 滑窗大小确定 每次R前进一步L也前进一步 保证此时滑窗中最大值的
        数组下标在[L，R]中，并将当前最大值记录
  举例： nums[1，3，-1，-3，5，3，6，7] k=3
     1：L=0，R=0，队列【0】 R-L+1 < k
            队列代表值【1】
     2: L=0,R=1, 队列【1】 R-L+1 < k
            队列代表值【3】
     解释：当前数为3 队列中的数为【1】 要保证队列中的数从大到小 弹出1 加入3
          但队列中保存的是值对应的数组下标 所以队列为【1】 窗口长度为2 不添加记录
     3: L=0,R=2, 队列【1，2】 R-L+1 = k ,result={3}
            队列代表值【3，-1】
     解释：当前数为-1 队列中的数为【3】 比队列尾值小 直接加入 队列为【3，-1】
          窗口长度为3 添加记录记录为队首元素对应的值 result[0]=3
     4: L=1,R=3, 队列【1，2，3】 R-L+1 = k ,result={3，3}
            队列代表值【3，-1,-3】
     解释：当前数为-3 队列中的数为【3，-1】 比队列尾值小 直接加入 队列为【3，-1，-3】
          窗口长度为4 要保证窗口大小为3 L+1=1 此时队首元素下标为1 没有失效
          添加记录记录为队首元素对应的值 result[1]=3
     5: L=2,R=4, 队列【4】 R-L+1 = k ,result={3，3，5}
            队列代表值【5】
     解释：当前数为5 队列中的数为【3，-1，-3】 保证从大到小 依次弹出添加 队列为【5】
          窗口长度为4 要保证窗口大小为3 L+1=2 此时队首元素下标为4 没有失效
          添加记录记录为队首元素对应的值 result[2]=5
    依次类推 如果队首元素小于L说明此时值失效 需要弹出
*/
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        if(nums==null||nums.length<2) return nums;
        // 双向队列 保存当前窗口最大值的数组位置 保证队列中数组位置的数按从大到小排序
        LinkedList<Integer> list = new LinkedList();
        // 结果数组
        int[] result = new int[nums.length-k+1];
        for(int i=0;i<nums.length;i++){
            // 保证从大到小 如果前面数小 弹出
            while(!list.isEmpty()&&nums[list.peekLast()]<=nums[i]){
                list.pollLast();
            }
            // 添加当前值对应的数组下标
            list.addLast(i);
            // 初始化窗口 等到窗口长度为k时 下次移动在删除过期数值
            if(list.peek()<=i-k){
                list.poll();   
            } 
            // 窗口长度为k时 再保存当前窗口中最大值
            if(i-k+1>=0){
                result[i-k+1] = nums[list.peek()];
            }
        }
        return result;
    }
}
```





### 11、机器人的运动范围

描述：地上有一个m行和n列的方格。一个机器人从坐标0,0的格子开始移动，每一次只能向左，右，上，下四个方向移动一格，但是不能进入行坐标和列坐标的数位之和大于k的格子。 例如，当k为18时，机器人能够进入方格（35,37），因为3+5+3+7 = 18。但是，它不能进入方格（35,38），因为3+5+3+8 = 19。请问该机器人能够达到多少个格子？

**核心思路：**

   **1.从(0,0)开始走，每成功走一步标记当前位置为true,然后从当前位置往四个方向探索，**

   **返回1 + 4 个方向的探索值之和。**

   **2.探索时，判断当前节点是否可达的标准为：**

   **1）当前节点在矩阵内；**

   **2）当前节点未被访问过；**

   **3）当前节点满足limit限制。**

```java
public class Solution {
    public int movingCount(int threshold, int rows, int cols)
    {
        if(rows<=0 || cols<=0 || threshold<0) return 0;
        boolean[][] isVisited = new boolean[rows][cols];
        int count = movingCount(threshold, rows, cols, 0,0,isVisited);
        return count;
    }
    private int movingCount(int threshould, int rows, int cols, int row,
                            int col,boolean[][] isVisited){
        //探索时，判断当前节点是否可达的标准为
        if(row<0 || col<0 ||row>=rows ||col>=cols || isVisited[row][col]
          || cal(row)+cal(col)>threshould) return 0;
        isVisited[row][col] = true;
        return 1+movingCount(threshould,rows,cols,row-1,col,isVisited)
            +movingCount(threshould,rows,cols,row+1,col,isVisited)
            +movingCount(threshould,rows,cols,row,col-1,isVisited)
            +movingCount(threshould,rows,cols,row,col+1,isVisited);
    }
    private int cal(int num){
        int sum=0;
        while(num > 0){
            sum+=num%10;
            num /= 10;
        }
        return sum;
    }
}
```



### 12、有序矩阵的第k小的元素  #leet378

描述：给定一个 n x n 矩阵，其中每行和每列元素均按升序排序，找到矩阵中第 k 小的元素。
请注意，它是排序后的第 k 小元素，而不是第 k 个不同的元素。

示例：matrix = [
   [ 1,  5,  9],
   [10, 11, 13],
   [12, 13, 15]],
k = 8,

结果 返回 13。

**思路**：利用排序 排出来从小到大 第k个就是第k小的元素，我是利用**堆排序(大顶堆)实现的**

但是**最优的方法是使用 二分查找** 思路，无奈没看懂思路

```java
class Solution {
    public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        int left = matrix[0][0];
        int right = matrix[n - 1][n - 1];
        while (left < right) {
            int mid = left + ((right - left) >> 1);
            if (check(matrix, mid, k, n)) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        return left;
    }

    public boolean check(int[][] matrix, int mid, int k, int n) {
        int i = n - 1;
        int j = 0;
        int num = 0;
        while (i >= 0 && j < n) {
            if (matrix[i][j] <= mid) {
                num += i + 1;
                j++;
            } else {
                i--;
            }
        }
        return num >= k;
    }
}

链接：https://leetcode-cn.com/problems/kth-smallest-element-in-a-sorted-matrix/solution/you-xu-ju-zhen-zhong-di-kxiao-de-yuan-su-by-leetco/
来源：力扣（LeetCode）
```

### 13 合并两个有序数组 leet88

描述：给你两个有序整数数组 *nums1* 和 *nums2*，请你将 *nums2* 合并到 *nums1* 中*，*使 *nums1* 成为一个有序数组。

> 输入:
> nums1 = [1,2,3,0,0,0], m = 3
> nums2 = [2,5,6],       n = 3
>
> 输出: [1,2,2,3,5,6]。
>

```java
//方法1 合并后在排序 时间复杂度较差，为O((n+m)log⁡(n+m))
class Solution {
  public void merge(int[] nums1, int m, int[] nums2, int n) {
    System.arraycopy(nums2, 0, nums1, m, n);//nums2元素复制到nums1中，0和m下标  n是长度
    Arrays.sort(nums1);//升序 快排和归并排序
  }
}

//方法2 利用双指针 分别对比两个数组大小 然后排在新数组中 
//时间复杂度 : O(n+m)  空间复杂度 : O(m)
class Solution {
  public void merge(int[] nums1, int m, int[] nums2, int n) {
    // Make a copy of nums1.
    int [] nums1_copy = new int[m];
    System.arraycopy(nums1, 0, nums1_copy, 0, m);

    // Two get pointers for nums1_copy and nums2.
    int p1 = 0;
    int p2 = 0;

    // Set pointer for nums1
    int p = 0;

    // Compare elements from nums1_copy and nums2
    // and add the smallest one into nums1.
    while ((p1 < m) && (p2 < n))
      nums1[p++] = (nums1_copy[p1] < nums2[p2]) ? nums1_copy[p1++] : nums2[p2++];

    // if there are still elements to add
    if (p1 < m)
      System.arraycopy(nums1_copy, p1, nums1, p1 + p2, m + n - p1 - p2);
    if (p2 < n)
      System.arraycopy(nums2, p2, nums1, p1 + p2, m + n - p1 - p2);
  }
}
```

### 14、删除排序数组中的重复项 leet26

给定一个**排序**数组，你需要在 **原地** 删除重复出现的元素，使得**每个元素只出现一次**，返回移除后数组的**新长度**。

不要使用额外的数组空间，你必须在 **原地** 修改输入数组 并在使用 **O(1) 额外空间**的条件下完成。

**思路：** 使用**快慢指针**，快指针向后遍历并不断和慢指针比较，遇到重复元素快指针向后走，遇到不重复元素则将快指针位置的元素替换到慢指针的下一位置上。

```java
public int removeDuplicates(int[] nums) {
    if(nums.length == 0 || nums==null) return 0;//检验
    // 思路 使用快慢指针表示 有重复元素快指针则往后走
    int i = 0; //慢指针   j快指针
    for(int j=1;j<nums.length;j++){
        if(nums[i] != nums[j]){
            //**优化 避免0 1 2 3 无重复的也要替换一遍
            i++;//先i加1  判断j和i是否在同一位置
            if(j-i>0){ //只有不在同一位置 即有重复才替换
                nums[i] = nums[j];//i先向后移动
            }
        }
    }
    return i+1; //因为i的起始下标是0 所以+1
}
```



#### 14.1、删除某值的元素

**描述：**给一个数组 nums 和一个值 val，需要 **原地** 移除**所有数值等于 val 的元素**，并返回移除后数组的**新长度**。

不要使用额外的数组空间，你必须仅使用 O(1) 额外空间并 原地 修改输入数组。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

**思路：**与14题极度相似！    我们可以保留**两个指针 i 和 j，其中 i 是慢指针，j 是快指针。**

算法：**当 nums[j] 与给定的值相等时，递增 j以跳过该元素**。只要 nums[j]≠val，我们就复制 nums[j]到 nums[i]并同时递增两个索引。重复这一过程，直到 j 到达数组的末尾，该数组的新长度为 i。

时间复杂度 O(N) 假设数组总共有 n 个元素，i 和 j 至少遍历2n 步 .                 空间复杂度O(1)

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        if(nums==null || nums.length==0) return 0;
        //1 快慢指针 i慢指针   j快指针
        int i=0;
        for(int j=0; j<nums.length; j++){
            //2 如果元素等于val则直接跳过 否则做交换,这里显然做了重复复制
            if(nums[j] != val){
                nums[i] = nums[j];
                i++;
            }
        }
        return i;// 3  i就代表剩下元素个数
    }
}
```

### 15、数组加一 leet66

描述：给定一个由**整数**组成的**非空**数组所表示的非负整数，在该数的基础上加一。

最高位数字存放在数组的首位， 数组中每个元素只存储**单个**数字。

你可以假设除了整数 0 之外，这个整数不会以零开头。

**示例 1:**

```xml
输入: [1,2,3]
输出: [1,2,4]
解释: 输入数组表示数字 123。
```

思路:  从最简单不需要进位考虑 就直接加一返回, 加一后需要进位则继续向后判断是否需要进位, 特殊情况[9] [9,9]需要将数组大小扩充加一位!

```java
//方法1  gsy  想的方法 不是很简介
public int[] plusOne(int[] digits) {
    if(digits==null || digits.length==0) return null;
    int t=0;
    int first = digits[digits.length-1];
    //1  判断最后一位是否为9 不是的话加一直接结束
    if(first == 9) {
        digits[digits.length-1]=0;
        t=1;
    }else{
        digits[digits.length-1] +=1;
        return digits;
    }
    //2 如果最后一位是9  则进位 并判断后面的是否需要进位
    for(int i=digits.length-2; i>=0; i--){
        if(digits[i]==9) {
            digits[i]=0;
            t=1;
        }else{
            digits[i] += t;
            t=0;
            break;
        }
    }
    //3  判断是否为[9] [9,9]这类情况 需要扩展数组加一位
    if(t==1) {
        int[] res = new int[digits.length+1];
        res[0] = 1; 
        return res;
    }else {
        return digits; 
    }
}

//2  leetcode大神思路 简洁
public int[] plusOne(int[] digits) {
    for (int i = digits.length - 1; i >= 0; i--) {
        digits[i]++; //当前位先加一
        digits[i] = digits[i] % 10;//判断当前位加一后是否为0 即进位，取10还是原来值！！！
        if (digits[i] != 0) return digits; //如果没有进位就直接结束啦
    }
    digits = new int[digits.length + 1];//如果上述没有结束掉 说明是[9][9,9]情况 扩大数组加一
    digits[0] = 1;
    return digits;
}
```





## 链表

### 1、交换一个单链表上奇偶位置的数字

思路：同上数组习题第一个，交换奇偶的位置，这里同时也保证了奇偶相对位置不改变。

```java
public ListNode reOrederListNode(ListNode head){
	if(head == null) return null;
    ListNode pre = head;
    ListNode last = null;
    while(pre != null){
        while(pre != null && pre.val%2 != 0) pre = pre.next; //往后找偶数
        last = pre.next;
        while(last != null && last.val%2 != 1) last = last.next; //往后找奇数
        // 整体移动偶数位置 并把奇数放前面去
        if(last != null){
            int temp = last.val; //奇数值
            ListNode t = pre; // 偶数位置起点
            while(t != last.next){ // 偶数向前移动 直到奇数位置处  next相当于等于号
                t = t.next;
            }
            pre.val = temp; // 奇数放前面位置处
            pre = pre.next;
        }else break;
    }
}
```





### **2、两个单链表相加

**链表反转后的值相加  链表考虑到进位处理，来自letcode**

**描述：**给出两个 **非空** 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 **逆序** 的方式存储的，并且它们的每个节点只能存储 **一位** 数字。

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

示例： **这里是从左往右计算 并进位  并不是实际的加法逻辑**

输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        if(l1 == null && l2 ==null) return null;
        ListNode t = new ListNode(0);
        ListNode p = t;//p是记录链表位置状态的临时节点
        int over = 0; //两个数相加大于10的十位数字1
        while(l1 != null || l2 != null){
            int x = (l1 == null) ? 0 : l1.val;
            int y = (l2 == null) ? 0 : l2.val;
            int sum = x+y+over;
            p.next = new ListNode(sum % 10);
            over = sum / 10;
            
            if(l1 != null) l1 = l1.next; //注意，需要判断不为空 如果是[1]，next.next就报空指针
            if(l2 != null) l2 = l2.next;
            p = p.next;
        } 
        if(over == 1) p.next = new ListNode(1); //注意，如果是[5][6]=[0][1]  需要记录相加进位的1
        return t.next;
    }
}
```



#### 2.1、两个单链表相加2

**正常加法逻辑，不改变链表结构 来自letcode**

**描述：**给你两个 **非空** 链表来代表两个非负整数。数字最高位位于链表开始位置。它们的每个节点只存储一位数字。将这两数相加会返回一个新的链表。

你可以假设除了数字 0 之外，这两个数字都不会以零开头。

进阶：

**如果输入链表不能修改该如何处理？换句话说，你不能对列表中的节点进行翻转**。

示例： **正常的加法逻辑 不改变链表结构 使用栈解决  最后使用头插法解决**

**输入：(7 -> 2 -> 4 -> 3) + (5 -> 6 -> 4)**
**输出：7 -> 8 -> 0 -> 7**

即7243 + 564 = 7807

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        if(l1 == null && l2 == null) return null;
        Stack<Integer> s1 = new Stack<>(); //使用栈处理不改变原来链表结构的方法
        Stack<Integer> s2 = new Stack<>();
        while(l1 != null){
            s1.push(l1.val);
            l1 = l1.next;
        }
        while(l2 != null){
            s2.push(l2.val);
            l2 = l2.next;
        }
        ListNode res = null; //以为个位数上 next后面没有数值了 就是null
        int over = 0;
        while(!s1.isEmpty() || !s2.isEmpty() || over>0){
            int x = s1.isEmpty() ? 0 : s1.pop();  //peek()是只读不删除
            int y = s2.isEmpty() ? 0 : s2.pop();
            int sum = x + y +over;
            ListNode node = new ListNode(sum % 10);
            // *******  链表的头插法*******
            node.next = res; // 最后一个节点需要指向null
            res = node; //res跑到当前节点上去
            over = sum /10; //计算进位
        }
        return res;
    }
}
```



### *3、二叉搜索树与双向链表    #JZ26

**二叉搜索树也叫 二叉排序树 或 二叉查找树**

什么是二叉查找树：
根节点的值大于其左子树中任意一个节点的值，小于其右节点中任意一节点的值，这一规则适用于二叉查找树中的每一个节点。

<img src="C:\Users\guosiyu8\AppData\Roaming\Typora\typora-user-images\image-20200707210455696.png" alt="image-20200707210455696" style="zoom:50%;" />

**描述：输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表**。要求不能创建任何新的结点，只能调整树中结点指针的指向。

**思路：**

```
方法一：非递归版--利用栈存储节点
解题思路：
1.核心是中序遍历的非递归算法。
2.同时修改当前遍历节点与前一遍历节点的指针指向。
```

<img src="C:\Users\guosiyu8\AppData\Roaming\Typora\typora-user-images\image-20200707210236167.png" alt="image-20200707210236167" style="zoom: 67%;" />

```JAVA
// 定义树class
public class TreeNode{
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;
    public TreeNode(int val){this.val = val;}
}
import java.util.*;
public class solution{
    public TreeNode Convert(TreeNode root){
        if(root == null) return null;
        Stack<TreeNode> stack = new Stack<TreeNode>();//保存树节点
        TreeNode p = root; // 单独的助理节点 指向根节点
        TreeNode pre = null; // 用于保存中序遍历序列的上一个节点  因为要求双向链表
        boolean isFirst = true;
        while(p != null || !stack.isEmpty()){
            while(p != null){
                stack.push(p); // 放入当前根节点
                p = p.left; //一直往左遍历节点
            }
            p = stack.pop(); //每次都是从stack取出节点的
            if(isFirst){ // 第一次先 将中序遍历的第一个节点标记为root
                root = p; // root指向p，p是助理节点 也是引用类型
                pre = root; //pre等于p 指向当前节点  因为最后p都会改变
                isFirst = false;
            }else{
                pre.right = p; //双端链表 指向当前根节点
                p.left = pre; //指向上个节点
                pre = p; // 上个节点变成当前根节点
            }
            p = p.right; // 之前p存储的是左节点，现在存储右节点
        }
        return root; // 返回root  因为root=p
    }
}
```



### 4、删除链表中重复的结点   #JZ56

**描述**：在一个排序的链表中，存在重复的结点，请删除该链表中重复的结点，**重复的结点不保留**，**返回链表头指针**。 例如，**链表1->2->3->3->4->4->5 处理后为 1->2->5**

**思路**： 利用**双指针**，pre和last表示当前结点和下一个结点；last结点用于判断是否存在重复结点，如果存在则pre直接 pre.next = 最后一个重复元素的下一个结点，然后last继续向后判断是否还存在重复结点

```JAVA
public class ListNode {
    int val;
    ListNode next;
    ListNode(int val) {
        this.val = val;
}
   
public ListNode deleteDuplication(ListNode pHead){
    if(pHead == null || pHead.next == null) return pHead;
    ListNode head = new ListNode(0); //新节点
    head.next = pHead; // 指向原链表
    ListNode pre = head; //pre指向当前结点
    ListNode last = head.next; //last指向下一个结点
    while(last != null){
        if(last.next != null && last.val == last.next.val){ //遇到重复结点
            // 一直往后遍历 找到最后一个相同的节点
            while(last.next!=null && last.val == last.next.val){
                last = last.next;
            }
            //** 因为pre=head 所以pre的操作就是修改了原head链表
            pre.next = last.next; //当前指针指向重复元素的下一个 这里也是修改了head链表
            last = last.next;
        }else{ // 没有重复结点 当前节点和下一节点向后走
            pre = pre.next;
            last = last.next;
        }
    }
    return head.next; //因为head第一个节点是0 
}
```

#### 4.1、删除排序链表中的重复元素 leet83

描述：给定一个排序链表，删除所有重复的元素，**使得每个元素只出现一次(保留一份重复的元素)**。

**注意：此题和第四题区别在于 此题要保留一份重复的元素，而第四题重复的元素都删掉**

> 示例 1:
>
> 输入: 1->1->2
> 输出: 1->2
>
> 示例 2:
>
> 输入: 1->1->2->3->3
> 输出: 1->2->3

思路：使用当前节点 和下一个节点比较，相等的话则cur.next=cur.next.next;即跳过重复的元素

时间复杂度：O(N)

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if(head==null || head.next==null) return head;
        ListNode cur = head;
        while(cur!=null && cur.next!=null){
            if(cur.val == cur.next.val){
                cur.next = cur.next.next; //跳过当前重复的节点
            }else{
                cur = cur.next; //这里保证了cur会保留一份重复的节点
            }
        }
        return head;
    }
}
```

#### 4.2 删除链表中某值的一个节点 #jz18

描述：给定**单向链表**的头指针和**一个要删除的节点的值**，定义一个函数删除该节点。

返回删除后的链表的头节点。

**注意：此题只需要删除一个匹配的节点！** 12.2是删除所有匹配的节点

> 示例 1:
>
> 输入: head = [4,5,1,9], val = 5
> 输出: [4,1,9]
> 解释: 给定你链表中值为 5 的第二个节点，那么在调用了你的函数之后，该链表应变为 4 -> 1 -> 9.

思路：和12题思路一直，找到待删除结点的前一个结点然后两次next删除该节点。同样借助哑节点 来解决头节点为null的情况

```java
class Solution {
    public ListNode deleteNode(ListNode head, int val) {
        if(head==null) return null;
        ListNode res = new ListNode(0);//哑结点
        res.next=head;
        ListNode slow = res;//慢指针找到待删除结点的前一个结点
        ListNode fast = head;//快指针找到待删除结点 去比较值是否为val
        while(fast!=null){
            if(fast.val == val){
                slow.next = slow.next.next;
                break;
            }else{
                fast = fast.next;
                slow = slow.next;
            }
        }
        return res.next;
    }
}
class ListNode{ //非静态class 可以被同一文件中其他类调用到！！！！！！！！！！！
    int val;
    ListNode next;
    ListNode(x){this.val = x;}
}
```

**补充：也是删除链表中某个值的节点，但是吧，函数就给你一个待删除的节点 让你去删除它**

```java
class Solution {
    public void deleteNode(ListNode node) {//node就是待删除的节点
        node.val = node.next.val; // 当前值等于下一个
        node.next = node.next.next;//删除下一个值
    }
}
```



#### 4.3 删除链表中某值的所有节点 #leet203

删除链表中等于给定值 val 的**所有节点**。

**注意：此题和12.1不同之处，这里是删除所有匹配的节点！**

> 示例:
>
> 输入: 1->2->6->3->4->5->6, val = 6
> 输出: 1->2->3->4->5

**思路**：类似第四题 删除链表中重复的节点(不保留重复节点)！ 

利用**双指针**，pre和last表示当前结点和下一个结点；last结点用于判断是否存匹配节点 存在则向后走，然后pre直接 pre.next = last

```java
class Solution {
    public ListNode removeElements(ListNode phead, int val) {
        if(phead==null ) return null;
        ListNode head = new ListNode(0); //借助哑节点
        head.next = phead;
        ListNode pre = head;//待比较删除节点的前一个节点
        ListNode last = head.next;//待比较节点
        while(last != null){
            if(last.val == val){
                // **注意 这里只需要保证当前last不为null **
                while(last != null && last.val == val){
                    last = last.next;
                }
                pre.next = last;//此时last.val！=val
            }else{
                pre = pre.next;
                last = last.next;
            }
        }
        return head.next;
    }
}
```

#### 4.4 删除无序链表中重复节点 #leet面试0201

描述：编写代码，移除**未排序链表**中的**重复节点**。**保留最开始出现的节点**。

 **示例1:**

```xml
 输入：[1, 2, 3, 3, 2, 1]
 输出：[1, 2, 3]
```

思路：使用**HashSet去重** 若已存在则跳过重复结点。

时间复杂度和空间复杂度：O(N)

```java
class Solution {
    public ListNode removeDuplicateNodes(ListNode head) {
        if(head == null) return null;
        Set<Integer> set = new HashSet<>();
        ListNode res = new ListNode(0);
        ListNode p =res;
        while(head != null){
            if(set.add(head.val)){// 1 之前不存在的则正常拼接
                p.next = head;
                p = p.next;
            }else{
                p.next = head.next; //2 之前存在 则删除重复节点
            }
            head = head.next;
        }
        return res.next;
    }
}
```





### **5、链表中环的入口结点     #JZ55

**描述**：给一个链表，若其中包含环，请找出该链表的环的入口结点，否则，输出null。

**思路**：分为两种思路：

**1、简单**：使用HashSet集合判断，利用Set集合不能存放相同元素的特征，如果存在相同结点说明找到了环的入口

**时间复杂度：O(n)    空间复杂度：O(n)** 

```java
class ListNode {
    int val;
    ListNode next = null;
    ListNode(int val) {this.val = val;}
}

import java.util.*;
public class Solution {
    // 利用hashSet判断  Set不能存放重复元素  感谢b站大神up
    // 也可返回boolean类型的
    public ListNode EntryNodeOfLoop(ListNode pHead)
    {
        if(pHead == null) return null;//false
        Set<ListNode> hs = new HashSet<>();//***set中可以直接放结点去判断是否相同！
        while(pHead != null){
            if(!hs.add(pHead)){  // 当存在重复元素了就添加失败 再取反就是此重复节点，就是环入口
                return pHead;//true
            }
            pHead = pHead.next;
        }
        return null;//false
    }
}
```

2、利用数学思维，利用公式判读，如下：

  假设x为环前面的路程（黑色路程），a为环入口到相遇点的路程（蓝色路程，假设顺时针走）， c为环的长度（蓝色+橙色路程） 

  当快慢指针相遇的时候： 此时慢指针走的路程为Sslow =  x + m * c + a。 快指针走的路程为Sfast = x + n * c + a
 2 Sslow =  Sfast    2 * ( x + m*c + a ) = (x + n *c + a)
 从而可以推导出：
 x = (n  - 2 * m )*c - a =  (n - 2 *m -1 )*c + c - a
 即环前面的路程 =  数个环的长度（为可能为0） + c - a
 什么是c - a？这是相遇点后，环后面部分的路程。（橙色路程）
所以，**我们可以让一个指针从起点A开始走，让一个指针从相遇点B开始继续往后走，2个指针速度一样，那么，当从原点的指针走到环入口点的时候（此时刚好走了x）从相遇点开始走的那个指针也一定刚好到达环入口点。 所以2者会相遇，且恰好相遇在环的入口点。** 

最后，判断是否有环，且找环的算法复杂度为： 

**时间复杂度：O(n)    空间复杂度：O(1)** 

<img src="/Snipaste_2020-07-08_14-46-47.png" style="zoom:40%;" />



```JAVA
// 1 返回具体的环入口结点
public ListNode EntryNodeOfLoop(ListNode pHead){
    if(pHead == null || pHead.next == null){return null;}
    ListNode fast = pHead;
    ListNode slow = pHead;
    //1 寻找相遇点位置
    while(fast!=null && fast.next!=null){
        fast = fast.next.next;
        slow = slow.next;
        if(fast == slow) break;
    }
    // 2 寻找入口点 即慢指针走a路程等于快指针走c路程
    slow = pHead;
    //fast==slow说明有环 或者 fast不等于null说明没有环
    while(fast != slow && fast!=null){
        fast = fast.next;
        slow = slow.next;
    }
    if(fast==slow) return slow;
    else return null;
}

//2 判断是否有环 Boolean
public boolean hasCycle(ListNode head) {
    // 方法1 使用快慢指针 数学方法解决
    if(head==null || head.next==null) return false;
    //1 使用快慢指针寻找相遇点
    ListNode fast = head;
    ListNode slow = head;
    while(fast!=null && fast.next!=null){
        slow = slow.next;
        fast = fast.next.next;
        if(slow == fast) break;
    }
    //2 寻找环的入口
    slow = head; //slow慢指针为head
    while(slow != fast && fast!=null){
        slow = slow.next;
        fast = fast.next;
    } //如果题目一定有环 那么slow/fast就是环的入口
    //3 如果相同这说明有环 否则没有
    if(slow == fast) return true;
    else return false;
}
```



### 6 孩子们的游戏(圆圈中最后剩下的数）       #JZ46

**题目描述：**

   每年六一儿童节,牛客都会准备一些小礼物去看望孤儿院的小朋友,今年亦是如此。HF作为牛客的资深元老,自然也准备了一些小游戏。其中,有个游戏是这样的:首先,让小朋友们围成一个大圈。然后,他随机指定一个数m,让编号为0的小朋友开始报数。每次喊到m-1的那个小朋友要出列唱首歌,然后可以在礼品箱中任意的挑选礼物,并且不再回到圈中,从他的下一个小朋友开始,继续0...m-1报数....这样下去....直到剩下最后一个小朋友,可以不用表演,并且拿到牛客名贵的“名侦探柯南”典藏版(名额有限哦!!^_^)。请你试着想下,哪个小朋友会得到这份礼品呢？(注：小朋友的编号是从0到n-1) 

 如果没有小朋友，请返回-1 

**思路：**java中直接使用一个**list集合**来模拟，并使用一个索引cur类指向删除的位置，当cur的值为list的size，就让cur指到头位置。

```java
import java.util.*;
public class Solution {
    public int LastRemaining_Solution(int n, int m) {
        if(n<=0 || m<=0) return -1;
        //构建list环
        List<Integer> list = new ArrayList<>();
        for(int i=0; i<n; i++){
            list.add(i); //所有人都放入集合中
        }
        int cur = -1; //模拟指针
        while(list.size() > 1){
            // 向后移动m个位置
            for(int i =0; i<m; i++){
                cur++; //指针后移
                if(cur == list.size()) cur=0;//回到环的头
            }
            list.remove(cur);//移除被选中的m位置
            cur--; //因为新的list中cur指向了下一个元素，为了保证移动m个准确性，所以cur向前移动一位
        }
        return list.get(0); //最后只剩下一个元素
    }
}
```



### *7、相交链表&两个链表的第一个公共节点 

  #JZ36和leetcode160同一个题目

**描述：**输入两个链表，找出它们的第一个公共结点。（注意因为传入数据是链表，所以错误测试数据的提示是用其他方式显示的，保证传入数据是正确的）

> 输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,0,1,8,4,5], skipA = 2, skipB = 3
> 输出：Reference of the node with value = 8
> 输入解释：相交节点的值为 8 （注意，如果两个链表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,0,1,8,4,5]。在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。

**思路：** **双指针**。

创建两个指针p1和p2,分别指向两个链表的**头结点**，然后依次往后遍历。如果某个指针到达末尾，则将该指针指向另一个链表的头结点；如果两个指针所指的节点相同，则循环结束，返回当前指针指向的节点。比如两个链表分别为：1->3->4->5->6和2->7->8->9->5->6。短链表的指针p1会先到达尾部，然后重新指向长链表头部，当长链表的指针p2到达尾部时，重新指向短链表头部，此时p1在长链表中已经多走了k步(k为两个链表的长度差值)，p1和p2位于同一起跑线，**往后遍历找到相同节点即可**。其实该方法主要就是**用链表循环的方式替代了长链表指针先走k步这一步骤**。

p1: 1 3 4 5 6->2   7 8 9 **5**

p2: 2 7 8 9 5   6->1 3 4 **5** 6   当走到节点5时 就是相同的节点了

```java
//优化版本 时间复杂度低
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if(headA==null || headB==null) return null;
        ListNode p1 = headA, p2=headB;
        while(p1 != p2){
            p1 = p1==null? headB : p1.next;
            p2 = p2==null? headA : p2.next;
        }
        return p1;
    }
}

//初始版本 时间慢
public class Solution {
    public ListNode FindFirstCommonNode(ListNode pHead1, ListNode pHead2) {
        if(pHead1 == null || pHead2 == null) return null;
        ListNode p1 = pHead1;
        ListNode p2 = pHead2;
        while(p1 != p2){
            p1 = p1.next;
            p2 = p2.next;
            if(p1 != p2){
                if(p1 == null) p1 = pHead2; //p1走到结尾了就指向链表2
                if(p2 == null) p2 = pHead1;
            }
        }
        return p1;
    }
}
```

### 8、复杂链表的复制   #JZ25

**描述**：输入一个复杂链表（**每个节点中有节点值，以及两个指针，一个指向下一个节点，另一个特殊指针random指向一个随机节点**），请对此链表进行深拷贝，并返回拷贝后的头结点。（注意，输出结果中请不要返回参数中的节点引用，否则判题程序会直接返回空）

```
*解题思路：
*``1``、遍历链表，复制每个结点，如复制结点A得到A1，将结点A1插到结点A后面；
*``2``、重新遍历链表，复制老结点的随机指针给新结点，如A1.random = A.random.next;
*``3``、拆分链表，将链表拆分为原链表和复制后的链表
```

```java
public class Solution {
    public RandomListNode Clone(RandomListNode pHead) {
        if(pHead == null) {return null;}
        RandomListNode currentNode = pHead;
        //1、复制每个结点，如复制结点A得到A1，将结点A1插到结点A后面；
        while(currentNode != null){
            RandomListNode cloneNode = new RandomListNode(currentNode.label);
            RandomListNode nextNode = currentNode.next;
            currentNode.next = cloneNode;
            cloneNode.next = nextNode;
            currentNode = nextNode;
        }
        currentNode = pHead;
        //2、重新遍历链表，复制老结点的随机指针给新结点，如A1.random = A.random.next;
        while(currentNode != null) {
            currentNode.next.random = currentNode.random==null?null:currentNode.random.next;
            currentNode = currentNode.next.next;
        }

        //3、拆分链表，将链表拆分为原链表和复制后的链表
        currentNode = pHead;
        RandomListNode pCloneHead = pHead.next;
        while(currentNode != null) {
            RandomListNode cloneNode = currentNode.next;
            currentNode.next = cloneNode.next;
            cloneNode.next = cloneNode.next==null?null:cloneNode.next.next;
            currentNode = currentNode.next;
        }
        return pCloneHead;
    }
}
```



### 9、合并两个排序的链表   #JZ16

**描述**：输入两个单调递增的链表，输出两个链表合成后的链表，当然我们需要合成后的链表满足单调不减规则。

**问题点**： 此题属于简单类型， **出错了两个点** 

1. **声明了一个结点t用于存储新的链表，但是注意不能使用t去拼接，因为拼接后t就指向链表尾了，所以使用一个助理结点p，p = t，让p去拼接链表，最后返回t还是新链表的头部**。
2. **每次比较完大小后  让当前链表的结点往后移动，而另外一个链表是不需要移动！**

```java
/*
public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}*/
public class Solution {
    public ListNode Merge(ListNode list1,ListNode list2) {
        //1 判断是否存在null
        if(list1 == null && list2 == null) return null;
         // 如果其中一个为null 直接返回那个不为mull的链表
        if(list1 == null || list2 == null){
            return list1==null ? list2:list1;
        }
        //2 遍历  比较大小后 添加新链表
        ListNode t = new ListNode(-1);
        ListNode p = t;//p是t链表的临时节点 去遍历！！！！！！！！！！！！！
        while(list1 != null && list2 !=null){
            if(list1.val<=list2.val){
                p.next = list1;
                list1 = list1.next; //！！！当前链表的节点移动就好 另外一个链表此时不需要移动
            }else{
                p.next = list2;
                list2 = list2.next;
            }
            p = p.next; //p节点向后移动 继续追加后续节点
        }//while end
        // 3 如果其中一个为null了  直接追加不为null的那个链表
        if(list1 == null){
            p.next = list2;
        }
        if(list2 == null){
            p.next = list1;
        }
        return t.next;  // 因为p=t，修改p相当于是修改的对t的引用，所以t也发生了改变
    }
}
```

#### **9.1 合并k个排序链表

描述：**合并 k 个排序链表，返回合并后的排序链表**。请分析和描述算法的复杂度。

示例:

> 输入:
> [1->4->5,
>   1->3->4,
>   2->6]
> 输出: 1->1->2->3->4->4->5->6

思路：1. **优先级队列（堆） + 贪心算法**          2. **分治合并**

<u>**思路1.** **优先级队列（堆） + 贪心算法   （推荐）**</u>

1、由于是 k 个排序链表，那么**这 k 个排序的链表头结点中 val 最小的结点就是合并以后的链表中最小的结点**；

2、最小结点所在的链表的头结点就要**更新**了，**更新成最小结点的下一个结点**（如果有的话），此时还是这 k 个链表，这 k 个排序的链表头结点中 val 最小的结点就是合并以后的链表中第 2 小的结点。

写到这里，我想你应该差不多明白了，**我们每一次都从这 k 个排序的链表头结点中拿出 val 最小的结点“穿针引线”成新的链表（使用优先级堆实现），**这个链表就是题目要求的“合并后的排序链表”。**“局部最优，全局就最优”，这不就是贪心算法**的思想吗。

**复杂度分析：**

**时间**复杂度：**O(N logk)**，这里 N 是这 k 个链表的结点总数，每一次从一个优先队列中选出一个最小结点的时间复杂度是 O(logk)，故时间复杂度为O(Nlogk)。
**空间**复杂度：**O(k)**，使用优先队列需要 k 个空间，“穿针引线”需要常数个空间，因此空间复杂度为O(k)。

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        int len = lists.length;
        if(len ==0 || lists==null) return null;
        //1 创建小顶堆 每次取出最小的数  使用lamdba创建(根据val值排序的)小顶堆//时间快一些
        PriorityQueue<ListNode> queue = new PriorityQueue<>(len, (a,b)->a.val-b.val); 
        // PriorityQueue<ListNode> queue = new PriorityQueue<>(len, new Comparator<ListNode>(){
        //     public int compare(ListNode a, ListNode b){
        //         return a.val - b.val;
        //     }
        // });
        for(ListNode node : lists){
            // 这一步很关键，不需要将空对象添加到优先队列中
            if(node!=null) queue.offer(node);
        }
        //2 创建新链表 从堆中取最小的节点 进行合并
        ListNode resNode = new ListNode(-1); //结果节点
        ListNode p = resNode; //助理节点
        while(!queue.isEmpty()){
            p.next = queue.poll(); //拼接结果链表
            p = p.next;
            // 只有非空节点才能加入到优先队列中，此时p指向queue.poll()的当前链表的节点引用
            if(p.next != null) queue.offer(p.next);
        }
        return resNode.next;
    }
}
```

思路2. 分治合并  递归合并（但是会消耗大连的线程 实际生产中容易栈溢出）

**时间复杂度：O(N logk)**       空间消耗在递归方法，待确定

```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        // 分治思想  把多个list两两合并 最终得到一个list
        if(lists == null || lists.length==0) return null;
        return merge(lists, 0, lists.length-1);
    }
    private ListNode merge(ListNode[] lists, int left, int right){
        if(left == right) return lists[left];
        int mid = left + (right-left)/2;
        ListNode l1 = merge(lists, left, mid); //左边一直二分到最左
        ListNode l2 = merge(lists,mid+1, right); // 右边找到第二个
        //比较待合并的两个list，成为一个有序的list 然后在递归回去继续合并
        return megerTwoList(l1,l2); 
    }
    private ListNode megerTwoList(ListNode l1, ListNode l2){
        if(l1 == null) return l2;
        if(l2 == null) return l1;
        if(l1.val<l2.val){           ///  这部分比较加值可以用迭代代替
            l1.next = megerTwoList(l1.next,l2);
            return l1;
        }else{
            l2.next = megerTwoList(l1,l2.next);
            return l2;
        }
    }
}
```







### *10、反转链表 #JZ15 

**描述**：输入一个链表，反转链表后，输出新链表的表头。

**思路**：使用一个链表操作即可 注意使用**前后指针**

```java
public class Solution {
    public ListNode ReverseList(ListNode head) {
        if(head == null || head.next==null) return head;
        ListNode pre = null;//前一个节点
        ListNode last = null;//后一个节点
        while(head != null){ //!!!!!!! head是一直向后移动
            last = head.next;
            head.next = pre;
            pre = head; // 前一个结点等于当前节点
            head = last; // 当前节点等于下一个节点
        }
         return pre;//pre是反转后的头节点 而head此时指向last=null
    }
}
// 方法2  使用递归
ListNode reverse(ListNode head) {
    if (head.next == null) return head;
    ListNode last = reverse(head.next);
    head.next.next = head;
    head.next = null;
    return last;
}

作者：labuladong
链接：https://leetcode-cn.com/problems/reverse-linked-list-ii/solution/bu-bu-chai-jie-ru-he-di-gui-di-fan-zhuan-lian-biao/
```

#### 10.1、反转链表2  leet92

**反转从位置 m 到 n 的链表。请使用一趟扫描完成反转。**

说明: 1 ≤ m ≤ n ≤ 链表长度。

示例:

输入: 1->2->3->4->5->NULL, m = 2, n = 4
输出: 1->4->3->2->5->NULL

```java
/**
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
//********************** 迭代方法********
class Solution {
    public ListNode reverseBetween(ListNode head, int m, int n) {
        ListNode dmy = new ListNode(0);
        dmy.next = head;
        int delta = n-m;
        ListNode pre = dmy, tail = head;
        //先定位初m节点和m之前的节点
        while(m>1){
            pre = tail;
            tail = tail.next;
            m--;
        }
        while(delta>0){
            ListNode next = tail.next;
            tail.next = next.next; //tail一直不变，只要修改指针到NEXT.NEXT
            next.next = pre.next;//next.next指向pre的next 最新的第m个位置
            pre.next = next; //更新next为最新的第m个位置
            delta--;
        }
        return dmy.next;
    }
}

//*********递归方法*************
ListNode reverseBetween(ListNode head, int m, int n) {
    // base case
    if (m == 1) {
        return reverseN(head, n);
    }
    // 前进到反转的起点触发 base case
    head.next = reverseBetween(head.next, m - 1, n - 1);
    return head;
}
链接：https://leetcode-cn.com/problems/reverse-linked-list-ii/solution/bu-bu-chai-jie-ru-he-di-gui-di-fan-zhuan-lian-biao/
```

```java
//****补充****   ****反转前k个链表节点******
ListNode successor = null; // 后驱节点
// 反转以 head 为起点的 n 个节点，返回新的头结点
ListNode reverseN(ListNode head, int n) {
    if (n == 1) { 
        // 记录第 n + 1 个节点
        successor = head.next;
        return head;
    }
    // 以 head.next 为起点，需要反转前 n - 1 个节点
    ListNode last = reverseN(head.next, n - 1);

    head.next.next = head;
    // 让反转之后的 head 节点和后面的节点连起来
    head.next = successor;
    return last;
} 
链接：https://leetcode-cn.com/problems/reverse-linked-list-ii/solution/bu-bu-chai-jie-ru-he-di-gui-di-fan-zhuan-lian-biao/
```





#### *10.2、奇偶位置链表+反转+合并   #字节

**题目描述：**一个链表，**奇数位升序偶数位降序**，**如何让链表变成升序的**。 注意是奇数位置

比如：1 8 3 6 5 4 7 2 9，最后输出1 2 3 4 5 6 7 8 9。

**思路：**可以看做是**反转链表**和**合并有序链表**的**合体版**

1.从这个链表中分离处奇数位和偶数位，组成两个新的链表。

2.然后对偶数链表进行反转。 第10题

3.最后将两个有序链表进行合并。第9题

```java
//1 分离出两个链表 奇数位是升序  偶数位是降序
public static ListNode[] splitNode(ListNode head){
    ListNode head1 = null;//奇数位的链表头结点
    ListNode cur1 = null; //奇数位的助理节点
    ListNode head2 = null;
    ListNode cur2 = null;
    int num = 1; //计数器 链表节点从1开始
    // 原链表不等于null  一直往后遍历
    while(head != null){
        if(num % 2 ==1){ //除以2取余是1 说明是奇数
            if(cur1 != null){
                cur1.next = head; //确立了头结点 剩下每次就是找head
                cur1 = cur1.next; // 往后移动一位
            }else{  
                cur1 = head; // 第一次先确定奇偶的头结点 head1  cur1助理节点
                head1 = cur1;
            }
        }else{//除以2取余是0 说明是偶数
            if(cur2 != null){
                cur2.next = head;
                cur2 = cur2.next;
            }else{
                cur2 = head;
                head2 = cur2;
            }
        }//外层if
        head = head.next;
        num++;
    }// while end
    
    cur1.next = null; //最后两个末尾元素的下一个都指向null
    cur2.next = null;
    ListNode[] heads = new ListNode[]{head1, head2}; //这里head1是新链表的头节点
    return heads;
}

//2 对偶位置链表进行反转为升序链表
public static ListNode reverList(ListNode head){
    if(head == null) return null;
    ListNode pre = null;
    ListNode last = null;
    ListNode cur = head;  // **** 这里也可以用助理节点指向head，也可以直接用head如第10题**
    while(cur != null){
        last = cur.next;
        cur.next = pre;
        pre = cur;
        cur = last;
    }
    return pre;
}

//3 合理两个有序链表 成为新的有序链表
public static ListNode merge(ListNode list1,ListNode list2){
    if(list1 == null && list2 == null) return null;
    if(list1 == null || list2 == null){ // 如果其中一个为null 直接返回那个不为mull的链表
        return list1==null ? list2:list1;
    }
    //*******  这里不能直接使用t去next，否则最后t指向的并不是新链表的头节点！ ******
    ListNode t = new ListNode(-1); //用于存储合并后链表
    ListNode p = t; // 必须使用助理节点！！ p指向t的引用 去next，最后t还是指向新链表的头位置**
    while(list1 != null && list2 !=null){
        if(list1.val > list2.val){
            p.next = list2;
            list2 = list2.next;
        }else{
            p.next = list1;
            list1 = list1.next;
        }
        p = p.next;
    }
    if(list1 == null) p.next = list2;
    if(list2 == null) p.next = list1;
    return t.next;
}
```



### 11、链表中倒数第k个数   #JZ14

**描述**：输入一个链表，输出该链表中倒数第k个结点。。 1-2-3-4-5 倒数第2个  就是4

**思路**：使用**快慢指针**，  双指针

**出现问题：忘记判断k是否大于链表长度的有效性检验！**

```java
public ListNode FindKthToTail(ListNode head,int k) {
    if(head == null || k<=0) return null;
    //使用双指针
    ListNode pre = head;
    ListNode last = head;
    for(int i =0; i<k; i++){
        if(pre == null) return null; // *此句判断k是否大于链表长度的！如果等于null 则k大于链表长度
        pre = pre.next;
    }
    while(pre != null){ // pre 走到尾端时 last刚好走到倒数第k个位置
        last = last.next;
        pre = pre.next;
    }
    return last;
}
```



### *12、删除链表倒数第k个节点   #leet19

给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。

**示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

```java
//思路：利用一个哑节点，快指针先走n步，然后快慢指针同时走，最后慢指针走到倒数第k个节点前一个位置，这时候慢指针的next等于慢指针的next.next就删除了倒数第k个节点
    
public ListNode removeNthFromEnd(ListNode head, int n) {
    if(head == null || n<1) return null;
    ListNode res = new ListNode(0);//1 哑结点,避免删除头节点时 找不到下个节点
    res.next = head;
    ListNode fast = res;//1.1 快慢指针都指向哑节点
    ListNode slow = res;
	// fast先走n步，过滤不合法的n
    while(n>0){
        if(fast.next!=null){
            fast=fast.next;
            n--;
        }else{//过滤不合法的n
            return null;
        }
    }
	// slow是倒数第k个节点的前一个节点
    while(fast.next != null){
        slow = slow.next;
        fast = fast.next;
    }
    slow.next = slow.next.next;//删除节点
    return res.next;
}
```





### 13、从尾到头打印链表   #JZ3

**描述**：输入一个链表，按链表从尾到头的顺序返回一个ArrayList。

```xml
输入：head = [1,3,2]
输出：[2,3,1]
```

**思路**：1使用**前后指针 类似于链表反转**，  2 使用栈的思想，先入后出

出现问题：**此题不需要第一个有效性判断 否则编译 不通过。这里把它放在下面一行 链表为空则return空的list**

```java
//方法1
import java.util.ArrayList;
public class Solution {
    public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
        //if(listNode == null) return null;  //放这里测试不通过
        ArrayList<Integer> list = new ArrayList<Integer>();
        if(listNode == null) return list; //放这里ok
        ListNode pre = null;
        ListNode last = null;
        while(listNode != null){
            last = listNode.next;
            listNode.next = pre;
            pre = listNode;
            listNode = last;
            list.add(0,pre.val); // 亮点！！ 每次元素放0位置 那么之前元素就往后移动 形成倒序！！！
        }
        //while(pre!=null){ //   效果同list.add(0,pre.val);
        //    list.add(pre.val);
        //    pre = pre.next;
        //}
        return list;
    }
}

//方法2
class Solution {
    public int[] reversePrint(ListNode head) {
        Stack<Integer> stack = new Stack<>();
        // Deque<Integer> stack = new LinkedList<>();//效率偏高
        while(head!=null){
            stack.push(head.val);
            // stack.addLast(head.val);
            head = head.next;
        }
        int[] res = new int[stack.size()];
        //***注意，这里判断长度不要使用stack.size(),因为stack本身一直在改变啊！！！
        for(int i=0; i<res.length; i++){ 
            res[i] = stack.pop();
            // res[i] = stack.removeLast();
        }
        return res;
    }
}
```



### *14、链表的中间结点-快手

要求两步：  注意点：这里中间结点指的是从1开始的；定义静态内部类并使用；判断链表有否环

**1找到一个链表的中间节点**，思路：使用快慢指针，快指针每次走两步 慢指针走一步

**2判断链表极端情况(监测环的情况 有的话抛出异常)**

**3###对于有偶数个节点(两个中间链表)的，输出的是中间链表的第一个##不同于leetcode 的题**

例如：链表中点

> ​	// case1： 
>
> ​	// 1->2->3->null
>
> ​	// 输出: val=2
>
> ​	// case 2:
>
> ​	// 1->2->3->4->null
>
> ​	// 输出: val=2 （输出的是第二个中间链表！！！输出顺序的第二个 不是下标）
>

```java
//***补充点***在同一个java文件中写两个class的方法常用以下两种：
//1.被调用的ListNode类是非public 的，而调用它的类(可以是main方法)可以是public
class ListNode{ //*******这里必须是非public类型，否则需要单独一个java文件存放此类
    int val;
    ListNode next;
    ListNode(int x){this.val = x;}
}
public class Main {
	 public static void main(String[] args) {
        ListNode node = new ListNode(1); //可以创建ListNode类对象
        node.next=new ListNode(2);
     }
}

// 本题解决过程：
//2.ListNode类是public static，相当于静态内部类调用方式
import java.util.*;
public class Main {
    public static class ListNode{
        int val;
        ListNode next;
        ListNode(int x){
            this.val = x;
        }
    }
    public static void main(String[] args) {
        // 在同一个类中可直接new ListNode，但是不在同个类中 需要用new InnerMian.ListNode(1)
        ListNode node = new ListNode(1);
        // ListNode node2 = new InnerMian.ListNode(1);//等价上面，在不同类情况下使用
        node.next=new ListNode(2);
        ListNode p = node.next;//****临时结点 模拟环**
        node.next.next = new ListNode(3);
        node.next.next.next = new ListNode(4);
        node.next.next.next.next = p; //****模拟链表环的情况*********
        ListNode res = getMidNode(node);
        System.out.println(res.val);
    }
    //找出链表的中间节点  使用 快慢指针
    public static ListNode getMidNode(ListNode head){
        if(head==null || head.next==null || head.next.next==null) return head;
        //1 判断此链表是否有环！！抛出异常
        if(isReverNode(head)){
            throw new RuntimeException();
        }
        ListNode mid = head;
        ListNode fast = head;
        //2 快慢指针开始走 注意这里的快指针终止条件！！  1->2->3->4->null  1->2->3->null
        while(fast!=null && fast.next!=null && fast.next.next!=null){
            mid = mid.next;
            fast = fast.next.next;
        }
        ListNode res = new ListNode(mid.val); //只返回这个节点值
        return res;
    }
    
	//判断链表是否有环--利用HashSet方法
    public static boolean isReverNode(ListNode head){
        if(head == null) return false;
        Set<ListNode> hs = new HashSet<>();
        while(head != null){
            if(!hs.add(head)){
                return true;
            }
            head = head.next;
        }
        return false;
    }
}
```

#### 14.1 链表的中间节点 leet876

给定一个带有头结点 head 的非空单链表，返回链表的中间结点。

**如果是偶数个节点(有两个中间结点)，则返回第二个中间结点**。**注意，这里和上面快手要求的不一样(快手返回第一个中间节点)**

思路：同样也是使用**快慢指针**，一个走两步一个走一步，但是**while的终止条件不同**，详见代码

> 示例 1：
>
> 输入：[1,2,3,4,5]
> 输出：此列表中的结点 3 (序列化形式：[3,4,5])
> 返回的结点值为 3 。 (测评系统对该结点序列化表述是 [3,4,5])。
> 注意，我们返回了一个 ListNode 类型的对象 ans，这样：
> ans.val = 3, ans.next.val = 4, ans.next.next.val = 5, 以及 ans.next.next.next = NULL.
>
> 示例 2：
>
> 输入：[1,2,3,4,5,6]
> 输出：此列表中的结点 4 (序列化形式：[4,5,6])
> 由于该列表有两个中间结点，值分别为 3 和 4，我们返回第二个结点。

```java
class Solution {
    public ListNode middleNode(ListNode head) {
        if(head==null || head.next==null) return head;
        ListNode fast = head;
        ListNode slow = head;
        //**** 注意，这里的终止条件只有当前节点和下一个节点不为空****  1-2-3-4-null
        while(fast!=null && fast.next!=null){
            slow =slow.next;
            fast = fast.next.next;
        }
        return slow; //返回的是当前节点和后序节点的值
    }
}
```

#### 14.2、重排链表 leet143

描述：给定一个单链表 L：L0→L1→…→Ln-1→Ln ，
将其重新排列后变为： L0→Ln→L1→Ln-1→L2→Ln-2→…

你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

> 示例 1:
>
> 给定链表 1->2->3->4, 重新排列为 1->4->2->3.
>

思路：**寻找中间节点(14.1找后一位中间点) + 反转链表 + 插入合并**

```java
class Solution {
    public void reorderList(ListNode head) {
        ListNode slow = head;
        ListNode fast = head;
        ListNode half = null;
        // 1 快慢指针找重点，若是奇数则往后一位
        while(true){
            if(fast != null && fast.next != null && fast.next.next != null){
                slow = slow.next;
                fast = fast.next.next;
            }else if(fast != null && fast.next != null){
                half = slow.next;
                slow = slow.next;
                break;
            }else{
                half = slow;
                break;
            }
        }
        //2 反转链表
        ListNode second = reverse(half);
        //3 交叉合并
        ListNode ans = new ListNode();
        ListNode temp = ans;
        while(head != slow){
            temp.next = head;
            head = head.next;
            temp = temp.next;
            if(second == null) break;
            temp.next = second;
            second = second.next;
            temp = temp.next;
        }
        head = ans.next;
    }
    
    public ListNode reverse(ListNode node){
        ListNode pre = null;
        while(node != null){
            ListNode last = node.next;
            node.next = pre;
            pre = node;
            node = last;
        }
        return pre;
    }
    
}
```





### 15、两两交换链表中的节点

给定一个链表，**两两交换其中相邻的节点，并返回交换后的链表**。

你不能只是单纯的改变节点内部的值，而是**需要实际的进行节点交换**

> 示例:
>
> 给定 1->2->3->4, 你应该返回 2->1->4->3.
>

```java
// 迭代方法
class Solution {
    public ListNode swapPairs(ListNode head) {
        ListNode pre = new ListNode(0); //哑结点
        pre.next = head;
        ListNode temp = pre; //助理节点
        //需要判断相邻的两个节点都不为null
        while(temp.next != null && temp.next.next != null){
            ListNode start = temp.next; // 待交换的两个节点 第一个节点
            ListNode end = temp.next.next; //            第二个节点
            temp.next = end;
            start.next = end.next;
            end.next = start;
            temp = start; //temp此时为下一组待交换节点的前一个位置
        }
        return pre.next;
    }
}

// 递归方法
class Solution {
    public ListNode swapPairs(ListNode head) {
        if(head == null || head.next == null){
            return head;
        }
        ListNode next = head.next;
        head.next = swapPairs(next.next);
        next.next = head;
        return next;
    }
}
```

### 16、k个一组翻转链表

给你一个链表，**每 k 个节点一组进行翻转**，请你返回翻转后的链表。

k 是一个正整数，它的值小于或等于链表的长度。

**如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序**。

> 示例：
>
> 给你这个链表：1->2->3->4->5
>
> 当 k = 2 时，应当返回: 2->1->4->3->5
>
> 当 k = 3 时，应当返回: 3->2->1->4->5
>

**时间复杂度为 O(n∗K)** 最好的情况为 O(n)最差的情况未 O(n2)
空间复杂度为 O(1) 除了几个必须的节点指针外，我们并没有占用其他空间

<img src="/k个链表的翻转1_2020-08-31_22-27-38.png" style="zoom:70%;" />

<img src="/k个链表的翻转2_2020-08-31_22-27-38.png" style="zoom:67%;" />

```java
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        if(head == null || head.next == null) return head;
        ListNode dummy = new ListNode(0); //定义一个哑节点
        dummy.next = head;
        //pre end都指向dummy，
        ListNode pre =dummy;//pre指向待反转链表头结点的上一个点
        ListNode end =dummy;//end 指向要反转链表的尾节点
        
        while(end.next!=null){
            //1循环k次，找到待反转链表的结尾
            for(int i=0;i<k && end!=null;i++){
                end = end.next;
            }
            //2如果end==null 需要反转的链表节点数小于k 不执行反转
            if(end == null) break;
            //先记录end.next 方便后面连接链表
            ListNode next = end.next;
            end.next = null; //断开链表
            ListNode start = pre.next;//待反转链表的头结点
            pre.next = reverse(start);//反转链表 pre.next指向反转后的链表
            start.next = next;//反转后节点变到最后
            pre = start;//将pre换成下次要反转的链表的头结点的上一个
            end = start;//反转结束 将end置位下次要翻转的链表头结点的上一个节点
        }
        return dummy.next;
    }
    // 链表反转
    public ListNode reverse(ListNode head){
        if(head==null || head.next==null) return head;
        ListNode pre = null;
        ListNode cur = head;
        ListNode next = null;
        while(cur != null){
            next = cur.next;
            cur.next = pre;
            pre = cur;
            cur = next;
        }
        return pre;
    }  
}
```

### 17、回文链表 

描述：请判断一个链表是否为回文链表。

> 示例 1:
>
> 输入: 1->2
> 输出: false
>
> 示例 2:
>
> 输入: 1->2->2->1
> 输出: true

思路：优选方法2

方法一：**将值复制到数组中**后用**双指针法**

有两种常用的列表实现，一种是数组列表和链表。如果我们想在列表中存储值，那么它们是如何保存的呢？

    数组列表底层是使用数组存储值，我们可以通过索引在 O(1) 的时间访问列表任何位置的值，这是由于内存寻址的方式。
    链表存储的是称为节点的对象，每个节点保存一个值和指向下一个节点的指针。访问某个特定索引的节点需要 O(n) 的时间，因为要通过指针获取到下一个位置的节点。

**确定数组列表是否为回文很简单，我们可以使用双指针法来比较两端的元素，并向中间移动**。一个指针从起点向中间移动，另一个指针从终点向中间移动。这需要 O(n) 的时间，因为访问每个元素的时间是 O(1)，而有 n 个元素要访问。

然后，直接在链表上操作并不简单，因为不论是正向访问还是反向访问都不是 O(1)。而将链表的值复制到数组列表中是 O(n)，因此最简单的方法就是**将链表的值复制到数组列表中，再使用双指针法判断**。

我们可以分为两个步骤：

    复制链表值到数组列表中。
    使用双指针法判断是否为回文。

**复杂度分析**

```XML
时间复杂度：O(n)，其中 n 指的是链表的元素个数。
    第一步： 遍历链表并将值复制到数组中，O(n)。
    第二步：双指针判断是否为回文，执行了 O(n/2)次的判断，即O(n)。
    总的时间复杂度：O(2n)=O(n)O(2n)=O(n)。
空间复杂度：O(n)，其中 n指的是链表的元素个数，我们使用了一个数组列表存放链表的元素值。
```

**方法二：优选!**

借助**快慢指针**思想，**先找到列表的中间节点**，然后**反转中间节点及之后部分**，然后匹配前后两部分。需要注意节点个数是奇数还是偶数个。时间复杂度：O(n)  空间复杂度：O(1)

```java
//方法1 简单好理解，但是空间复杂度高，可以优化为O(1)见方法2
class Solution {
    public boolean isPalindrome(ListNode head) {
        List<Integer> res = new ArrayList<>();
        //1 将链表复制到数组中
        ListNode cur = head;
        while(cur!=null){
            res.add(cur.val);
            cur =cur.next;
        }
        //2 利用双指针判断数组是否为回文，通用思路！！！
        int front = 0;
        int back = res.size()-1;
        while(front < back){
            if(!res.get(front).equals(res.get(back))){
                return false;
            }
            front++;
            back--;
        }
        return true;
    }
}
//****优选  方法2 时间复杂度O(N) 空间是O(1)***
        /**
     * 方法2：借助快慢指针思想，先找到列表的中间节点，然后反转中间节点及之后部分，然后匹配
     * 空间复杂度 O(1)
     * 
     * 快指针每次走两步，慢指针每次走一步
     * 初始  fast = head, slow = head;
     * 如果有偶数个节点，1->2->3->5->null 结束后 fast = null，slow = 3
     * 如果有奇数个节点，1->2->3->4->5->null 结束后 fast = 5 != null，slow = 3
     *      因为奇数情况下最中间节点不需要考虑，所以此时slow需要继续后移一步
     *  然后返回slow往后的列表 分别得到  1->2  null<-3<-5         1->2 3 null<-4<-5
     *
     */
    public boolean isPalindrome(ListNode head) {
       ListNode fast = head, slow = head;
       // 快慢指针找到最中间的节点
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
        }
        // 此时，说明有奇数个节点，slow需要后移一个
        if (fast != null) slow = slow.next;
        // 反转slow及之后的列表节点
        ListNode left = head, right = reverseList(slow);
        // 判断两部分是否对称
        while (right != null) {
            if (left.val != right.val) return false;
            left = left.next;
            right = right.next;
        }
        return true;
    }

    /**
     * 反转以head开始的列表，返回反转后的首节点(反转前的最后一个)
     */
    private ListNode reverseList(ListNode head) {
        if (head == null) return head;
        ListNode pre = null,next=null, cur = head;
        while (cur != null) {
            next = cur.next;
            cur.next = pre;// 指针反向
            pre = cur;// pre后移
            cur = next;//当前节点后移
        }
        return pre;
    }
}
```



### 18、旋转链表

给定一个链表，旋转链表，**将链表每个节点向右移动 k 个位置**，其中 k 是非负数。

> 示例 1:
>
> 输入: 1->2->3->4->5->NULL, k = 2
> 输出: 4->5->1->2->3->NULL
> 解释:
> 向右旋转 1 步: 5->1->2->3->4->NULL
> 向右旋转 2 步: 4->5->1->2->3->NULL

算法实现很直接：

    找到旧的尾部并将其与链表头相连 old_tail.next = head，整个链表闭合成环，同时计算出链表的长度 n。
    找到新的尾部，第 (n - k % n - 1) 个节点 ，新的链表头是第 (n - k % n) 个节点。
    断开环 new_tail.next = None，并返回新的链表头 new_head。

```java
class Solution {
    public ListNode rotateRight(ListNode head, int k) {
        // base cases
        if (head == null) return null;
        if (head.next == null) return head;

        // close the linked list into the ring
        ListNode old_tail = head;
        int n;
        for(n = 1; old_tail.next != null; n++){
          old_tail = old_tail.next;
        }
        old_tail.next = head;

        // find new tail : (n - k % n - 1)th node
        // and new head : (n - k % n)th node
        ListNode new_tail = head;
        for (int i = 0; i < n - k % n - 1; i++){
          new_tail = new_tail.next;
        }
        ListNode new_head = new_tail.next;

        // break the ring 断开闭环
        new_tail.next = null;
        return new_head;
    }
}
```

### 19、排序链表 leet148

描述：在 *O*(*n* log *n*) 时间复杂度和常数级空间复杂度下，对链表进行排序。

**示例 1:**

```
输入: 4->2->1->3
输出: 1->2->3->4
```

思路：**归并排序**

```java
class Solution {
    public ListNode sortList(ListNode head) {
        if (head == null || head.next == null) return head;
        //1 分割链表 成为两两一对 利用快慢指针找到中间节点 分为两部分 一次递归
        ListNode fast = head.next, slow = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        ListNode tmp = slow.next;
        slow.next = null;
        ListNode left = sortList(head); //递归调用拆分
        ListNode right = sortList(tmp);
        // 2合并两两链表
        ListNode h = new ListNode(0);
        ListNode res = h;
        while (left != null && right != null) {//合并两个有序链表
            if (left.val < right.val) {
                h.next = left;
                left = left.next;
            } else {
                h.next = right;
                right = right.next;
            }
            h = h.next;
        }
        h.next = left != null ? left : right;
        return res.next;
    }
}
```

#### 19.1链表的插入排序

描述： 使用**插入排序 使 链表有序**

插入排序算法：

    插入排序是迭代的，每次只移动一个元素，直到所有元素可以形成一个有序的输出列表。
    每次迭代中，插入排序只从输入数据中移除一个待排序的元素，找到它在序列中适当的位置，并将其插入。
    重复直到所有输入数据插入完为止。

关键是几个链表节点的作用：

    dummy指向第一个节点，一是用于结果输出，二是寻找插入位置时用于回到起点
    cur用于遍历的节点
    pre一是用于和当前节点作比较，二是做标记，让交换后的cur回到pre后面

```java
public ListNode insertionSortList(ListNode head) {
    if (head == null || head.next == null) {
        return head;
    }
    ListNode dummy = new ListNode(0);
    ListNode pre = head;
    ListNode cur = head.next;
    dummy.next = head;
    while (cur != null) {
        if (pre.val <= cur.val) {// 本来就有序
            pre = cur;
            cur = cur.next;
        } else {
            ListNode p = dummy;
            // 找到一个位置使得p < cur < p.next
            while (p.next != cur && p.next.val < cur.val) {
                p = p.next;
            }
            // 将cur插入到p和p.next之间
            // 因为cur被插到前面，pre的指针需要跳过cur
            pre.next = cur.next;
            cur.next = p.next;
            p.next = cur;
            // 完成插入后，cur回到pre后面
            cur = pre.next;
        }
    }
    return dummy.next;
}
```



### 20、有序链表转换二叉搜索树 leet109

描述：给定一个**单链表**，其中的元素**按升序排序**，将其**转换为高度平衡的二叉搜索树**。

本题中，一个高度平衡二叉树是指一个二叉树每个节点 的**左右两个子树的高度差的绝对值不超过 1**。

示例:

给定的有序链表： [-10, -3, 0, 5, 9],

一个可能的答案是：[0, -3, 9, -10, null, 5], 它可以表示下面这个高度平衡二叉搜索树：

     	 0
     	/ \
       -3   9
       /   /
     -10  5
思路：因为链表是升序的，所以找出**链表的中点就是当前二叉搜索树的根节点**，然后在对左半部分和右半部分分别找他们的中点作为根节点，不断递归。

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
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public TreeNode sortedListToBST(ListNode head) {
        if(head == null) return null;
        //preSlow保存slow的前一个节点
        ListNode slow = head, fast = head, preSlow=null;
        // 1 找到当前链表中点
        while(fast!=null && fast.next!=null){
            preSlow = slow; //记录slow的前一个节点
            slow = slow.next;
            fast = fast.next.next;
        }
        // 2 构建当前中间节点的左右子树
        TreeNode root = new TreeNode(slow.val);
        if(preSlow != null){
            preSlow.next = null; //切断preSlow和重点slow
            root.left = sortedListToBST(head);//左半部分作为左子树
        }
        root.right = sortedListToBST(slow.next);//右半部分作为右子树
        return root;
    }
}
```

### 21、分割链表 leet86

描述：给定一个链表和一个特定值 x，对链表进行分隔，**使得所有小于 x 的节点都在大于或等于 x 的节点之前**。

你应当保留两个分区中每个节点的初始相对位置。

> 示例:
>
> 输入: head = 1->4->3->2->5->2, x = 3
> 输出: 1->2->2->4->3->5

思路：利用2个哑节点和助理节点，分别对大于x和小于x的拼接为有序链表，最后在拼接完成

```java
class Solution {
    public ListNode partition(ListNode head, int x) {
        // 借助虚拟头结点实现
        ListNode dummyHead1 = new ListNode(0);//哑节点
        ListNode dummyHead2 = new ListNode(0);
        ListNode node1 = dummyHead1;//助理节点
        ListNode node2 = dummyHead2;
        while (head != null) {
            if (head.val < x) {
                node1.next = head;
                node1 = node1.next;
                head = head.next;
                node1.next = null;//防止出现环
            } else {
                node2.next = head;
                node2 = node2.next;
                head = head.next;
                node2.next = null;
            }
        }
        node1.next = dummyHead2.next;
        return dummyHead1.next;
    }
}
```



### 22、奇偶链表分割重排 leet328

描述：给定一个单链表，**把所有的奇数节点和偶数节点分别排在一起**。请注意，这里的奇数节点和偶数节点指的是节点编号的奇偶性，而不是节点的值的奇偶性。

请尝试使用原地算法完成。你的算法的**空间复杂度应为 O(1)，时间复杂度应为 O(nodes)**，nodes 为节点总数。

> 示例 1:
>
> 输入: 1->2->3->4->5->NULL
> 输出: 1->3->5->2->4->NULL

思路：**将奇节点放在一个链表里，偶链表放在另一个链表里。然后把偶链表接在奇链表的尾部**。

​		一个 LinkedList 需要一个头指针和一个尾指针来支持双端操作。我们用变量 **head 和 odd 保存奇链表的头和尾指针**。 **evenHead 和 even 保存偶链表的头和尾指针**。算法会遍历原链表一次并把奇节点放到奇链表里去、偶节点放到偶链表里去。遍历整个链表我们至少需要一个指针作为迭代器。这里 odd 指针和 even 指针不仅仅是尾指针，也可以扮演原链表迭代器的角色。

    复杂度分析
    时间复杂度： O(n) 。总共有 n 个节点，我们每个遍历一次。
    空间复杂度： O(1) 。我们只需要 4 个指针。

```java
class Solution {
    public ListNode oddEvenList(ListNode head) {
        if(head == null) return null;
        ListNode odd=head;//奇数位置下标
        ListNode even=head.next, evenHead=even;//1偶数位下标和头结点
        //2 奇数头结点拼接奇数位 偶数位亦如此
        while(even !=null && even.next!=null){//注意此条件
            odd.next = even.next;
            odd = odd.next;
            even.next = odd.next;
            even = even.next;
        }
        odd.next = evenHead;//3 奇数链表 指向偶数头结点
        return head;
    }
}
```

### 23、二进制链表转整数 leet1290

描述:给你一个单链表的引用结点 head。链表中每个结点的值不是 0 就是 1。已知此链表是一个整数数字的二进制表示形式。请你返回该链表所表示数字的 十进制值 。

```xml
输入：head = [1,0,1]
输出：5
解释：二进制数 (101) 转化为十进制数 (5)
```

思路：由于链表中从高位到低位存放了数字的二进制表示，因此我们可以使用**二进制转十进制的方法**，**在遍历一遍链表的同时得到数字的十进制值**。

> **复杂度分析**
>
> - 时间复杂度：O(N)，其中 N 是链表中的节点个数。
> - 空间复杂度：O(1)。

```java
class Solution {
    public int getDecimalValue(ListNode head) {
        ListNode cur = head;
        int res = 0;
        while (cur != null) {
            //cur指针右移一位            
            //--1-- res:0  cur.val: 1 --> res:1   0 * 2 +  1
            //--2-- res:1  cur.val: 0 --> res:2   1 * 2 +  0
            //--3-- res:2  cur.val: 1 --> res:5   2 * 2 +  1
            //res:5 --> (((0 * 2 +  1) *2  + 0 ) *2   + 1 
            //                      1* 2^2  + 0 * 2^1 + 1 * 2^0     
            res = res *2 + cur.val;
            cur = cur.next;
        }
        return res;
    }
}
```

### 24、







## 树

### Key总结：

> 关于二叉树的**深度优先遍历**和**广度优先遍历**：
> 1、**深度优先遍历**常用的数据结构为**栈**。 **广度优先遍历**常用的数据结构为**队列**
>
> 2、**深度**优先遍历的思想是**从上至下**，**对每一个分支一直往下一层遍历直到这个分支结束**，**然后返回上一层**，**对上一层的右子树这个分支继续深搜**，直到一整棵树完全遍历，因此深搜的步骤符合**栈后进先出**的特点
> 	**广度**优先遍历的思想是**从左至右**，对树的**每一层所有结点依次遍历**，**当一层的结点遍历完全后，对下一层开始遍历**，而下一层结点又恰好是上一层的子结点。因此广搜的步骤符合**队列先进先出**的思想。
>
> 3、关于**二叉树的深度优先搜索--使用栈+迭代 或 递归方法**
> 则又有三种遍历方法
> 先序遍历：对任一子树，先访问根，然后遍历其左子树，最后遍历其右子树。
> 中序遍历：对任一子树，先遍历其左子树，然后访问根，最后遍历其右子树。
> 后序遍历：对任一子树，先遍历其左子树，然后遍历其右子树，最后访问根。
> 除了利用栈以外，深度优先搜索也可以使用递归的方法。
>
> 4、深度优先搜索算法：不全部保留结点，占用空间少；有回溯操作(即有入栈、出栈操作)，运行速度慢。
> 	广度优先搜索算法：保留全部结点，占用空间大； 无回溯操作(即无入栈、出栈操作)，运行速度快。

下面介绍二叉树的前序 后序 中序遍历代码 - **深度优先遍历DFS**  借助**栈**的**迭代**方法

```java
//前序遍历 根左右   -迭代-栈
List<Integer> list = new ArrayList<>(); //存储结果
public List<Integer> preOrder(TreeNode root){
    if(root == null) return list;
    Stack<TreeNode> stack = new Stack<>();
    stack.push(root); //0 先存储根节点
    while(!stack.isEmpty()){
        TreeNode temp = stack.pop();
        list.add(temp.val); // 1 存储根节点 每次循环开始先存储节点
        if(temp.right != null) stack.push(temp.right); //2 存储右节点，结合栈的结构 先进后出
        if(temp.left != null) stack.push(temp.left); //3 存储左节点 下一次该节点会先出来
    }
    return list;
}
// 递归方法
List<Integer> list = new ArrayList<>(); //存储结果  注意 使用递归的话 list需要是全局变量
public List<Integer> preOrder(TreeNode root){
    if(root == null) return list;
    if(root != null) list.add(root.val); // 中序、后序遍历对应此句位置就放在中间和最后
    if(root.left != null) preOrder(root.left);
    if(root.rigth != null) preOrder(root.right);
    return list;
}
```



```java
//后序遍历 左右根   -迭代-栈   **和前序相比 注意list.add(0,element)的位置**
List<Integer> list = new ArrayList<>(); //存储结果
public List<Integer> preOrder(TreeNode root){
    if(root == null) return list;
    Stack<TreeNode> stack = new Stack<>();
    stack.push(root); //0 先存储根节点
    while(!stack.isEmpty()){
        TreeNode temp = stack.pop();
        if(temp.left != null) stack.push(temp.left); //1 存储左节点 
        if(temp.right != null) stack.push(temp.right); //2 存储右节点
        list.add(0, temp.val); // 3 **存储根节点  add(0,ele) 头插法，根节点会在最后**所以存储就是根 右 左  反过来就是左右根-后序遍历！
    }
    return list;
}
```



```java
//中序遍历 左根右   -迭代-栈   **和前序后序相比 中序是先一直存储左节点到栈 左节点为空再找他的右节点**
List<Integer> list = new ArrayList<>(); //存储结果
public List<Integer> preOrder(TreeNode root){
    if(root == null) return list;
    Stack<TreeNode> stack = new Stack<>();
    
    while(!stack.isEmpty() || root != null){
        // 1 先一直遍历左节点 存入栈
        while(root != null){
            stack.push(root);
            root = root.left;
        }
        // 2 从下往上遍历 遇到有右节点的则遍历该节点的右子树
        if( !stack.isEmpty()){
            root = stack.pop();
            list.add(root.val);
            root = root.right; //3 ***判断该节点的右节点 有的话则遍历右子树
        }
    }
    return list;
}
```





------



### ***1、判断是否为镜像树(迭代实现)

**思路：**这道题用迭代手撕，简单级别。主要使用栈或者队列成对存放节点。

当首结点不为空，那么放入首结点的左孩子和右孩子；

如果都为null，那么也是对称树；

如果其中一个为空，另外一个不为空，则不对称；

如果上述成立 都不为空，则判断这两个节点的值是否相同。

最后成对放入 左节点的左子树和右节点的右子树 and 左结点右孩子和右节点左孩子。

```java
boolean isSymmetrical(TreeNode pRoot){
        if (pRoot == null) return true; //空节点也是镜像的
//注意点：
//        Queue queueBlock = new LinkedBlockingQueue();
//        queueBlock.add(null); // 阻塞队列不能存null 会报错
        //**LinkedList与ArrayList一样实现List接口 可以放null
        Queue<TreeNode> queue = new LinkedList<>();
       // queue.add(null);
        queue.add(pRoot.left);
        queue.add(pRoot.right);
        while (!queue.isEmpty()){
            TreeNode leftnode = (TreeNode)queue.poll();
            TreeNode rightnode = (TreeNode)queue.poll();
            //continue 语句是跳过循环体中剩余的语句而强制执行下一次循环，其作用为结束本次循环
            if (leftnode==null && rightnode==null) continue;
            if (leftnode==null || rightnode==null) return false;
            if (leftnode.val != rightnode.val){
                return false;
            }
            // 成对插入 左节点的左子树和右节点的右子树 and 左结点右孩子和右节点左孩子
            queue.add(leftnode.left);
            queue.add(rightnode.right);
            queue.add(leftnode.right);
            queue.add(rightnode.left);
        }
        return true;
    }
```



### *2、重建二叉树  #JZ4

描述：输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。**假设输入的前序遍历和中序遍历的结果中都不含重复的数字**。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。

1-root ，472在左子树上，5386在右子树上。

2-是左子树的第一个root，47在2-root的左子树上，右子树为null，4为root，4-root左子树为null，右子树为7

所以左子树：1.left=2 ，2.left=4，2.right=null，4.left=null，4.right=7，7.left=null，7.right=null

右子树同理

回顾：

1.前序遍历首先会打印根节点，然后对其左子树进行打印操作，再对其右子树进行打印操作

2.中序遍历则是不断地对左子树执行打印操作直到左子树为空时才进行实际地打印

（打印操作指递归函数，实际打印指print语句）

 		于是，我们可以设计递归过程，重建二叉树：

将前序序列的第一个值作为根节点，同时也将该值作为分割点将中序序列分割得到左子树的中序序列和右子树的中序序列以此得到左子树的节点个数L和右子树的节点个数R，将前序序列的除第一个值之外的前L个数作为左子树的前序序列，后R个数作为右子树的前序序列，对左子树和右子树重复上述操作直到序列都为null时返回

```java
public TreeNode reConstructBinaryTree(int[] pre, int[] in){ //前序和中序遍历
    if(pre.length ==0 || in.length==0) return null; //此时根节点已经没有后续结点了 返回null
    TreeNode root = new TreeNode(pre[0]);//pre的第一个元素就是根节点
    // 递归查找 每一个节点的子节点 直至没有子节点
    for(int i=0; i<in.length; i++){
        if(in[i] == pre[0]){ //如果是根节点 则继续寻找它的左右子树
            //copyOfRange 复制指定范围的数组到新数组  左闭右开[..)
            root.left = reConstructBinaryTree(Arrays.copyOfRange(pre,1,i+1),Arrays.copyOfRange(in,0,i));
            root.right = reConstructBinaryTree(Arrays.copyOfRange(pre,1+1,pre.length),Arrays.copyOfRange(in,i+1,in.length));
            break; //如果左右子树都找完了结束当前循环
        }
    }
    return root;
}
```



### *3、树的子结构  #JZ17

描述：输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构）

思路：1.首先寻找树1的某个结点是否和树2某个结点相等，分别遍历树1的根节点 左右节点，**递归**

2.如果有相同的节点，那么以此节点，判断树2的**所有孩子结点**和树1对应的所有孩子结点是否相同，如果都相同，最终会到null 则true，否则 如果树1先没有了后续结点 或 树1结点值不等于树2结点值 则false。



```java
public class Solution {
    public boolean HasSubtree(TreeNode root1,TreeNode root2) {
        boolean res = false;
        if(root1 != null && root2 != null){//只有两个树都不为空才比较，否则false
            //以下if是 如果某个结点出现相同  则调用自定义方法 从这个结点一直往下对比判断
            if(root1.val == root2.val){
                // 以这个根结点为起点判断是否包含Tree2
                res = doesTreeHaveTree2(root1, root2);
            }
            if(!res){
                // 如果找不到 则取root1的左儿子为起点，去判断左子树是否包含tree2
                res = HasSubtree(root1.left, root2);
            }
            if(!res){
                res = HasSubtree(root1.right, root2);//去右孩子判断
            }
        }
        return res;
    }
    
    public static boolean doesTreeHaveTree2(TreeNode node1, TreeNode node2){
        //如果Tre2都遍历完了 都可以对应上，则返回true
        if(node2 == null) return true;
        //如果Tree2还没有遍历完，Tree1却遍历完了。返回false
        if(node1 == null) return false;
        //如果其中有一个点没有对应上  返回false
        if(node1.val != node2.val) return false;
        
        //其他情况说明根节点都对应上 即相同 那么就分别去左右子节点里面匹配
        return doesTreeHaveTree2(node1.left, node2.left) && 			   		         doesTreeHaveTree2(node1.right, node2.right);
    }
    
}
```



### *4、从上往下打印二叉树   广度优先遍历-队列 #JZ22

描述：从上往下打印出二叉树的每个节点，同层节点从左至右打印。

思路：**这里和第一题 “判断是否为镜像二叉树”思路类似，** 中序遍历思想

**广度优先遍历。借助队列思想，将根节点放入队列，在循环中取出，然后再放入此节点的左右节点，。**

```java
import java.util.*;
public ArrayList<Integer> PrintFromTopToBottom(TreeNode root) {
    ArrayList<Integer> res = new ArrayList<Integer>();
    if(root == null) return res;
    Queue<TreeNode> q = new LinkedList<>(); //借助队列实现 *****存元素的类型是TreeNode
    q.offer(root); // 先放根节点
    while(!q.isEmpty()){
        res.add(q.peek().val);
        if(q.peek().left != null) q.offer(q.peek().left);
        if(q.peek().right != null) q.offer(q.peek().right);
        q.poll();// 获取并移除此队列的第一个元素，若队列为空，则返回 null
    }
    return res; //*******咋不写返回值？？？？
}

//note:  这里也可以只使用ArrayList模拟队列，list.add(root) 存放节点，再循环中使用 TreeNode temp = list.remove(0);得到集合中第一个元素并且移除这个元素 相当于poll(),剩下思路类似
```



### *4.1、二叉树按行打印 #JZ60

描述：从上到下按层打印二叉树，同一层结点从左至右输出。每一层输出一行。

思路：这题就是在**从上往下打印基础上多了个层次问题**。在while中加个int size = queue.size();就ok了

**这个size就是代表当前行有几个节点个数 把这一行的节点放在一个list集合中**。`

```java
import java.util.*;
public class Solution {
    ArrayList<ArrayList<Integer> > Print(TreeNode pRoot) {
        ArrayList<ArrayList<Integer>> outList = new ArrayList<>();
        if(pRoot == null) return outList;
        Queue<TreeNode> q = new LinkedList<>(); //ta可以存null blockingQueue不能存null
        q.offer(pRoot); //1 先存入根节点
        
        while(!q.isEmpty()){
            //这里和下面的for就是比从上到下打印树多的2步骤
            int size = q.size(); // 2 队列的长度就是当前行的节点个数。
            ArrayList<Integer> list = new ArrayList<>();
            //3 遍历当前层  放入list中
            for(int i=0; i<size; i++){ 
                TreeNode temp = q.poll();// 在这一行中 存入list并且存入当前节点的左右节点
                list.add(temp.val);//存入当前行的节点值
                //4 存放当前节点左右节点。因为对象都是引用传递  temp指向pROOT的地址,数据共享一块
                if(temp.left != null) q.offer(temp.left); 
                if(temp.right != null) q.offer(temp.right);
            }
            outList.add(list);
        }
        return outList;
    }
}
```

### **4.2、按之字打印二叉树  #JZ59

描述：请实现一个函数按照之字形打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右至左的顺序打印，第三行按照从左到右的顺序打印，其他行以此类推。

思路：这题就是在**上一题“按行打印二叉树”的基础上多了个正序倒序打印的问题！**。思路代码整体一样！

**在list存储一行数据的时候 根据标识为flag，进行正序存储list.add(element)或者逆序存储list.add(0,element)就ok**

```java
import java.util.*;
public class Solution {
    public ArrayList<ArrayList<Integer> > Print(TreeNode pRoot) {
        ArrayList<ArrayList<Integer>> res = new ArrayList<>(); //列表存放最外面的数据
        if(pRoot == null) return res;
        Queue<TreeNode> q = new LinkedList<>();//声明队列存放节点 这个可以存放null
        boolean flag = true; // 控制每次一存值是否需要反转
        q.add(pRoot); // 1先存根节点
        while(!q.isEmpty()){
            int size = q.size(); //2 当前行的节点个数
            ArrayList<Integer> list = new ArrayList<>();//存放内存shuuju
            // 循环存入 当前行的元素
            for(int i=0; i<size; i++){
                TreeNode node = q.poll();
                //  *****与上题不同的之处******
                if(flag)  list.add(node.val); //正序存储  这两步就是打印出“之”字
                else  list.add(0,node.val); //逆序存储
                if(node.left != null) q.offer(node.left); //存储当前节点的左右子树节点
                if(node.right != null) q.add(node.right);
            }
            res.add(list); //大列表存储每一层小列表
            flag = !flag; // ***控制正序倒序存入数据
        }
        return res;
    }
}
```







### *5、二叉搜索树与双向链表    #JZ26

**二叉搜索树也叫 二叉排序树 或 二叉查找树**

什么是二叉查找树：
根节点的值大于其左子树中任意一个节点的值，小于其右节点中任意一节点的值，这一规则适用于二叉查找树中的每一个节点。

<img src="C:\Users\guosiyu8\AppData\Roaming\Typora\typora-user-images\image-20200707210455696.png" alt="image-20200707210455696" style="zoom:50%;" />

**描述：**输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。

**思路：** 非递归---使用栈---深度优先遍历     整体就是**二叉树的中序遍历改版**

```
方法一：非递归版--利用栈存储节点
解题思路：
1.核心是中序遍历的非递归算法。
2.同时修改当前遍历节点与前一遍历节点的指针指向。
```

<img src="C:\Users\guosiyu8\AppData\Roaming\Typora\typora-user-images\image-20200707210236167.png" alt="image-20200707210236167" style="zoom: 67%;" />

```JAVA
// 定义树class
public class TreeNode{
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;
    public TreeNode(int val){this.val = val;}
}
import java.util.*;
public class solution{
    public TreeNode Convert(TreeNode root){
        if(root == null) return null;
        Stack<TreeNode> stack = new Stack<TreeNode>();//保存树节点
        TreeNode p = root; // 单独的助理节点 指向根节点
        TreeNode pre = null; // 用于保存中序遍历序列的上一个节点  因为要求双向链表
        boolean isFirst = true;
        while(p != null || !stack.isEmpty()){
            while(p != null){
                stack.push(p); // 放入当前根节点
                p = p.left; //一直往左遍历节点
            }
            p = stack.pop(); //每次都是从stack取出节点的
            if(isFirst){ // 第一次先 将中序遍历的第一个节点标记为root
                root = p; // ***root指向p，p是助理节点 也是引用类型****
                pre = root; //pre等于p 指向当前节点  因为最后p都会改变
                isFirst = false;
            }else{
                pre.right = p; //双端链表 指向当前根节点
                p.left = pre; //指向上个节点
                pre = p; // 上个节点变成当前根节点
            }
            p = p.right; // 之前p存储的是左节点，现在存储右节点  *****遍历该节点的右子树****
        }
        return root; // 返回root  因为root=p
    }
}
```



### **5.1、二叉搜索树的第K个节点  #JZ62

描述：给定一棵二叉搜索树，请找出其中的第k小的结点。例如， （5，3，7，2，4，6，8）  中，按结点数值大小顺序第三小结点的值为4。

思路：和第五题 类型，涉及到BST的遍历都是先一直遍历存储左子树节点，然后再存储右子树节点。

**标准的 中序迭代方法----使用栈存储Stack**

```java
// 1 迭代  优选！  
TreeNode KthNode(TreeNode pRoot, int k){
    if(pRoot == null || k==0) return null;
    int count = 0; //计数器 第k大
    Stack<TreeNode> stack = new Stack<>(); //*******使用栈存储******
    
    while(pRoot != null || !stack.isEmpty()){ // 两个while条件 注意
        // 1 循环存入左子树的节点
        while(pRoot != null){
            stack.push(pRoot);
            pRoot = pRoot.left;
        }
        //2 取出节点判断是否是第k大
        pRoot = stack.pop();
        count++;
        if(count == k) return pRoot;
        //3 寻找当前节点的右节点
        pRoot = pRoot.right;
    }
    return null;
}
```







```java
// 2 递归方法  递归中序遍历
import java.util.ArrayList;
//中序遍历 结果就是从小打到  然后倒数第几 就是k-1
public class Solution {
    ArrayList<TreeNode> list = new ArrayList<>(); // 1
    TreeNode KthNode(TreeNode pRoot, int k){
        addNode(pRoot); //中序排好序 就是递增的
        if(k>=1 && k<=list.size()) {
            return list.get(k-1);
        }
        return null;
    }
    //中序遍历  递归
    public void addNode(TreeNode node){
        if(node != null){
        addNode(node.left);
        list.add(node); //调整list.add的位置可以是前序遍历 后续遍历
        addNode(node.right); 
        }
    }


}
```







### **6 、二叉树的深度  #JZ38

描述:输入一棵二叉树，求该树的深度。从根结点到叶结点依次经过的结点（含根、叶结点）形成树的一条路径，最长路径的长度为树的深度。

思路:

二叉树：二叉树是每个结点最多有两个子树的树结构。通常子树被称作“左子树”（left subtree）和“右子树”（right subtree）。二叉树常被用于**实现二叉查找树和二叉堆**。
题目：求二叉树的深度，**从根节点到孩子节点的最长路径**。

1.递归：如果当前根为null就是0，然后一直向左子树遍历 再一直向右子树遍历，最后返回左右子树中最大值的那个再加1就是当前节点高度。最下面叶节点高度是1，依次向上是2 3 4.....

2.非递归：利用**队列**，count是当前的节点(不断累加)，nextcount是当前深度总的节点(控制有几个孩子节点)。【总是要遍历到当前深度nextCount 的最后一个节点，深度结果res才加1】  **层次遍历的思想**

```java
//1 递归方法  多理解
public class Solution {
    public int TreeDepth(TreeNode root) {
        if(root == null) return 0;
        int left = TreeDepth(root.left);
        int right = TreeDepth(root.right);
        return Math.max(left, right)+1; //注意 选择最大的 再加1 就是当前节点的高度 叶结点是1
        //return 1+(left>right ? left:right); //可替换上面
    }
}


//2 迭代方法 层次遍历-队列
import java.util.*;
public class Solution {
    public int TreeDepth(TreeNode root) {
        if(root == null) return 0;
        Queue<TreeNode> q = new LinkedList<>(); //使用队列
        q.add(root); // 循环之前先放入root节点
        int res=0, count=0, nextCount=q.size();// 结果res、当前节点计数、当前深度的节点数
        
        while(!q.isEmpty()){
            TreeNode t = q.poll();
            count++;
            if(t.left != null) q.add(t.left);
            if(t.right !=null) q.add(t.right);
            if(count == nextCount){
                res++;//结果
                count = 0;//当前节点计数清零
                nextCount = q.size(); //当前深度的节点数更新为队列中数目
            }
        }
        return res;
    }
}
```



#### *6.1 验证平衡二叉树 #JZ39

**描述：**  输入一棵二叉树，判断该二叉树是否是平衡二叉树。 

在这里，我们只需要考虑其平衡性，不需要考虑其是不是排序二叉树 

**思路：**平衡二叉树对定义：平衡二叉树的左右子树也是平衡二叉树，那么所谓平衡就是左右子树的高度差不超过1.

1.判断树是否为空，**空则返回true**
2.**判断左右子树深度差**，其中，**求树深度的函数在上一题中“二叉树的深度中”已实现**，差超过1，返回false
3.若通过2的判断，**对左右子树也判断是否都是平衡二叉树**，判断函数为函数自身，**递归调用**

```java
public class Solution {
    public boolean IsBalanced_Solution(TreeNode root) {
        if(root == null) return true; //空树也是平衡的
        return depth(root) != -1; //不等于-1代表高度差都是小于1
    }
    // 这里就是求解 树的高度  和上一题一样  只不过过程中加了平衡二叉树的判断
    private int depth(TreeNode root){
        if(root == null) return 0;
        int left = depth(root.left); // 一直向左子树遍历
        if(left == -1) return -1; //如果发现子树不平衡之后就没有必要进行下面的高度的求解了
        int right = depth(root.right);// 一直向右子树遍历
        if(right == -1) return -1;
       	//判断平衡二叉树的条件 高度差小于1。
        if(left-right<-1 || left-right>1){  //等价于 Math.abs(left-right)>1
            return -1;
        }else{
            return 1 + (left>right ? left:right); //*****注意三目运算单独加括号**********   等价于Math.max(l,r);
        }
    }
}
```





### *7、判断数组是否为BST的后序遍历 #JZ23

描述：输入一个整数数组，判断**该数组**是不是某**二叉搜索树**的**后序遍历的结果**。如果是则输出Yes,否则输出No。假设输入的数组的任意两个数字都互不相同。

思路：

<img src="/image-20200727115010974.png" alt="image-20200727115010974" style="zoom:33%;" />

    一棵 BST ：左孩子 < 根结点 < 右孩子
    一棵 BST 的左子树或者右子树都是 BST
后序遍历是，左右根：[3, 4, 9, 5, 12, 11, 10]，结合图再从左往右分析后序序列，分析子树，可以发现：
    [3, 4, 9, 5] 10 [12, 11]   10为当前树的根
        [3, 4] 5 [9]   5为当前树的根
            [3] 4  4为当前树的根
        [12] 11   11为当前树的根
发现对于每一棵子树，**它的根结点总是对应该子树的后序序列的最后一个数**
那么，**只需要不断地确定出左子树区间和右子树区间**，**并且判断：左子树区间的所有结点值 < 根结点值 < 右子树区间所有结点值**，
这个条件是否满足即可

```java
public class Solution {
    public boolean VerifySquenceOfBST(int [] sequence) {
        if(sequence == null || sequence.length==0) return false;
        return isBST(sequence, 0, sequence.length-1);
    }
    
    private boolean isBST(int[] seq, int start, int end){
        if(start >= end) return true;
        int val = seq[end]; //记录后序遍历的最后一个 这是根节点
        int split = start; //分割的下标
         //1 判断该子树左节点小于根节点
        while(split<end && seq[split] < val){
            split++; //寻找当前数根节点val的分界处 0-split-1 是比val小的，split-end-1是比val大的
        }
         //2 判断该子树右节点大于根节点
        for(int i=split; i<end; i++){
            if(seq[i] < val) return false; //再分割位置后面的应该比val都大 如果有小的就不是BST
        }
        //3 对当前根节点的左右子树再去判读
        return isBST(seq, start, split-1) && isBST(seq, split,end-1);
    }
}
```





### **8、找二叉树的下一个节点  #JZ57

**描述：**给定一个二叉树和其中的一个结点，请找出**中序遍历顺序的下一个结点**并且返回。注意，树中的结点不仅包含左右子结点，同时包含指向父结点的指针。

思路： **本题是根据中序遍历的特点找的规律**

1.如果为null  直接返回

2.**如果该节点有右子树**  则**右子树最左边的节点就是该节点的下一个节点**

3.**如果该节点没有右子树**，则**找该节点 == 它父节点的左孩子** 的那个节点就是下一个节点

```java
/*
public class TreeLinkNode {
    int val;
    TreeLinkNode left = null;
    TreeLinkNode right = null;
    TreeLinkNode next = null;

    TreeLinkNode(int val) {
        this.val = val;
    }
}
*/
// 注意此题目的规则是基于 中序遍历下
public class Solution {
    public TreeLinkNode GetNext(TreeLinkNode pNode)
    {
        if(pNode == null) return null;
        //1.如果该节点有右子树  则右子树最左边的节点就是该节点的下一个节点
        if(pNode.right != null){
            pNode = pNode.right;//找该节点的右子树
            while(pNode.left != null){
                pNode = pNode.left;
            }
            return pNode;
        }
        //2. 如果该节点没有右子树，则找该节点 = 它父节点的左孩子 的那个节点就是下个节点
        while(pNode.next != null){
            if(pNode == pNode.next.left){
                return pNode.next;
            }
            pNode = pNode.next; //继续向上找父节点的左孩子 = 当前节点
        }
        return null;// 向上找到根节点了还没有找到  则返回null
    }
}
```

### ?9、二叉树和为某值的一条路径   #JZ24

**描述：**输入一颗二叉树的根节点和一个整数，按字典序打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。

思路： 深度优先遍历。其实这一题就是引出所有的从根结点到叶子结点的路径的变形，就在判断条件上多了一个 sum(path) == target。

```java
public class Solution {
    private ArrayList<ArrayList<Integer>> res = new ArrayList<ArrayList<Integer>>();
    private ArrayList<Integer> list = new ArrayList<>();
    public ArrayList<ArrayList<Integer>> FindPath(TreeNode root,int target) {
        if(root == null) return res;
        list.add(root.val);
        target -= root.val; //除去当前根的值 还需要多少值加起来等于target
        if(target == 0 && root.left==null && root.right==null){//节点必须是叶子节点
            res.add(new ArrayList<Integer>(list));
        }
        FindPath(root.left, target);
        FindPath(root.right, target);
        list.remove(list.size()-1);
        return res;
    }
}
```



### *10、最小高度树   #leet

描述：**给定一个有序整数数组**，元素各不相同且按**升序**排列，编写一个算法，**创建一棵高度最小的二叉搜索树**。

**思路：递归构建**   **升序想到二分法的思想** 找到中间的就当作根
要求高度最小，保持高度平衡，也就是说左右子树的节点个数应该尽可能接近，那么我们可以如此进行：

    用nums数组的中间值mid作为根节点，根据mid划分左子数组和右子数组。左子数组构建左子树，右子数组构建右子树。
    那么现在的问题就转化为怎么用左子数组构建左子树/右子数组构建右子树，以左子数组构建左子树为例；
    为了保持高度平衡，继续采用（1）中的划分方法，划分出新的左子数组和右子数组；
    当左子数组/右子数组为空时，返回null。

右子数组构建右子树的过程与上述相同。

```java
class Solution {
    public TreeNode sortedArrayToBST(int[] nums) {
        return helper(nums, 0, nums.length - 1);
    }

    public TreeNode helper(int[] nums, int low, int high) {
        if (low > high) {   // low > high表示子数组为空
            return null;
        }
        // 以mid作为根节点
        int mid = (high - low) / 2 + low;
        TreeNode root = new TreeNode(nums[mid]);
        // 左子数组[low, mid -1]构建左子树
        root.left = helper(nums, low, mid - 1);
        // 右子数组[mid + 1, high]构建右子树
        root.right = helper(nums,mid + 1, high);
        return root;
    }
}
```





























------



## 数学思维

### **0、判断回文数

> //**任何一个数除以10的余数就是该数最后一位**
> //**任何一个数除以10的商就是排除掉最后一位后的数**
> //所以 ，一个数1234 就可以通过这种方法得到 123 和 4
> //接下来对 123 进行同样的操作，就得到 12 和 3
> //接下来得到 1 和 2
> //接下来得到 0 和 1
> //整个过程是个循环，当商不是 0 的时候就一直这么干
> //每次拿到一个余数，都用来构造新数，**新数=新数*10+余数**
> //所以经过四次循环后，我们得到新数 4321 ，**如果是回文，那么新数应该等于原数，否则，说明不是回文**。

```java
import java.util.Scanner;
public class huiwen {
		public static void main(String [] args) {
		Scanner scan = new Scanner(System.in);
		System.out.println("请输入一个整数:");
		int hw=scan.nextInt();
		int y=0;
		int h=hw;
		while(hw>0){
			y=y*10+hw%10;
			hw=hw/10;
		}
		if(y==h){
			System.out.println("是回文数");
		}else{
			System.out.println("不是回文数");
		}
}
}
```



### 1、二进制中1的个数 #JZ11

描述：输入一个**整数**，输出该数32位二进制表示中1的个数。其中负数用补码表示。

思路：

​	如果一个整数**不为0**，那么这个整数**至少有一位是1**。

​	如果我们把这个整数减1，那么原来处在整数最右边的1就会变为0，原来在1后面的所有的0都会变成1(如果最右边的1后面还有0的话)。其余所有位将不会受到影响。
​	举个例子：一个二进制数1100，从右边数起第三位是处于最右边的一个1。减去1后，第三位变成0，它后面的两位0变成了1，而前面的1保持不变，因此得到的结果是1011.我们发现减1的结果是把最右边的一个1开始的所有位都取反了。这个时候如果我们再把原来的整数和减去1之后的结果做**与运算**，从原来整数最右边一个1那一位开始所有位都会变成0。**如1100&1011=1000**.也就是说，**把一个整数减去1，再和原整数做与运算，会把该整数最右边一个1变成0**。**那么一个整数的二进制有多少个1，就可以进行多少次这样的操作**。

```java
public class Solution{
    public int numberOf1(int n){
        int count = 0;
        while(n != 0){
            n = n&(n-1);
            count++;
        }
        return count;
    }
}
```



### 2、数值的整数次方 #JZ12

描述：  给定一个double类型的浮点数base和int类型的整数exponent。求base的exponent次方。保证base和exponent不同时为0 

思路： 

```java
public double Power(double base, int exponent) {
    if(base==1.0) return 1.0;  
    if(exponent==0) return 1.0;  // 细节考虑全面
    double res = 1.0;
    int loop = exponent>0 ? exponent:-exponent;  //**注意 方便起见 先按照正数处理
    for(int i=0;i<loop;i++){
        res *= base;
    } 

    return exponent>0 ? res : 1/res; // **如果是负数，结果就是res的倒数
}
```



### 3、求 1+2+3....+n   #JZ12

描述：求1+2+3+...+n，要求不能使用乘除法、for、while、if、else、switch、case等关键字及条件判断语句（A?B:C）。

思路：**短路求值**

先给自己普及大佬们口中的**短路求值**是什么。
作为**"&&"和"||"**操作符的操作数表达式，这些表达式在进行求值时，只要**最终的结果已经可以确定是真或假，求值过程便告终止**，这称之为**短路求值**（short-circuit evaluation）。
假如expr1和expr2都是表达式，并且expr1的值为0，在下面这个逻辑表达式的求值过程中：

- expr1 && expr2 
- expr2将不会进行求值，因为整个逻辑表达式的值已经可以确定为0。 
- expr1 || expr2 
- 当expr1=1时，expr2将不会进行求值，因为整个逻辑表达式的值已经确定为1。 


**思路**：因此可以**利用左边的表达式来作为递归结束的判断条件**。因此**递归的表达式就在右边了**。而想到递归的解法，必然是sum=Sum(n)=Sum(n-1)+n
**使用&&,表示两边都为真，才为真，左边为假，右边就没用了。因此在不断递归时，直到左边为假时，才不执行右边。因此在第一次进行右边的判断时，就进入递归的调用。**
  想到结束条件在左边，只能是n=0时结束，即从n递减到0结束，所以递归的调用理所当然放在了右边。由于左边需要不断的进行条件判断，因为需要一个每次递归后都递减的变量，而n是递减的，因此用n来作为左边的变量，int sum = n; 而短路求值左边可以写为(n>0)或（n!=0），右边写为sum +=sum(n-1)，再加一个判断（实际上**需要保证右边的条件一直为真**），因此java版的解法就出来了.

```java
public int Sum_Solution(int n) {
    int sum = n;
    //左边判断何时结束 右边递归一直为真进行计算 n+n-1+n-2.。。。+1
    boolean f = (sum>0) && (sum += Sum_Solution(sum-1))>0;
    return sum;
}
```

### 4、不用加减乘除做加法

描述：写一个函数，求两个整数之和，要求在函数体内不得使用+、-、*、/四则运算符号。

思路：

首先看十进制是如何做的： 5+7=12，三步走 第一步：相加各位的值，不算进位，得到2。 

第二步：计算进位值，得到10. 如果这一步的进位值为0，那么第一步得到的值就是最终结果。

第三步：重复上述两步，只是相加的值变成上述两步的得到的结果2和10，得到12。 

**同样我们可以用三步走的方式计算二进制值相加**： 5-101，7-111 

第一步：**相加各位的值，不算进位**，得到010，二进制**每位相**加就相当于各位做**异或操作**，101^111。

第二步：**计算进位值**，得到1010，相当于各位做**与操作**得到101，**再向左移一位**得到1010，(101&111)<<1。

第三步重复上述两步， 各位相加 010^1010=1000，进位值为100=(010&1010)<<1。     

继续重复上述两步：1000^100 = 1100，进位值为0，跳出循环，1100为最终结果。

```java
public int Add(int num1,int num2) {
    while(num2 != 0){
        int temp = num1^num2; //相加不算进位
        num2 = (num1&num2)<<1; //计算进位
        num1 = temp;
    }
    return num1;
}
```

### 5、把字符串转换为整数

描述：将一个字符串转换成一个整数，要求不能使用字符串转换整数的库函数。        数值为0或者字符串不是一个合法的数值则返回0

输入一个字符串,包括数字字母符号,可以为空。如果是合法的数值表达则返回该数字，否则返回0

示例1 输入+2147483647   1a33

输出2147483647    0

```java
//补充
for(int i = 0;i<str.length();i++)
{
    char temp_char = str.charAt(i);
    //把字符转换成数字方法一
    int temp_int = temp_char-'0';
    //把字符转换成数字方法二
    int temp_int = Integer.parseInt(String.valueOf(temp_char));

}
```



```java
// 一场捕获方法  钻空子
public int StrToInt(String str) {
    Integer res = 0;
    try{
        res = new Integer(str);  //可以直接转换为 Integer！！！666
    }catch(NumberFormatException e){
        return 0;
    }finally{
        return res;
    }
}

//最优方法
public class Solution {
    public int StrToInt(String str) {
         //最优解
        if(str == null || "".equals(str.trim())) return 0; 
        str = str.trim();//str.trim()去除空格
        char[] arr = str.toCharArray(); //字符串转换为char数组
        int i = 0;
        int flag = 1; //正负号
        int res = 0; //结果
        if(arr[i] == '-'){ //注意用单引号
            flag = -1;
        }
        if( arr[i] == '+' || arr[i] == '-'){
            i++; //除了开头的符号位 往后走
        }
        while(i < arr.length ){
            //是数字
            if(isNum(arr[i])){
                int cur = arr[i] - '0'; //** char减去'0' 相当于转换为int*****
                // 下面这两句不写 也可通过 ，判断是否数值溢出
                if(flag == 1 && (res > Integer.MAX_VALUE/10 || res == Integer.MAX_VALUE/10 && cur >7)){
                    return 0;
                }
                if(flag == -1 && (res > Integer.MAX_VALUE/10 || res == Integer.MAX_VALUE/10 && cur >8)){
                    return 0;
            }
                res = res*10 +cur; //结果乘10加cur当前值
                i++; //向后遍历
            }else{
                //char数组中有一个不是数字  就结束
                return 0;
            }
        }
        return res*flag; //乘以符号
    }
    public static boolean isNum(char c){ //判断char大小
        return c>='0'&& c<='9';
    }
}
```







## 穷举

### *1、和为S的连续正整数序列

**描述：**小明很喜欢数学,有一天他在做数学作业时,要求计算出9~16的和,他马上就写出了正确答案是100。但是他并不满足于此,他在想究竟有多少种连续的正数序列的和为100(**至少包括两个数**)。没多久,他就得到另一组连续正数和为100的序列:18,19,20,21,22。现在把问题交给你,你能不能也很快的找出所有和为S的连续正数序列? Good Luck!

**思路：双指针**

输入sum=20（1，2，3，4，5，6，7，8，9，10，11，12，13，14，15 

1，**定义两个指针，左指针从1开始**，**右指针从2开始** 循环开始 

2，求和1+2 = 3，如果判断**3小于20，右指针++**，2变为3，

3，求和3+3=6。**循环一直到右指针=6**，和为21。 

4，**else if 判断21大于20，左指针++**，1变为2，和减去左指针值，和为21-1=20。

5，**else 和与输入一样，存数**。   【**再把右指针++，求和，求剩余组合**】 循环结束

```java
import java.util.ArrayList;
public class Solution {
    public ArrayList<ArrayList<Integer> > FindContinuousSequence(int sum) {
       ArrayList<ArrayList<Integer> > res = new ArrayList<ArrayList<Integer> >();
        if(sum < 3) return res;  // 最小是 1 2 相加就是3 左右知识大于等于3
        int left = 1; //左指针
        int right = 2; //右指针
        int sumVal = left + right;
        while(left<right && right<sum){ //右指针>sum 就一定不会等于sum
            if(sumVal < sum){
                right++;  //右指针先自增
                sumVal += right;
            }else if(sumVal > sum){
                sumVal -=left; //先减去原左指针的值 再自减
                left++;
            }else{
                ArrayList<Integer> list = new ArrayList<>();
                for(int i=left; i<=right; i++){
                    list.add(i);
                }
                res.add(list);
                right++;   // 保存完结果后右指针继续向右走 并且累加
                sumVal += right;
            }
        }
        return res;
    }
}
```



### ？2、丑数

描述：把**只包含质因子2、3和5的数称作丑数**（Ugly Number）。例如6、8都是丑数，但14不是，因为它包含质因子7。 习惯上我们把1当做是第一个丑数。**求按从小到大的顺序的第N个丑数**。

链接：https://www.nowcoder.com/questionTerminal/6aa9e04fc3794f68acf8778237ba065b?answerType=1&f=discussion

这道题目自己是有思路的，丑数能够分解成2^x*3^y*5^z,
所以只需要把得到的丑数不断地乘以2、3、5之后并放入他们应该放置的位置即可，
而此题的难点就在于如何有序的放在合适的位置。
1乘以 （2、3、5）=2、3、5；2乘以（2、3、5）=4、6、10；3乘以（2、3、5）=6,9,15；5乘以（2、3、5）=10、15、25；
从这里我们可以看到如果不加策略地添加丑数是会有重复并且无序，
而在2*x，3*y，5*z中，如果x=y=z那么最小丑数一定是乘以2的，但关键是有可能存在x》y》z的情况，所以我们要维持三个指针来记录当前乘以2、乘以3、乘以5的最小值，然后当其被选为新的最小值后，要把相应的指针+1；因为这个指针会逐渐遍历整个数组，因此最终数组中的每一个值都会被乘以2、乘以3、乘以5，也就是实现了我们最开始的想法，只不过不是同时成乘以*2、*3、*5，而是在需要的时候乘以*2、*3、5.

```java
public int getUglyNumberK(int index){
    if(index <= 0) return 0;
    int p2=0, p3=0, p5=0; //初始化三个指针 指向潜在成为最小丑数的位置
    int[] res = new int[index];
    res[0] = 1; //默认最小丑数1
    for(int i =1; i<index; i++){
        res[i] = Math.min(res[p2]*2, Math.min(res[p3]*3,res[p5]*5));
        if(res[i] == res[p2]*2) p2++; //防止重复需要三个if都可走到
        if(res[i] == res[p3]*3) p3++;
        if(res[i] == res[p5]*5) p5++;
    }
    return res[index-1];
}
```

### ？3、超级丑数

描述：

思路：Java **动态规划** 超级简单 分析透彻
简单分析过程：

1 大家应该都做过丑数的题目。套路就是：**为每个质因数建立一个指针，然后再这几个质因数运算的结果中，找出个最小的，然后匹配这个数是由哪个质因数算出来的，把它的指针值+1**。
2 这道题的套路也差不多。只不过，因为我们这次是需要把计算出来的丑数再次和primes里面的质因数结合，算出新的丑数。算出来的丑数放在一个dp数组里。
3 所以，现在要做的事，怎么能知道每个质因数已经和dp中哪个位置的丑数进行了结合，下一个要结合的位置在哪。就需要建立一个index数组，用来存放每个质因数下一个将要结合的dp的下标，这个下标是从0开始的，每结合一次就+1。extra：有个细节我会在注释里写一下，就是如果出现不同的质因数相乘，乘出来的结果是相同的，是重复的丑数，这个时候该怎么办呢：应该把这几个质因数下一个要结合的dp下标都加1。因为只把其中一个+1的话，下一次计算的丑数一定会是刚才这个重复的丑数，你的结果中就会有很多重复的数，所以，全都加1的话就会把这个重复数给过滤掉了。
4 好了，现在就可以写代码了

```java
public class SuperUglyNumber {
    public int nthSuperUglyNumber(int n, int[] primes) {
        int len = primes.length;
        int dp[] = new int[n];
        dp[0] = 1;
        /*梳理一下思路，dp[i]保存的是第i个超级丑数*/
        /*index[i]表示的是primes里面的第i个数下一个将要相乘的数在dp中的位置，
        反过来想，对于每个primes来说，我们都需要和dp中已经算出来的结果相乘算，
        然后取最小的那个作为新的dp元素
        索引index实际上表示是这个素数已经和dp中的哪个位置结合了，下一个位置的坐标是多少 */
        int index[] = new int[len];
        /*可能存在重复的丑数，所以呢，不要在for循环里面加break，把所有的情况都+1*/
        for (int i = 1; i < n; i++) {
            int min = Integer.MAX_VALUE;
            /*遍历对比一下值，找出最小的，*/
            for (int j = 0; j < len; j++) {
                if (min > primes[j] * dp[index[j]]) {
                    min = primes[j] * dp[index[j]];//这个地方就是当前质因数和他要结合的dp
                }
            }
            dp[i] = min;
            /*那个素数要乘以dp的坐标index要加1，向后推一个位
            * 如果存在重复的值，也就是说不同的质因数相乘，得出来相同的结果了，
            * 我们就把这几个位置都+1，这样就可以避免出现重复的值了。
            * 你想想，假如你找到了对应的素数的index，把它加1之后就break掉，那么后面的数也可以算出这个结果，
            * 下次循环的时候，势必会把这个重复的数当成下一个dp，因为这个数肯定要比下一个丑数小
            * 所以我们在for循环中不要加break；*/
            for (int j = 0; j < len; j++) {
                if (min == primes[j] * dp[index[j]]) {
                    index[j]++;
                }
            }

        }
        return dp[n - 1];
    }
}
```













------



## Java程序

### 1、单例模式

**note:得到class的三种方式：**

**studengt.getClass()     Student.class    class.forName("com.pojo.Student")**

```java
public class Singleton{
    private static Singleton singleton = null; //私有静态  可以添加volatile防止指令重排
    private Singleton(){}; //声明私有的构造方法
    public static Singleton getSingleton(){ //私有方法调用私有变量
        if(singleton == null){
            synchronized(Singleton.class){ //双重检验  不需要可去掉  synchronized
                if(singleton == null){ //双重检验  不需要可去掉
                    singleton = new Singleton();
                }
            }
        }
        return singleton;
    }
}
```



### 2、LRU缓存机制的实现



一、什么是 LRU 算法

就是一种缓存淘汰策略。

计算机的缓存容量有限，如果缓存满了就要删除一些内容，给新内容腾位置。但问题是，删除哪些内容呢？我们肯定希望删掉哪些没什么用的缓存，而把有用的数据继续留在缓存里，方便之后继续使用。那么，什么样的数据，我们判定为「有用的」的数据呢？

**LRU 缓存淘汰算法**就是一种常用策略。**LRU 的全称是 Least Recently Used(最近最少使用策略)**，也就是说我们认为**最近使用过的数据应该是是「有用的」**，**很久都没用过的数据应该是无用的**，**内存满了就优先删那些很久没用过的数据**。当然还有其他缓存淘汰策略，比如不要按访问的时序来淘汰，而是按访问频率（LFU 策略）来淘汰等等，各有应用场景。本文讲解 LRU 算法策略。

二、LRU 算法描述

LRU 算法实际上是让你设计数据结构：首先要接收一个 capacity 参数作为缓存的最大容量，然后实现两个 API，一个是 put(key, val) 方法存入键值对，另一个是 get(key) 方法获取 key 对应的 val，如果 key 不存在则返回 -1。

注意哦，**get 和 put 方法必须都是 O(1) 的时间复杂度**，**HashMap查询元素和链表添加删除元素的时间复杂度都是O(1),所以LRU的实现就依靠这两种数据结构**.

我们举个具体例子来看看 LRU 算法怎么工作。

```xml
/* 缓存容量为 2 */
LRUCache cache = new LRUCache(2);
// 你可以把 cache 理解成一个队列
// 假设左边是队头，右边是队尾
// 最近使用的排在队头，久未使用的排在队尾
// 圆括号表示键值对 (key, val)

cache.put(1, 1);
// cache = [(1, 1)]
cache.put(2, 2);
// cache = [(2, 2), (1, 1)]
cache.get(1);       // 返回 1
// cache = [(1, 1), (2, 2)]
// 解释：因为最近访问了键 1，所以提前至队头
// 返回键 1 对应的值 1
cache.put(3, 3);
// cache = [(3, 3), (1, 1)]
// 解释：缓存容量已满，需要删除内容空出位置
// 优先删除久未使用的数据，也就是队尾的数据
// 然后把新的数据插入队头
cache.get(2);       // 返回 -1 (未找到)
// cache = [(3, 3), (1, 1)]
// 解释：cache 中不存在键为 2 的数据
cache.put(1, 4);    
// cache = [(1, 4), (3, 3)]
// 解释：键 1 已存在，把原始值 1 覆盖为 4
// 不要忘了也要将键值对提前到队头
```

**三、LRU 算法设计**

分析上面的操作过程，要让 put 和 get 方法的时间复杂度为 O(1)，我们可以总结出 cache 这个数据结构必要的条件：**查找快，插入快，删除快，有顺序之分**。

因为**显然 cache 必须有顺序之分，以区分最近使用的和久未使用的数据**；而且我们**要在 cache 中查找键是否已存在**；如果容量满了要删除最后一个数据；每次访问还要把数据插入到队头。

那么，什么数据结构同时符合上述条件呢？哈希表查找快，但是数据无固定顺序；链表有顺序之分，插入删除快，但是查找慢。所以结合一下，形成一种新的数据结构：哈希链表。

**LRU 缓存算法的核心数据结构就是哈希链表，双向链表和哈希表的结合体**。如下图

<img src="/LRU数据结构_2020-08-17_15-00-38.png" style="zoom:50%;" />

问：为什么要是双向链表，单链表行不行？另外，既然哈希表中已经存了 key，为什么链表中还要存键值对呢，只存值不就行了？

答：因为我们需要删除操作。删除一个节点不光要得到该节点本身的指针，也需要操作其前驱节点的指针，而双向链表才能支持直接查找前驱，保证操作的时间复杂度 `O(1)`。

四、代码实现

很多编程语言都有内置的哈希链表或者类似 LRU 功能的库函数，但是为了帮大家理解算法的细节，我们用 Java 自己造轮子实现一遍 LRU 算法。  

Java的LinkedHashMap就是此结构，其中可以按照访问顺序排序 自定义实现删除策略，进而基于LinkedHashMap接口实现LRU算法

```java
class LRUCache {
    //数据结构使用hashmap和双端列表
    //1 定义结点(链表的结点)
    class Node{
        public int key, val;
        public Node next, pre; //前后指针
        public Node(int k, int v){
            this.key = k;
            this.val = v;
        }
    }
    //2 定义双端链表结构 和 操作方法
    class DoubleList{
        private Node head, tail; //头节点 尾结点
        private int size; //链表长度
        // 2.1 在链表头位置插入元素
        public void addFirst(Node node){
            if(head == null) head=tail=node;
            else{
                Node n = head;//头节点助理结点
                n.pre = node;
                node.next = n;
                head = node;//更新头节点位置
            }
            size++; //链表容量加1
        }
        //2.2  删除元素
        public void remove(Node node){
            if(head==node && tail==node){ //头尾就一个结点
                head = null;
                tail = null;
            }else if(tail == node){// 删除结点是尾结点
                node.pre.next = null; //指向null
                tail = node.pre;//更新尾节点位置
            }else if(head == node){ // 删除结点是头节点
                node.next.pre = null;
                head = node.next;
            }else{
                node.pre.next = node.next;
                node.next.pre = node.pre;
            }
            size--; //链表容量减去1
        }
        //2.3 删除尾结点
        public Node removeLast(){
            Node node = tail;
            remove(node); //删除尾节点
            return node;
        }
        //2.4 返回链表容量大小
        public int size(){
            return size;
        }
    }   

    //3  初始化LRU算法相关操作
    private HashMap<Integer, Node> map; //映射map中值和链表的关系
    private DoubleList cache;
    private int cap;///链表容量
    
    public LRUCache(int capacity) {
        this.cap = capacity;
        map = new HashMap<>();
        cache = new DoubleList();
    }
    
    public int get(int key) {
        if(!map.containsKey(key)) return -1;
        int val = map.get(key).val; 
        put(key, val); //查询也更新结点位置。获取元素也把当前获取的值更新到链表前面去
        return val; 
    }
    // 添加元素 如果不存在则加入，如果存在则更新数值 涉及到操作就把当前数放到链表前面去
    public void put(int key, int value) {
        Node x = new Node(key, value);//新节点
        if(map.containsKey(key)){ //key已存在
            cache.remove(map.get(key)); //先移除这个结点
            cache.addFirst(x); //添加到头节点处 表示更被使用 最新的
            map.put(key, x);//更新map映射关系
        }else{ //不存在结点
            if(cap == cache.size()){ //先判断是否达到链表大小 达到话先移除最后一个结点
                Node last = cache.removeLast(); //** 移除最近最少使用的结点  即链表最后的结点
                map.remove(last.key); //更新映射关系
            }
            //添加新结点
            cache.addFirst(x);
            map.put(key, x);
        }
    }
}//end

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```



# 线程

## 1、交替打印foobar  let1115

我们提供一个类：

class FooBar {
  public void foo() {
    for (int i = 0; i < n; i++) {
      print("foo");
    }
  }

  public void bar() {
    for (int i = 0; i < n; i++) {
      print("bar");
    }
  }
}

两个不同的线程将会共用一个 FooBar 实例。其中一个线程将会调用 foo() 方法，另一个线程将会调用 bar() 方法。

请设计修改程序，以确保 "foobar" 被输出 n 次。

 

示例 1:

输入: n = 1
输出: "foobar"
解释: 这里有两个线程被异步启动。其中一个调用 foo() 方法, 另一个调用 bar() 方法，"foobar" 将被输出一次。

示例 2:

输入: n = 2
输出: "foobarfoobar"
解释: "foobar" 将被输出两次。

```java
class FooBar {
    private int n;

    public FooBar(int n) {
        this.n = n;
    }

    Semaphore foo = new Semaphore(1);
    Semaphore bar = new Semaphore(0);

    public void foo(Runnable printFoo) throws InterruptedException {
        for (int i = 0; i < n; i++) {
            foo.acquire();
            printFoo.run();
            bar.release();
        }
    }

    public void bar(Runnable printBar) throws InterruptedException {
        for (int i = 0; i < n; i++) {
            bar.acquire();
            printBar.run();
            foo.release();
        }
    }
}

```



## 2、打印零与奇偶数  leet1116

```java
相同的一个 ZeroEvenOdd 类实例将会传递给三个不同的线程：
    线程 A 将调用 zero()，它只输出 0 。
    线程 B 将调用 even()，它只输出偶数。
    线程 C 将调用 odd()，它只输出奇数。
每个线程都有一个 printNumber 方法来输出一个整数。请修改给出的代码以输出整数序列 010203040506... ，其中序列的长度必须为 2n。

示例 1：
输入：n = 2
输出："0102"
说明：三条线程异步执行，其中一个调用 zero()，另一个线程调用 even()，最后一个线程调用odd()。正确的输出为 "0102"。

示例 2：
输入：n = 5
输出："0102030405"


class ZeroEvenOdd {
    private int n;
    private int i = 1;
    public ZeroEvenOdd(int n) {
        this.n = n;
    }

    // printNumber.accept(x) outputs "x", where x is an integer.
    public void zero(IntConsumer printNumber) throws InterruptedException {
        synchronized(this){
            while(true){
                while (i <= 2 *n && i%2 == 0) wait();
                if (i > 2*n) break;
                printNumber.accept(0);
                i++;
                notifyAll();
            }
        }
    }

    public void even(IntConsumer printNumber) throws InterruptedException {
        synchronized(this){
            while(true){
                while (i <= 2*n && (i%2 == 1 || (i%2 == 0 && i/2%2 == 1))) wait();
                if(i > 2*n) break;
                printNumber.accept(i/2);
                i++;
                notifyAll();
            }
        }
    }

    public void odd(IntConsumer printNumber) throws InterruptedException {
        synchronized(this){
            while(true){
                while (i <= 2*n &&(i%2 == 1 || (i%2 == 0 && i/2%2 == 0))) wait();
                if(i > 2*n) break;
                printNumber.accept(i/2);
                i++;
                notifyAll();
            }
        }
    }
}

```

## 3、多线程计算数组之和

描述：给定一个数组 如 int[] a = {1,2,3,4,5,6,7,8,9}，使用若干线程计算这个数组之和，同时计算提升效率

思路：使用Thread.join()函数，首先让k个线程去分别计算数组中某一部分，然后所有线程指向完毕后，计算总值

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class SumKThread {
    public static void main(String[] args) throws InterruptedException {
        int arrayLength = 10;
        int[] a = new int[arrayLength];//模拟待计算的数组
        int threadNum = 5; //启动k个线程同时执行
        int avg = arrayLength / threadNum;//每个线程计算多少范围的数相加
        int sum = 0;
        List<MyThread> list = new ArrayList<>();

        //给数组赋值
        for (int i = 0; i < a.length; i++) {
            a[i] = i;
        }
        System.out.println("arr="+ Arrays.toString(a));

        //循环创建并开启线程，并且把线程对象放入集合中
        for (int i = 0; i < threadNum; i++) {
            MyThread m = new MyThread(a, i * avg, (i + 1) * avg);
            m.start();
            list.add(m);
            //不能在这个循环中join，会变成串行。
        }

        /**
         * 将所有5个子线程都插队到主线程前面完成。
         * 确保只要主线程运行完了这个循环，5个子线程的计算任务就都做完了，
         * 这样最后输出的一定是正确的数值，不会遗漏任何一个线程的计算结果。
         *
         * 使用join方法底层是wait，等待上一个线程执行完毕 notify下一个线程
         */
        for (int i = 0; i < list.size(); i++) {
            list.get(i).join();
            System.out.println("第"+(i+1)+"个线程计算结果为"+list.get(i).getSum());
            sum += list.get(i).getSum();
        }
        System.out.println("所有数的和为：" + sum);
    }
}

class MyThread extends Thread {
    public int sum = 0; //公共的计算和
    private int[] a;
    private int start;//开始下标
    private int end;//结束下标
    public MyThread(int[] a, int start, int end) {
        this.a = a;
        this.start = start;
        this.end = end;
    }
    @Override
    public void run() {
        while (start < end) {
            sum += a[start];
            start++;
        }
    }
    public int getSum() {
        return sum;
    }
    public void setSum(int sum) {// 可删除
        this.sum = sum;
    }
}
```





## 快手一面编程

1找到一个链表的中间节点，2判断链表极端情况(监测环的情况 有的话抛出异常)

例如：链表中点

​	// case1： 

​	// 1->2->3->null

​	// 输出: val=2

​	// case 2:

​	// 1->2->3->4->null

​	// 输出: val=2 （输出顺序的第二个 不是下标）

```java
    import java.util.*;
    public class Main {
       public static class ListNode{
           int val;
           ListNode next;
           ListNode(int x){
               this.val = x;
           }
       }
        public static void main(String[] args) {
            // 在同一个类中可直接new ListNode，但是不在同个类中 需要用new InnerMian.ListNode(1)
            ListNode node = new ListNode(1);
            // ListNode node2 = new InnerMian.ListNode(1);//等价上面
            node.next=new ListNode(2);
            ListNode p = node.next;
            node.next.next = new ListNode(3);
            node.next.next.next = new ListNode(4);
            node.next.next.next.next = p;
            ListNode res = getMidNode(node);
            System.out.println(res.val);
        }
        //找出链表的中间节点
        public static ListNode getMidNode(ListNode head){
            if(head==null || head.next==null || head.next.next==null) return head;
            if(isReverNode(head)){throw new RuntimeException();}
            ListNode mid = head;
            ListNode fast = head;
            while(fast!=null && fast.next!=null &&fast.next.next!=null){
                mid = mid.next;
                fast = fast.next.next;
            }
            ListNode res = new ListNode(mid.val);
            return res;
        }

        public static boolean isReverNode(ListNode head){
            if(head == null) return false;
            Set<ListNode> hs = new HashSet<>();
            while(head != null){
                if(!hs.add(head)){
                    return true;
                }
                head = head.next;
            }
            return false;
        }
    }
```







## 大佬博客

1、https://github.com/yuanguangxin/LeetCode 算法+面试题

2、https://labuladong.gitbook.io/algo/  算法

3、https://mp.weixin.qq.com/s?__biz=MzAwNDA2OTM1Ng==&mid=2453142059&idx=1&sn=9569fe831f3b0a68a544f4c915594f33&chksm=8cf2d8a8bb8551be3b8a4b6c2f08f3030e21d41395f9eb3b44090da085014e61a9698d3dabd0&mpshare=1&srcid=&sharer_sharetime=1589242345838&sharer_shareid=f01765690bd5587b427a453b9150a644&from=groupmessage&scene=1&subscene=10000&clicktime=1589619015&enterid=1589619015&ascene=1&devicetype=android-28&version=27001139&nettype=WIFI&abtest_cookie=AAACAA%3D%3D&lang=zh_CN&exportkey=A3ZX3t05QGgXaY%2FWdv3yDYM%3D&pass_ticket=NOfPAGYuFlIgQxxhEH%2BFoui79vdA6v4%2Bcs5fVeduQuUEzJZtsQslI7lNB%2FXGPyWq&wx_header=1   敖丙知识点



```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        int len = nums.length;
        //1 大顶堆 降序 lamda表达式
        // PriorityQueue<Integer> queue = new PriorityQueue<>((a,b)->b-a); 
        // 放入全部元素构建大顶堆  时间稍长
        // for(int n : nums){
        //     queue.offer(n);
        // }
        // for(int i=0; i<k-1; i++){
        //     queue.poll();
        // }

        // 2 小顶堆 升序 lamda表达式
        // PriorityQueue<Integer> queue = new PriorityQueue<>((a,b)->a-b); 
        // // 放入前k个元素构建xiao顶堆  ****时间少****
        // for(int i=0; i<k; i++){
        //     queue.offer(nums[i]);
        // }
        // //取出小顶堆最小的去判断  如果没有比它大的就不比较交换了
        // for(int i=k; i<nums.length; i++){
        //     int t = queue.peek();
        //     if(t < nums[i]){
        //         queue.poll();
        //         queue.offer(nums[i]);
        //     }
        // }
        // return queue.peek();

        //3 利用快排分治思路
        // int left = 0, right = nums.length-1;
        //  int index = nums.length - k; // 第k大值对应的数组下标
        // while (true){
        //     int mid = getMid(nums, left, right);// 获得调整后每次位置
        //     if (index == mid) return nums[mid];// 如果mid和待求下标k相同 则找到
        //     else if (index > mid) left = mid + 1;// 左指针缩小范围
        //     else right = mid - 1;
        // }

    // // 3快排分治
    // public Random random = new Random();
    // public int getMid(int[] arr, int left, int right){
    //     // **随机设置pivot 枢纽值，否则对于有序数组会退化为O(n^2)
    //     if (left < right){
    //         int randomIndex = left + 1 + random.nextInt(right - left);// 加1保证不与自身交换
    //         int temp = arr[left];
    //         arr[left] = arr[randomIndex];
    //         arr[randomIndex] = temp;
    //     }
    //     int pivot = arr[left];// 设置基准值
    //     while (left < right){
    //         while (left < right && pivot <= arr[right]) right--;
    //         if (left < right) arr[left++] = arr[right]; // 在右边找到比基准值小的 与左边交换
    //         while (left < right && pivot > arr[left]) left++;
    //         if (left < right) arr[right--] = arr[left];
    //     }
    //     arr[left] = pivot; // 把基准值放在正确的位置left
    //     return left; // 返回基准值
    // }

        //4 手写堆 小顶堆
        int[] temp = new int[k]; //临时结果
        for(int i=0; i<k; i++){
            temp[i] = nums[i];
        }
        //默认对前k个数建堆 xiao顶堆  取出堆顶就是最xiao元素！！！
        for(int i=k/2; i>=0; i--){ // 注意 i--  ***
            adjustHeap(temp, i, k-1);
        }
        for(int i=k; i<len; i++){
            int t = temp[0]; //最小的
            if(nums[i] > t){ //把更大的数放进来 最后是k到最大的升序
                temp[0] = nums[i];
                adjustHeap(temp, 0, k-1);
            }
        }
        return temp[0];//返回第k大的  temp保存的第k大到最大的k个数
    }
//4 调整为最小堆
    public void adjustHeap(int[] arr, int parent, int len){
        int temp = arr[parent];
        int child = 2*parent+1;
        while(child <= len){
            if(child+1 <= len && arr[child+1]<arr[child]){ //**这里是最小堆写法
                child++;
            }
            if(arr[child] > temp){//**这里是最小堆写法
                break;
            }else{
                arr[parent] = arr[child];
                parent = child;
                child = 2*parent+1;
            }
        }
        arr[parent] = temp;
    }

}
```

