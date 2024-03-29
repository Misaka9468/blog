# 洛谷题目
**P2678 跳石头** 

https://www.luogu.com.cn/problem/P2678

题意：至多删去一些石头，使得跳得最小步数最大。

**P1902 刺杀大使**

https://www.luogu.com.cn/problem/P1902

题意：从矩阵的第一行到最后一行，求最小血量通过【经过格子会扣血，但是只会扣最大的】

**P1314 聪明的质监员**

https://www.luogu.com.cn/problem/P1314

题意：em

**P1083 借教室**

https://www.luogu.com.cn/problem/P1083

题意：每天提供的教室有限，有n个请求，每个请求包含起始时间与终止时间，以及请求的教室个数。【区间的频繁加减】想知道是否能满足所有订单，如果不能满足应该最早到哪个请求（二分）

**P4343 [SHOI2015]自动刷题机**

https://www.luogu.com.cn/problem/P4343

题意：每天会写x行代码或者删y行代码。对于一个 OJ，存在某个固定的正整数长度 *n*，一旦自动刷题机在某秒结束时积累了大于等于 *n* 行的代码，它就会自动提交并 AC 此题，然后新建一个文件（即弃置之前的所有代码）并开始写下一题。二分枚举题数的最大值与最小值。

# 二分枚举相关题目特点

* 最大值最小：

	如P1902，让路途中所受伤最大值最小，【枚举出受伤数，判断能否走通】

* 最小值最大：
     	
    如P2678跳石头，让最小间距尽可能大
    
* 最值
     	
    实际上，二分枚举问题的关键是，有一些值都符合条件，但是我们要找到那个最值作为答案。
    
    用朴素枚举，得到结果。而我们用二分枚举，加速这一枚举。
    
    关键在于，首先你枚举的答案区间要具有单调性，也就是说我们要知道要怎样改变l和r的逼近。是l=mid还是r=mid-1. 其次，这类题的特征是有多个答案都可以满足条件，但是题目要求的是最值。
    
    比如说，P2678跳石头，我们枚举的是最短跳跃距离的最大值：如果选取该值能满足搬石头条件，那么我们会贪心地继续令l=mid，即加速枚举。
    
    又比如说P1902,同理我们枚举出最大值，如果可以走通那么r=mid，加速枚举尝试。
    
    又比如说P1314聪明的质检员，W值大反映出来的结果偏小，否则偏大，这也是一种单调性，也就是说我们枚举区间的选择会对结果质量判断有一个单调性特点。
    
    又比如说P1083借教室，枚举订单次序，也是找到临界。如果我们当前的订单不满足，则贪心寻找更小的订单次序。也是求一个求最小的。
    
    又比如说P4343自动刷题机，题目出现了最值，同样如果值选小了最后值比k大，值选大了最后值比k小，也是一个二分判断的条件。
    
    
 # 题解部分

 聪明的质检员，需要前缀和

 借教室，需要差分，我已经掌握啦

 自动AC机，总之写得不是很好，没看题解之前其实是没有把握到这个二分判断条件的精髓的，我只根据是否==k来判断judge，完全是因为有二分的标签硬套二分。

# 二分

总结：注意左闭右开的写法(左边永远是有效的,所以是mid+1)

以及直接利用c++的stl，其中lower_bound是大于等于的第一个



**最优雅的二分写法：**

* **求target**
* **升序求upper_bound  >=x**
* **升序求lower_bound  >x**

* **降序求upper_bound <x**
* **降序求lower_bound <=x**

**只需要掌握前三种，都是左闭右开、l < r 、l = mid+1   r = mid，结果返回l或r都可以因为二者相等**

**后面两个对应的是 前两者结果index-1**





```c++
#include <iostream>
int arr[20];

// 如果是求<x的最大值和<=x的最大值，只需要对应的>=x的-1与>x的-1就是

//查找二分 左闭右开 左边永远有效，右边永远无效
void binary_search(int left,int right,int num){
    int mid;
    while(left<right){
        mid = left+(right-left)/2;
        if(arr[mid]==num) {
            printf("work1: %d \n",mid);
            return ;
        }
        else if(arr[mid]>num)   right=mid;
        else left=mid+1;  
    }
}
//stl中, lower_bound寻找的是>=x的最小值  而upper_bound寻找的是>x的最小值
void my_lower_bound(int left,int right,int num){
    int mid;
    while(left<right){
        mid = left+(right-left)/2;
        if(arr[mid]<num) left = mid+1;
        else right=mid;
    }
    printf("lower_bound: %d \n",left);
}


void my_upper_bound(int left,int right,int num){
    int mid;
    while(left<right){
        mid = left+(right-left)/2;
        if(arr[mid]<=num) left = mid+1;
        else right=mid;
    }
    printf("upper_bound: %d \n",left);
}


int main(){
    for(int i = 0;i<20;i++){
        arr[i]=2*i;
    }
    
    binary_search(0,20,12);
    my_lower_bound(0,20,12); // >=x 的最小值
    my_upper_bound(0,20,12); // >x 的最小值

    return 0;
}

```