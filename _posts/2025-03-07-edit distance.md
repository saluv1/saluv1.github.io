--- 
layout: post
title: "review of edit distance"
tags: [dp, ps]
comments: true
---

The concept of edit distance is for how we can quantify similarity of 2 different strings.

<img width="578" alt="image" src="https://github.com/user-attachments/assets/ff9999eb-1da6-4058-b644-69c2e9ce027b" />[^1]

first, we need fill up our dp table with number of calculation for changing s1->NULL and NULL->s2. (For sure, s2->NULL, NULL->s1 is okay.)

we have 3 operations(insert, delete, modify).
In this problem, insert and delete have in common in terms of dealing with one character.
and The similarity check is always relative concept!!( To make same s1 and s2 ,let A is different factor in this situation, we can add A to s1 or delete A from s2.)

so, we have relation between s1, s2. dp[i][j]=min(dp[i-1][j]+1,dp[i][j-1]+1,dp[i-1][j-1]+c)
The cost c is equal to 0 ,if s1[i]==s2[j], or 1 , if s1[i]!=s2[j].

### Example
Baekjoon 30375 Edit distance on table

using above relation, we can think this problem as 0-1 bfs in (2000,100,100) space.
As the length changes, the minumum val of (x,y) changes. and this representation makes 1-1 corresponding between space and min-val in each situation.
The main idea is changing the string obtained from table because the string T is constant. 
so, we can 3 operations to the string obtained from table. and min(brr[l][x][y],1<=x<=H,1<=y<=W) is our goal.
```c++
#include <bits/stdc++.h>
using namespace std;
#define F first 
#define S second

char arr[104][104];
int brr[2009][104][104];
int H,W;
int l;
string s;
int dx[5]={1,-1,0,0};
int dy[5]={0,0,1,-1};
int safe(int x,int y)
{
    return x>=1&&x<=H&&y>=1&&y<=W;
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    cin>>H>>W;
    for(int i=1;i<=H;i++)
    {
        string t;
        cin>>t;
        for(int j=1;j<=W;j++)
        arr[i][j]=(char)t[j-1];
    }
    cin>>s;
    l=s.length();
    for(int i=1;i<=l;i++)
    {
        for(int x=1;x<=H;x++)
        {
            for(int y=1;y<=W;y++)
            brr[i][x][y]=2000000009;
        }
    }
    deque<pair<int,pair<int,int>>> dq;
    for(int i=1;i<=H;i++)
    {
        for(int j=1;j<=W;j++)
        {
            dq.push_back({0,{i,j}});
        }
    }
    while(!dq.empty())
    {
        int curl=dq.front().F;
        int curx=dq.front().S.F;
        int cury=dq.front().S.S;
        dq.pop_front();
        if(curl>=l)
        continue;
        for(int k=0;k<4;k++)
        {
            int nx=curx+dx[k];
            int ny=cury+dy[k];
            if(safe(nx,ny))
            {
                if(arr[nx][ny]==s[curl])
                {
                    if(brr[curl+1][nx][ny]>brr[curl][curx][cury])
                    {
                        dq.push_front({curl+1,{nx,ny}});
                        brr[curl+1][nx][ny]=brr[curl][curx][cury];
                    }
                }
                else
                {
                    if(brr[curl+1][curx][cury]>brr[curl][curx][cury]+1)
                    {
                        dq.push_back({curl+1,{curx,cury}});
                        brr[curl+1][curx][cury]=brr[curl][curx][cury]+1;
                    }
                    if(brr[curl+1][nx][ny]>brr[curl][curx][cury]+1)
                    {
                        dq.push_back({curl+1,{nx,ny}});
                        brr[curl+1][nx][ny]=brr[curl][curx][cury]+1;
                    }
                    if(brr[curl][nx][ny]>brr[curl][curx][cury]+1)
                    {
                        dq.push_back({curl,{nx,ny}});
                        brr[curl][nx][ny]=brr[curl][curx][cury]+1;
                    }
                }
            }
        }
    }
    int min1=2000000009;
    for(int i=1;i<=H;i++)
    {
        for(int j=1;j<=W;j++)
        {
            if(min1>brr[l][i][j])
            min1=brr[l][i][j];
        }
    }
    cout<<min1;
    return 0;
}
```
[^1]: <(https://velog.io/@49crehbgr/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98Algorithm-%ED%8E%B8%EC%A7%91%EA%B1%B0%EB%A6%AC-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98)>
