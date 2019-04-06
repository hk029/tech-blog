---
title: 元素排序(4)——凸包问题
date: 2016-05-10
categories: 
- 算法与数据结构
- Algorithms
tags: 
- 《常用算法和数据结构》
init: 1
---

> 本系列文章主要介绍常用的算法和数据结构的知识，记录的是《Algorithms I/II》课程的内容，采用的是“[算法（第4版）](https://book.douban.com/subject/19952400/)”这本红宝书作为学习教材的，语言是java。这本书的名气我不用多说吧？豆瓣评分9.4，我自己也认为是极好的学习算法的书籍。
>
> **通过这系列文章，可以加深对数据结构和基本算法的理解（个人认为比学校讲的清晰多了），并加深对java的理解。**

# 凸包问题

>凸包问题就是说，找到一个最小的凸多边形能圈住所有的点。

![image-20190302114058829](http://img.hksite.cn/2019-03-02-034103.png)

## 凸包问题的应用

- **运动规划：**找到从s到t的不经过多边形障碍的最短路径，最短路径肯定在凸包上

  ![](http://img.hksite.cn/2019-03-02-033901.png)



- **最远的一对：** 在平面上找一对相距最远的点，它们肯定在凸包上 

  ![image-20190302113949544](http://img.hksite.cn/2019-03-02-033949.png)

## 凸包的几何性质

- 可以通过逆时针遍历所有凸包顶点
- 凸包的顶点是关于p的极角的递增序列，而p拥有最小的y坐标。 

![](http://img.hksite.cn/2019-03-01-072720.jpg)

## Graham 扫描算法

1. **选择y坐标最小的p**
  - 如何确定最小y坐标？ 
     循环一遍
2. **对所有的点按关于p的 极角排序**
  - 如何对点进行极角排序？
  - 如何效率的排序？ 
    用MergeSort
3. **按顺序连接每个点，如果发现打破$p_{i−2}→p_{i−1}→p_i$ 打破了逆时针的原则，则舍弃$p_{i−1},p_i$。** 

  - 如何判断是否打破了逆时针原则ccw？
  - 如何解决退化问题（多个点在一条直线上） 

    通过计算（b-a）和（c-a）的叉乘就行了。
    如果area > 0 则是逆时针，
    如果area < 0 则是顺时针
    如果area = 0 则是共线 


![](http://img.hksite.cn/2019-03-01-072728.jpg)


### 代码

由于每次要判断最后的2个点是否是逆时针，毫无疑问使用栈结构是比较合适的
```java
public class Point2D 
{
   private final double x;
   private final double y;
   public Point2D(double x, double y)
   {
      this.x = x;
      this.y = y;
   } 
   ...
   public static int ccw(Point2D a, Point2D b, Point2D c)
   {
      double area2 = (b.x-a.x)*(c.y-a.y) - (b.y-a.y)*(c.x-a.x);
      if      (area2 < 0) return -1;  
// clockwise
      else if (area2 > 0) return +1;  
// counter-clockwise
      else                return  0;  
// collinear
   }
}
stack<Point2D> hull = new Stack<Point>();
Array.sort(p,Point2D.Y_ORDER);//find p
Array.sort(p,p[0],BY_POLAR_ORDER)//

hull.push(p[0]);
hull.push(p[1]);

for(i = 2;i <　N;i++)
{
    Point2D top = hull.pop();
    // 如果是小于0，说明是顺时针，继续Pop一个
    while(Point2D.ccw(hull.peek(),top,p[i]) <= 0)
        top = hull.pop();
    hull.push(top);
    hull.push(p[i]);

}
```
    