---
layout: post
title:  Aho Corasick Automaton
date:   2022-07-04 15:32:20 +0800
description: 多模匹配算法
img:    volcano.png # Add image post (optional)
tags:   [Algorithm, String]
author: # Add name author (optional)
---

# Manacher

{% highlight cpp %}
void manacher(char *p) {
    // 单字符循环节长度为1
    int ans = 1;
    for (int i = 1; p[i]; i++) {
        int s = i, t = i, len;
        while (p[t + 1] == p[i])
            ++t;
        i = t;
        while (p[s - 1] == p[t + 1])
            --s, ++t;
        if ((len = t - s + 1) > ans)
            ans = len;
    }
    printf("%d\n", ans);
}
{% endhighlight %}

# Trie

{% highlight cpp %}

// C++ Version From OI Wiki
struct trie {
    int nex[N][26], cnt;
    bool exist[N]; // 该结点结尾的字符串是否存在

    void insert(string s, int l) { // 插入字符串
        int p = 0;
        for (int i = 0; i < l; i++) {
            int c = s[i] - 'a';
            if (!nex[p][c])
                nex[p][c] = ++cnt; // 如果没有，就添加结点
            p = nex[p][c];
        }
        exist[p] = 1;
    }

    bool find(string s, int l) { // 查找字符串
        int p = 0;
        for (int i = 0; i < l; i++) {
            int c = s[i] - 'a';
            if (!nex[p][c])
                return 0;
            p = nex[p][c];
        }
        return exist[p];
    }
};

{% endhighlight %}

# KMP

{% highlight cpp %}

const int SIZE = 1e6 + 5;
char s[SIZE], t[SIZE];
int n, NEXT[SIZE];

void get_NEXT() {
    int i = 0, j = -1;
    NEXT[0] = -1;
    int len = strlen(t);
    while (i < len) {
        if (j == -1 || t[i] == t[j])
            NEXT[++ i] = ++ j;
        else
            j = NEXT[j];
    }
}
void kmp() {
    int ans = 0, i = 0, j = 0, lens = strlen(s), lent = strlen(t);
    while (i < lens) {
        if (j == -1 || s[i] == t[j])
            ++ i, ++ j;
        else j = NEXT[j];
        if (j == lent) ans ++;
    }
    printf("%d\n", ans);
}
{% endhighlight %}

# AC自动机

{% highlight cpp %}
#include<bits/stdc++.h>

using namespace std;
const int N = 1e6 + 7;

int trie[N][26], fail[N];
int cnt[N], n, num;
char s[N];
queue<int> q;

void insert(char *str) {
    int i = 1, cur = 0;
    while (str[i]) {
        if (!trie[cur][str[i] - 'a'])
            trie[cur][str[i] - 'a'] = ++num;
        cur = trie[cur][str[i] - 'a'];
        ++i;
    }
    ++cnt[cur];
}

void build() {
    for (int i = 0; i < 26; ++i)
        if (trie[0][i]) q.push(trie[0][i]);
    while (q.size()) {
        int cur = q.front();
        q.pop();
        for (int i = 0; i < 26; i++) {
            if (trie[cur][i]) {
                fail[trie[cur][i]] = trie[fail[cur]][i];
                q.push(trie[cur][i]);
            } else trie[cur][i] = trie[fail[cur]][i];
        }
    }
}

int query(char *str) {
    int i = 1, cur = 0, res = 0;
    while (str[i]) {
        cur = trie[cur][str[i] - 'a'];
        int j = cur;
        while (j && cnt[j] != -1) {
            res += cnt[j];
            cnt[j] = -1;
            j = fail[j];
        }
        ++i;
    }
    return res;
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) {
        scanf("%s", s + 1);
        insert(s);
    }
    scanf("%s", s + 1);
    build();
    if (query(s)) puts("YES");
    else puts("NO");
//    for (int i = 1; i <= num; i++) {
//        cout << i << ' ' << fail[i] << endl;
//    }
    return 0;
}

{% endhighlight %}
