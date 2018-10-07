---
layout: post
title: [Coursera] - Introduction to Big Data
categories: Coursera BigData
description: 最近在Coursera上完成了一门课的学习，在此对此课做一些记录。
keywords: Coursera BigData

---

## [Coursera] - Introduction to Big Data

## 关于这门课

最近在Coursera上完成了一门课，[Introduction to Big Data](https://www.coursera.org/learn/big-data-introduction/home/welcome)，是加州大学圣地亚哥分校(UCSD)计算机系开设的一门大数据入门课程。这门课属于Coursera上的一个Specification，[Big Data](https://www.coursera.org/specializations/big-data)，是这个Specification的第一门课，共6门课。

> "Big Data Specification"
>
>  
>
> Introduction to Big Data - 3 weeks - 6 h/week
>
> Big Data Modeling and Management Systems - 6weeks - 3 h/week
>
> Big Data Integration and Processing - 6weeks - 5 h/week
>
> Machine Learning with  Big Data - 5 weeks - 5h/week
>
> Graph Analytics for Big Data - 4 weeks - 5 h/week
>
> Big Data - Capstone Project 6 weeks - 5h/week
>
>  
>
> (3 + 6 + 6 + 5 + 4 + 6) = 30 weeks



第一门课只有三周，是个导论性质的Introduction性的课，前两周主要介绍了Big Data中的一种重要的基础概念，和一些foundation Components。第三周是Get started with Hadoop。



## 一些笔记

**Week One**

Key Points:

* What launched the big data era?
  * The growing data torrent
  * Cloud Computing 
  * both these 2 thing combined together and make it possible to do big data analysis

* Data Integration
  * Reduce data complexity
  * Increase data availability
  * Unify your data system
* Workflow with Big Data
  * Big data --> Better models  -->  Higher precision



**Week Two**

Key Points:

* How to describe big data?

  We use V s:
  * Volume == Size
    * Challenge:  Storage + Access + Processing(Performance)

  * Velocity == Complexity & Speed, real-time processing
    * Batch processing
      * Collect  data -> clean data -> feed in chunks -> wait -> act
    * Real time processing
      * Instantly capture streaming data -> feed real time to machines -> process real time -> act
      * Prevent missed  opportunities
  * Variety == increasing differences, type
  * Veracity == quality - vary greatly
    * Define the  quality of data
    * Uncertain
  * Valence == Connectedness
    * Connection increase -> challenges -> complex data exploration algorithm
  * Value

* Define the question

  * Big data -> Analysis question -> insight ==> -<
  * Strategy
    * Aim
    * Policy
    * Plan
    * Action
  * P's
    * People
    * Purpose
    * Process
    * Platforms
    * Programmability
    * ==> data product
  * Process
    * Define the problem -> access the situation -> define the goals and criteria -> formulate the question

* Data Analysis 

  * Acquire Data
  * Explore Data -> data understanding
  * Prepare - Pre-Processing - filter - clean - sub-setting - data quality
  * Analyze 
    * Classification
    * Clustering
    * Graph Analytics
    * Regression
    * Association Analysis
  * Report - Purpose
  * Act



**Week Three**

Key Points:

* What is a distributed file system?

* Scalable computing over the internet

* Programming Models for Big Data

* 1. Abstraction -> Runtime libraries + Programming language

  2. Requirements

  3. 1. - Support Big Data Operations

     2. 1. Split volumes of data 
        2. Access data fast
        3. Distribute computations to nodes

     3. - Handle Fault Tolerance

     4. 1. Replicate data partitions
        2. Recover files when needed

     5. - Enable adding more racks - Enable scale out

     6. - Optimized for specific data types

  4. One Instance of Programming Models: MapReduce

  5. And Hadoop is one implement of MapReduce

  6. Map -> apply, Reduce -> summarize

![bigdata-1](https://res.cloudinary.com/lvxiaoxin96/image/upload/v1538889116/bigdata-1.png)

![bigdata-2](https://res.cloudinary.com/lvxiaoxin96/image/upload/v1538889116/bigdata-2.png)



## A Simulator for MapReduce

![demo](https://res.cloudinary.com/lvxiaoxin96/image/upload/v1538889251/mapreduce.png)



