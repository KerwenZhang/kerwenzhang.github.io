---
layout: post
title: "重构与模式读书笔记"
date:   2017-03-02 14:50:00 
categories: "Others"
catalog: true
tags: 
    - Others
---



## 重构

重构就是一种“保持行为的转换”， 是一种对软件内部结构的改善， 目的是在不改变软件的可见行为的情况下， 使其更容易理解， 修改成本更低。  
重构的过程包括<b>去除重复</b>、<b>简化复杂逻辑</b>和<b>澄清模糊的代码</b>。 重构时， 需要对代码无情的针砭， 以改进其设计。 这种改进可能很小， 小到只是改变一个变量名； 也可能很大， 大到合并两个类层次。  
重构最好持续而不是分阶段的进行。  只要看到代码需要改善， 就改善它。  

<b>代码本身不清晰， 就说明存在坏味， 需要通过重构清除， 而不是用注释来掩饰。</b>  

好的代码：  
读起来像自然语言  
将重要代码与分散注意力的代码分离开来  

每个模式都是一个由三部分组成的规则， 它表达的是某一环境、一个问题以及解决问题的方案之间的关系。  

## 代码坏味

1. 重复代码  
2. 方法过长  
3. 条件逻辑太复杂  
4. 基本类型迷恋  
5. 不恰当的暴露  
6. 解决方案蔓延  
7. 异曲同工的类  
8. 冗赘类  
9. 类过大  
10. 分支语句  
11. 组合爆炸  
12. 怪异解决方案  

## 创建

#### 用Creation Method替换构造函数

构造函数本身无法有效和高效的表达意图。 拥有的构造函数越多， 程序员就越容易选择错误的构造函数。  
Creation Method就是类中的一个静态或者非静态的负责实例化类的新实例的方法。  
优点：  
比构造函数能更好的表达所创建的实例的种类  
避免了构造函数的局限， 比如两个构造函数的参数数目和类型不能相同  
更容易发现无用的创建代码  

当创建一个对象的知识散布在多个类中， 说明出现了创建蔓延问题： 将创建的职责放在了不应该承担对象创建任务的类中。 创建蔓延是解决方案蔓延坏味的一种， 往往是由于之前的设计问题所引起的。  

## 简化

#### 组合方法

将方法分解成命名良好的、处在细节的同一层面上的行为模块， 以此来简化方法。  
当把一个方法分解成一组行为的时候， 要保证这些行为在细节的相似层面上。  

#### 用Strategy替换条件逻辑

复杂的条件逻辑是最常导致复杂度上升的地点之一。  
条件逻辑可能会在类中产生许多仅仅用来处理算法的特殊变体的小方法， 从而增加了类的复杂度。 在这种情况下， 把算法的每个变体搬移到新类或子类是更好的选择。  

#### 将装饰功能搬移到Decorator

#### 用State替换状态改变条件语句
