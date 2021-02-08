# 目录

[TOC]

#### 稳定排序：排序前后不改变两个相同元素的位置

#### 不稳定排序：排序前后会改变两个相同元素的位置

#### 原地排序：指排序过程中不会申请额外的空间来辅助排序

#### 非原地排序：指排序过程中要使用辅助数组来完成排序

#### 时间复杂度：指完成一个算法所需要的时间

#### 空间复杂度： 指完成一个算法所需要的内存空间

### 冒泡排序

```java
public class BubbleSort {
    public static void bubbleSort(int[] arr){
        for(int i = 0;i < arr.length;i++) {
            boolean flag = true;
            for(int j = 0;j < arr.length-i-1;j++) {
                int temp;
                if(arr[j] > arr[j+1]) {
                    flag = false
                    temp = arr[j];
                    arr[j] = arr[j+1];
                    arr[j+1] = temp;
                }
                if(flag) break;     //如果第一次没有任何元素发生交换，则该数组已经有序，退出循环
            }
        }
    }
```

<font style="color:purple;font-size:20px">时间复杂度为：O(n2) 空间复杂度：O(1)  非稳定排序  原地排序</font>

----

### 选择排序

核心思想：从第一个元素开始遍历，找到最小的元素放在数组的第一个位置，然后第二遍从第二个元素开始遍历，找到最小的元素放在数组的第二个位置上，直到倒数第二个元素即完成排序

```java
public class SelectSort {
    public static void selectSort(int[] arr) {
        for (int i = 0; i < arr.length - 1; i++) {
            int min = i;
            for (int j = i + 1; j < arr.length; j++) {
                if (arr[min] > arr[j]) {
                    min = j;
                }
            }
            if (min != i) {
                int temp = arr[i];
                arr[i] = arr[min];
                arr[min] = temp;
            }
        }
    }
}
```

<font style="color:purple;font-size:20px">时间复杂度为：O(n2) 空间复杂度：O(1)  非稳定排序  原地排序</font>

----

### 插入排序

核心思想：将数组分为两部分。左边为有序的部分，而右边为无序的部分。第一次将第一个元素和后面的元素分开，开始从无序的部分开始遍历，即从数组的第二的元素开始遍历(从右向左遍历)，若当前元素小于前一个元素则交换。然后从数组的第三个元素开始遍历，倒数第一个元素结束

```java
public class InsertSort {
    public static void insertSort(int[] arr){
        for(int i = 1;i < arr.length;i++) {
            int temp = arr[i];
            for(int j = i;j > 0;j--) {
                if(arr[j] < arr[j-1]) {
                    arr[j] = arr[j-1];
                    arr[j-1] = temp;
                }
                else {
                    break;
                }
            }
        }
    }
```

<font style="color:purple;font-size:20px">时间复杂度为：O(n2) 空间复杂度：O(1) 稳定排序  原地排序</font>

-----

### 希尔排序

```java
public class ShellSort {
    public static void shellSort(int[] arr){
        for(int h = arr.length/2;h > 0;h /= 2) {
            for(int i = h;i < arr.length;i++) {
                int temp = arr[i];
                int k;
                for(k = i-h;k >= 0 && temp < arr[k];k -= h) {
                    arr[k+h] = arr[k];
                }
                arr[k+h] = temp;
            }
        }
    }
```

```c++
void ShellSort(int arr[],int N){
    for(int h = N/2;h > 0;h /= 2){
        for(int i = h;i < N;i++){
            int k;
            for(k = i - h;k >= 0 && arr[k+h] < arr[k];k -= h){
                int temp = arr[k];
                arr[k] = arr[k+h];
                arr[k+h] = temp;
            }
        }
    }
}
```

----

<font style="color:purple;font-size:20px">时间复杂度为：O(nlogn) 空间复杂度：O(1)  非稳定排序  原地排序</font>

### 归并排序

```java
public class MergeSort {
    public static int[] mergeSort(int[] arr, int left, int right){
        if(left < right){
            int mid = (right + left)/2;
            arr = mergeSort(arr, left, mid);
            arr = mergeSort(arr, mid+1, right);
            merge(arr, left, mid,right);
        }
        return arr;
    }
    public static void merge(int[] arr,int left,int mid,int right){
        int[] temp = new int[right-left+1];
        int i = left;
        int j = mid + 1;
        int k = 0;
        while (i <= mid&&j <= right) {
            if (arr[i] <= arr[j]) {
                temp[k++] = arr[i++];
            } else {
                temp[k++] = arr[j++];
            }
        }
        while(i <= mid) temp[k++] = arr[i++];
        while(j <=  right) temp[k++] = arr[j++];
        for(i = 0;i < k;i++){
            arr[left++] = temp[i];
        }
    }
```

<font style="color:purple;font-size:20px">时间复杂度为：O(nlogn) 空间复杂度：O(n) 稳定排序 非原地排序</font>

-----

### 快速排序

```java
public class QuickSort {
    public static int[] quickSort(int[] arr, int start, int end) {
        int pivot = arr[start];        
        int i = start;        
        int j = end;        
        while (i < j) {            
            while ((i < j) && (arr[j] > pivot)) {                
                j--;            
            }            
            while ((i < j) && (arr[i] < pivot)) {                
                i++;            
            }            
            if ((arr[i] == arr[j]) && (i < j)) {                
                i++;            
            } else {                
                int temp = arr[i];                
                arr[i] = arr[j];                
                arr[j] = temp;            
            }        
        }        
        if (i-1 > start) arr = quickSort(arr,start,i-1);
        if (j+1 < end) arr = quickSort(arr,j+1,end);
        return arr;    
    }    
```

<font style="color:purple;font-size:20px">时间复杂度为：O(nlogn) 空间复杂度：O(logn) 非稳定排序 原地排序</font>

----

