---
layout: post
title: AIStats 2017文章精读（四）
categories: 读论文
---

我们在这里对[AIStats 2017](http://www.aistats.org/)文章Fast Bayesian Optimization of Machine Learning Hyper-parameters on Large Datasets进行一个简单的分析解读。

[全文PDF](http://proceedings.mlr.press/v54/klein17a/klein17a.pdf)

[文章附加信息](http://proceedings.mlr.press/v54/klein17a/klein17a-supp.pdf)

这篇文章的作者群是一队来自德国的学者，分别来自University of Freiburg和Max Planck Institute for Intelligent Systems。文章讨论了一个很实际的问题，那就是如何对一个机器学习算法进行自动调参数。文章针对这几年逐渐火热起来的Bayesian Optimization，开发了一个快速的、并且能够在大规模数据上运行的算法。

传统的机器学习算法有很多所谓叫超参数（Hyper-parameter）需要设置。而这些超参数往往对最后的算法性能有至关重要的影响。在一般的情况下，如何寻找最佳的超参数组合则成为了很多专家的必要“技能”。而对于机器算法本身而言，取决于算法的复杂程度，有时候寻找一组合适的超参数意味着非常大的计算代价。

这篇文章讨论了这么一个思路，那就是，既然在全局数据上对算法进行评估计算代价太大，可能对于直接调参过于困难，那能否在一个数据的子集上进行调参，然后把获得的结果看能否运用到更大一点的子集上，最终运用到全集上。

这里，我们来回顾一下Bayesian Optimization的简单原理。首先，我们有一个“黑盒”的目标函数。我们的任务是找到这个目标函数最小值所对应的参数值（超参数）。这里，我们需要一个这个目标函数的先验分布，同时我们还需要一个所谓的Acquisition Function，用来衡量在某个点的参数值的Utility。有了这些设置，一个通常情况下的Bayesian Optimization的步骤是这样的：

1.  用数值优化的方法在Acquisition Function的帮助下，找到下一个Promising的点。
1.  带入这个Promising的点到黑盒函数中，得到当前的值，并且更新现在的数据集。
1.  更新目标函数的先验分布以及Acquisition Function。

通常情况下，Bayesian Optimization的研究喜欢用Gaussian Processes（GP）来做目标函数的先验分布。这里就不复述具体的设置了。而对于Acquisition Function，这里有好几种可能性，比如文章举了Expected Improvement（EI）、Upper Confidence Bound（UCB）、Entropy Search（ES）等的例子。这篇文章使用了EI和ES。

这篇文章提出的方法的思路的第一步，是把原来那个黑盒函数增加了一个参数，也就是除了原来的超参数以外，增加了一个数据集大小的参数。这个参数是按照比例（从0到1的一个值）来调整相对的数据集大小的。那么，如何应用这个参数呢？这里的技巧是，在GP里，需要有一个Kernel的设置。原本这个Kernel是定义在两组超参数之间的。那么，在这篇文章里，这个Kernel就定义在“超参数和数据集大小”这个Pair与另外一个Pair之间。于是，这里就能够通过已经经典的设置得到需要的效果。文章还提出了一个新的Acquisition Function用来平衡Information Gain和Cost。

文章用SVM在MNIST做了实验，还用CNN在CIFAR-10以及SVHN上做了实验，以及还用ResNet在CIFAR-10上做了实验。总体上说，提出来的算法比之前的方法快10倍到100倍。并且，相比较的一些其他算法（比如一开始就在全集上进行计算的方法）都没法完成实验。

这篇文章的基本思路和相关研究值得机器学习实践者学习。
