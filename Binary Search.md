# 知识点

- 难点：边界索引的指向
- 二分查找必须用在 ==“有序数组”==
- 举例：二分查找找某一个数

![image-20200913181411902](C:\Users\yawei\AppData\Roaming\Typora\typora-user-images\image-20200913181411902.png)

- **写binary search问自己三个问题：**

    - 是找<u>一个</u> target？？？还是找<u>一串数的起点</u>，而这个起点就是 target

    - while终止的条件？是l < r 还是l <= r (l == r 时，区间是否有效？)
    - f(m)？
    - 怎样移动 left 和 right 指针，才能保持区间有效

- 总结：

<img src="C:\Users\yawei\AppData\Roaming\Typora\typora-user-images\image-20200913181742971.png" alt="image-20200913181742971" style="zoom: 33%;" />

