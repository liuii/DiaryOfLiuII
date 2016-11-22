# SLAM资料汇总
> 资料来源：[学习SLAM需要哪些预备知识？](https://www.zhihu.com/question/35186064)  

- 入门的话首推《probabilistic robotics》。  
- 宝典[`Multiple View Geometry in Computer Vision Second Edition`](http://www.robots.ox.ac.uk/~vgg/hzbook/)，这本书基本涵盖了Vision-based SLAM这个领域的全部理论基础！  
- 所以另一个基础理论是`Sparse Matrix`，这是大型稀疏矩阵处理的一般办法。可以参考Dr. Tim Davis的课件：[`TIM DAVIS`](http://faculty.cse.tamu.edu/davis/welcome.html)。  
- 针对SLAM问题，最常用的least square算法是Sparse Levenberg Marquardt algorithm，这里有一份开源的代码以及具体实现的paper：[`sparseLM : Sparse Levenberg-Marquardt nonlinear least squares in C/C++`](http://users.ics.forth.gr/~lourakis/sparseLM/)。  
- 最常用的机器人框架是[`ROS`](http://www.ros.org/)  
- 另一个开源工具集是[`OpenSLAM`](https://www.openslam.org/)，其中的g2o是目前最流行的graph optimization的实现工具。  
- [`SLAM blog`](http://www.cnblogs.com/gaoxiang12/p/3695962.html)  
- 入门教程：《SLAM for dummies》。