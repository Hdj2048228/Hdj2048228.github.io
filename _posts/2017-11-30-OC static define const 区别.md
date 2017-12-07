---
layout:     post
title:      OC中的static define const extern
subtitle:    
date:       2017-11-29
author:     hdj
header-img: img/bgs/girl-3.jpg
catalog: true
tags:
    - 算法
    - 各种算法
---



# 前言

 最近在[leetcode](https://leetcode.com/)上做算法题，记录一下心得

  （我的答案是用js写的）
##  由易至难

### 第一题：669. Trim a Binary Search Tree

 Given a binary search tree and the lowest and highest boundaries as L and R, trim the tree so that all its elements lies in [L, R] (R >= L). You might need to change the root of the tree, so the result should return the new root of the trimmed binary search tree.
  
   **Binary Search Tree 二叉搜索树。 二叉搜索树的特点是，小的值在左边，大的值在右边！**
    
  
#### 我的答案：
  
  
    
    var trimBST = function(root, L, R) {
             if(!root){
                return null;
              }
             if(root.val < L){
                return trimBST(root.right,L,R);
             }
             if(root.val >  R){
                   return trimBST(root.left,L,R);
             }
           root.left = trimBST(root.left,L,R);
           root.right = trimBST(root.right,L,R);
         
            return root;
    };
  

  
   
### 第二题：LeetCode 463. Island Perimeter
   
>    You are given a map in form of a two-dimensional integer grid where 1 represents land and 0 represents water. Grid 
cells are connected horizontally/vertically (not diagonally). The grid is completely surrounded by water, and there is 
exactly one island (i.e., one or more connected land cells). The island doesn't have "lakes" (water inside that isn't 
connected to the water around the island). One cell is a square with side length 1. The grid is rectangular, width and 
height don't exceed 100. Determine the perimeter of the island.

[[0,1,0,0],
 [1,1,1,0],
 [0,1,0,0],
 [1,1,0,0]]
 
![isLand](https://leetcode.com/static/images/problemset/island.png)

`Answer: 16
Explanation: The perimeter is the 16 yellow stripes in the image below:
`

**题目大意：**
给定一个二维地图，1表示陆地，0表示水域。单元格水平或者竖直相连（不含对角线）。地图完全被水域环绕，只包含一个岛屿（也就是说，一个或者多个相
连的陆地单元格）。岛屿没有湖泊（岛屿内部环绕的水域）。单元格是边长为1的正方形。地图是矩形，长宽不超过100。计算岛屿的周长。

#### 解题思路：
每一个陆地单元格的周长为4，当两单元格上下或者左右相邻时，令周长减2。


#### 解：
    var islandPerimeter = function(grid) {
        var islands = 0, edges = 0;
        for(let i=0;i<grid.length;i++) {
            for(let j=0;j<grid[i].length;j++){
                if(grid[i][j] === 1) {
                    islands++;
                    
                    if(i<grid.length-1 && grid[i+1][j] === 1) {
                        edges++;
                    }
                    
                    if(j<grid[i].length-1 && grid[i][j+1] === 1) {
                        edges++;
                    }
                }
            }
        }
        
        return islands*4 - edges*2;
    };