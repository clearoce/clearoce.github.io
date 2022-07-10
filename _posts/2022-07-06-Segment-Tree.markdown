---
layout: post
title:  Segment Tree
date:   2022-07-04 15:32:20 +0800
description:  单点、区间的修改、查询 ------ 张昆玮
img:    tree.png
tags:   [Algorithm, String]
author: # Add name author (optional)
---

{% highlight cpp %}
// HDU 1166 敌兵布阵
#include <bits/stdc++.h>
#define mem(a,b) memset(a,b,sizeof(a))
#define size 50000+10
using namespace std;

int T, q = 1, a, b, c;
char order[10];
int M, n;                           //You're about to give a value to M, and for totally M cases, you're about to give a value to n for each case, then n numbers followed
                                    //Then you begin to make some orders, and to acheive them, while there is an "End", it means the end of this test case
int tree[size << 2];                //You can imagine that it's just like a full-binary-tree that upside down, you can get that 1 + 2 + 4 + 8 + ... + 2n almost equal to 4n
int add[size << 2];

void maintain(int i) {                                      //Function to maintain the information of the nodes of the segment tree
    tree[i] = tree[i << 1] + tree[i << 1 | 1];                  //sum
    //tree[i] = max(tree[i << 1], tree[i << 1 | 1]);            //max   //if use this function and other codes should be changed, too
    //tree[i] = min(tree[i << 1], tree[i << 1 | 1]);            //min
}                                                               //Notice that don't use the keyword "tree" with using namespace __gnu_pbds, or it will conflict

void build() {                                              //Function to build the segment tree
    for(M = 1; M < n; M <<= 1);                                 //This semicolon here is necessary, this line is used for opening space just, thus you know that the zkw-segment tree is a full binary tree
    //while(M <= n) M <<= 1;                                    //This form is easier to understand, and they both ensure that M will be 2's mutiple
    for(int i = M + 1; i <= M + n; i ++)                        //Notice that the range of the loop are those n nodes after M
        scanf("%d", &tree[i]);                          //You can imagine that you're filling the base of the upside-down segment tree
    for(int i = M; i >= 1; i --)                                //Cause the base is what we made, what we'd do next is to update the information of the whole tree
        maintain(i);
}

int point_query(int x) {                                    //Function to query the information of a single point
    return tree[x + M];                                         //The range of x is [1, n] while the range of space is from 0 to a radom power of 2, really easy
}

void point_update(int x, int v) {                           //Function to add the value of v to the point x, obviously if you want to make a subtract, just let v become its opposite number
    for(tree[x += M] += v, x >>= 1; x; x >>= 1)                 // add or subtract, when x equals to 1 and it means the root/edge
        //for(tree[x += M] = v, x >>= 1; x; x >>= 1)            // replace
        maintain(x);
}

int interval_query(int left, int right) {                   //Function to query the sum of value of the range [left, right]
    int ans = 0;
    int lnum = 0, rnum = 0, now = 1;
    left = left + M - 1;
    right = right + M + 1;
    // Imagine you're trying to get the sum of a range [s, t], now you have to consider the position of the points in the range
    // For point s, if it's on the left, then of course it's direct father(the sum of s and its brother point) is totally in this range, while t is the same
    // Then you only need to do the same thing on s's father and t's father and repeat till the two points you work on are brothers
    for( ; left ^ right ^ 1; left >>= 1, right >>= 1) {         // The function of  "left ^ right ^ 1"  is to check if point left and right are brothers
        if(~ left & 1)       // if it's the left                // The function of  "XXX & 1"  is to check if the current point is on the left or right
            ans += tree[left ^ 1];
        if(right & 1)       // if it's the right
            ans += tree[right ^ 1];
    }
    for (; left; left >>= 1, right >>= 1) {
        ans += add[left] * lnum;
        ans += add[right] * rnum;
    }
    return ans;
}

void interval_update(int left, int right, int value) {      // Function to change the value of a whole range [left, right]
    int lnum = 0, rnum = 0, now = 1;
    //lnum 表示当前左端点走到的子树有多少个元素在修改区间内 (rnum与lnum对称)
    //now 表示当前端点走到的这一层有多少个叶子节点
    left = left + M - 1;
    right = right + M + 1;
    for( ; left ^ right ^ 1; left >>= 1, right >>= 1, now <<= 1) {
        tree[left] += value * lnum;
        tree[right] += value * rnum;
        if (~ left & 1) {
            tree[left ^ 1] += value * now;
            add[left ^ 1] += value;
            lnum += now;
        }

        if (right & 1) {
            tree[right ^ 1] += value * now;
            add[right ^ 1] += value;
            rnum += now;
        }
    }
    for( ; left; left >>= 1, right >>= 1) {
        tree[left] += value * lnum;
        tree[right] += value * rnum;
    }
}

int main() {
    scanf("%d", &T);
    while(T --) {
        scanf("%d", &n);
        mem(tree, 0);
        printf("Case %d:\n", q ++);
        build();
        while(scanf("%s", order)) {
            if(order[0] == 'E')
                break;
            else if(order[0] == 'Q') {
                scanf("%d %d", &a, &b);
                printf("%d\n", interval_query(a, b));
            }
            else if(order[0] == 'S') {
                scanf("%d %d", &a, &b);
                point_update(a, -b);
            }
            else if(order[0] == 'A') {
                scanf("%d %d", &a, &b);
                point_update(a, b);
            }
            else if(order[0] == 'P') {
                scanf("%d", &a);
                printf("%d\n", point_query(a));
            }
            else if(order[0] == 'U') {
                scanf("%d %d %d", &a, &b, &c);
                interval_update(a, b, c);
            }
            else if(order[0] == 'G') {
                for(int i = M + 1; i <= M + n; i ++)
                    printf("%d ", tree[i]);
                printf("\n");
            }
        }
    }
    return 0;
}
/*
Add - point +
Sub - point -
Query [ ] - query
Point - query
Update [ ] - change
Get [ ] - given points
End
*/

/*
1
10
1 2 3 4 5 6 7 8 9 10
*/

{% endhighlight %}
