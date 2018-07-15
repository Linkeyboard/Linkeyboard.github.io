---
title: 树链剖分入门
date: 2018-07-13 22:04:39
tags: 树链剖分
categories: ACM
---
## 树链剖分入门

把树划分为轻链和重链，边标号，哈希到线段树上。

- dep[v]:v所在层次的深度(根的深度为1)
- son[v]:v的重儿子节点,size最大的子节点
- w[v]:v与其父亲节点的连边在线段树中的位置
- top[v]:v所在的重链的顶端节点
- size[v]:v包含的子树节点
- 重链:与重儿子连起来的边

<!-- more -->
---
- 第一次dfs可以求出son[v],dep[v],size[v]
- 第二次dfs
 * top[son[v]]=top[v];
 * 重儿子:w[son[v]]=totw+1;(直接递归下去，重链哈希为连续区间,totw是最后一个标号)
 * 轻儿子:w[轻儿子]=totw+1;
 
---
查询
```C++
f1=top[u],f2=top[v];
while(f1==f2){
   if(dep[f1]<dep[f2]){
      swap(f1,f2);
      swap(u,v);
   }
   tmp=max(tmp,query(1,w[f1],w[u]));
   u=fa[f1];f1=top[u];
}
if(u==v) return tmp;
if(dep[u]>dep[v]){
    swap(u,v);
}
return max(tmp,query(1,w[son[v]],w[u]));

```
---
更改某个边值，查询两点之间路径经过的最大边
```C++
#include <iostream>
#include <cstdio>
#include <string>
#include <cstring>

using namespace std;
const int MAXN=10000+10;
const int INF=0x3f3f3f3f;

struct cnode{
    int next;
    int to;
    int cost;
}node[MAXN*2];
int head[MAXN*2],tot;
int n;
int fa[MAXN],dep[MAXN],son[MAXN],top[MAXN],siz[MAXN],vis[MAXN];
int totw,w[MAXN];

void init(){
    tot=0;
    memset(head,-1,sizeof(head));
    memset(vis,0,sizeof(vis));
    memset(fa,0,sizeof(fa));
    memset(son,0,sizeof(son));
}
void init2(){
    memset(vis,0,sizeof(vis));
    top[1]=1;
    totw=0;
}
void show(){
    for(int i=1;i<=n;i++){
        int pos=head[i];
        printf("(%d) ",i);
        printf("fa:%d son:%d siz:%d dep:%d top:%d w:%d",fa[i],son[i],siz[i],dep[i],top[i],w[i]);
        for(;pos!=-1;pos=node[pos].next){
            //printf("%d ",node[pos].to);
        }
        printf("\n");
    }
}
void dfs1(int u,int d){
   vis[u]=1;
   siz[u]=1;
   int pos=head[u];
   int maxsize=0;
   int tmpson;
   for(;pos!=-1;pos=node[pos].next){
       int v=node[pos].to;
       if(!vis[v]){
           dfs1(v,d+1);
           siz[u]+=siz[v];
           if(siz[v]>maxsize){
               maxsize=siz[v];
               tmpson=v;
           }
           fa[v]=u;
       }
   }
   if(maxsize){
       son[u]=tmpson;
   }
   dep[u]=d;
}
void dfs2(int u){
    int pos=head[u];
    if(son[u]){
        top[son[u]]=top[u];
        w[son[u]]=++totw;
        dfs2(son[u]);
    }
    for(;pos!=-1;pos=node[pos].next){
        int v=node[pos].to; 
        if(v!=fa[u]&&v!=son[u]){
            top[v]=v;
            w[v]=++totw;
            dfs2(v);
        }
    }
}

int val[MAXN];
void updateval(){
    for(int i=1;i<=n;i++){
        int pos=head[i];
        for(;pos!=-1;pos=node[pos].next){
            int v=node[pos].to;
            if(v!=fa[i]){
                val[w[v]]=node[pos].cost;
            }
        }
    }
}

struct Treenode{
    int l,r;
    int maxx;
}Tree[MAXN*8];

void push_up(int u){
    int lson=u<<1;
    int rson=lson|1;
    Tree[u].maxx=max(Tree[lson].maxx,Tree[rson].maxx);
}
void build(int u,int l,int r){
    Tree[u].l=l;
    Tree[u].r=r;
    Tree[u].maxx=-INF;
    if(l==r){
       Tree[u].maxx=val[l];
       return; 
    }
    int mid=(l+r)>>1;
    build((u<<1),l,mid);
    build((u<<1)|1,mid+1,r);
    push_up(u);
}
int query(int u,int l,int r){
    if(Tree[u].l==l&&Tree[u].r==r){
        return Tree[u].maxx;
    }
    int mid=(Tree[u].l+Tree[u].r)>>1;
    int lson=u<<1;
    int rson=lson|1;
    if(l>mid){
        return query(rson,l,r);
    }else if(r<=mid){
        return query(lson,l,r);
    }else{
        return max(query(lson,l,mid),query(rson,mid+1,r));
    }
}
void update(int u,int k,int c){
    if(Tree[u].l==k&&Tree[u].r==k){
        Tree[u].maxx=c;
        return;
    }
    int mid=(Tree[u].l+Tree[u].r)>>1;
    if(k<=mid) update(u<<1,k,c);
    else update((u<<1)|1,k,c);
    push_up(u);
}
int findmax(int u,int v){
    int f1=top[u],f2=top[v];
    int tmp=-INF;
    while(f1!=f2){
        if(dep[f1]>dep[f2]){
            tmp=max(tmp,query(1,w[f1],w[u]));
            u=fa[f1];
        }else{
            tmp=max(tmp,query(1,w[f2],w[v]));
            v=fa[f2];
        }
        f1=top[u];
        f2=top[v];
    }
    if(u==v){
        return tmp; 
    }
    if(dep[u]>dep[v]){
        swap(u,v);
        swap(f1,f2);
    }
    return max(tmp,query(1,w[son[u]],w[v]));
}
int main(){
    freopen("in.txt","r",stdin);
    int T;cin>>T;
    while(T--){
        init();
        scanf("%d", &n);
        int a,b,c;
        for(int i=0;i<n-1;i++){
            scanf("%d%d%d",&a,&b,&c);
            node[tot].to=b;
            node[tot].next=head[a];
            node[tot].cost=c;
            head[a]=tot++;
            node[tot].to=a;
            node[tot].next=head[b];
            node[tot].cost=c;
            head[b]=tot++;
        }
        dfs1(1,1);
        init2();
        dfs2(1);
        updateval();
        build(1,1,n);
        //show();
        char str[10];
        while(scanf("%s",str)!=EOF){
           if(str[0]=='Q'){
               scanf("%d%d",&a,&b);
               printf("%d\n",findmax(a,b));
           }else if(str[0]=='D'){
               break;
           }else{
               int k,tv;
               scanf("%d%d",&k,&tv);
               int ta=node[2*k-1].to;
               int tb=node[2*k-2].to;
               if(fa[ta]!=tb) swap(ta,tb);
               update(1,w[ta],tv);
           }
        }
    }
    return 0;
}
```
