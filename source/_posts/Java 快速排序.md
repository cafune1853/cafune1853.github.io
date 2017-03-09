---
title: JS复习小计
date: 2017-03-09 16:41:24
categories: 前端
tags: JS
---
<!-- more -->
```
 /**
  * @author 黄志伟
  */
 public class QuickSort {
     public static void main(String[] args) {
         int [] array = {49,38,65,97,76,13,13,27,4,8,2,3,56};
         quickSort(array, 0, array.length - 1);
         for (int i = 0; i < array.length; i++) {
             System.out.println(array[i]);
         }
     }
     /**
      * @param nums 要递归处理的数组
      * @param left 左边界 begin 0
      * @param right 右边界 end length-1
      */
     private static void quickSort(int[] nums,int left,int right){
         int x = (left+right)/2;
         int key = nums[x];
         int i = left;
         int j = right;
         int index = x;
         //如果长度为一或越界则不需要处理
         if(left >= right) return;
         while(i < j){
             //左边跳过>=key的一切值
             while(i < j && nums[j] >= key){
                 j--;
             }
             //右边跳过<key的一切值
             while(i < j && nums[i] < key){
                 i++;
             }
             //使用index来保存key值所在的数组下标
             //x的对应值只会不对换或对换一次
             if(i == x && i != j){
                 index = j;
             }
             if(j == x && i != j){
                 index = i;
             }
             //交换i、j的对应值
             int tmp = nums[i];
             nums[i] = nums[j];
             nums[j] = tmp;
         }
         //判断i == j（边界）处的值大于还是小于0，划分到不同的地方
         if(nums[i] < key){
            ++i;
         }
         int temp = nums[i];
         nums[i] = nums[index];
         nums[index] = temp;
         quickSort(nums,left,i-1);
         quickSort(nums,i+1,right);
     }
 }

 
```