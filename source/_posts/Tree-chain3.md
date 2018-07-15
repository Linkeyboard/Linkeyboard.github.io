---
title: 树链剖分（区间更新，单点值）
date: 2018-07-15 16:43:09
tags: 树链剖分
categories: ACM
---
区间增加或者减少一个值，求单点的值
点带权转化为边带权，增加一个虚根
`线段树push_down`
<!-- more -->
---
```C++
#include <iostream>
#include <cstdio>
#include <string>
#include <cstring>

using namespace std;
const int MAXN=50000+10;
const int INF=0x3f3f3f3f;
int n,m,p;
struct cnode{
    int next;
    int to;
}node[MAXN*2];
int head[MAXN*2],tot;
int fa[MAXN],dep[MAXN],son[MAXN],top[MAXN],siz[MAXN],vis[MAXN];
int totw,w[MAXN];
int enemy[MAXN];

void init(){
    tot=0;
    memset(head,-1,sizeof(head));
    memset(vis,0,sizeof(vis));
    memset(fa,0,sizeof(fa));
    memset(son,0,sizeof(son));
    memset(dep,0,sizeof(dep));
    memset(enemy,0,sizeof(enemy));
    memset(w,0,sizeof(w));
    memset(siz,0,sizeof(siz));
    memset(top,0,sizeof(top));
}
void init2(){
    memset(vis,0,sizeof(vis));
    totw=0;
    top[n+1]=n+1;//初始化虚根top
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
void show(){
    for(int i=1;i<=n+1;i++){
        int pos=head[i];
        printf("(%d) ",i);
        for(;pos!=-1;pos=node[pos].next){
            //printf("%d ",node[pos].to);
        }
        printf("size:%d son:%d fa:%d dep:%d top:%d w:%d",siz[i],son[i],fa[i],dep[i],top[i],w[i]);
        printf("\n");
    }

}
int val[MAXN];
void updateval(){
    for(int i=1;i<=n;i++){
        val[w[i]]=enemy[i];
    }
}
struct Treenode{
    int l,r;
    int sum;
    int add;
}Tree[MAXN*4];
void push_up(int u){
    int lson=u<<1;
    int rson=lson|1;
    Tree[u].sum=Tree[lson].sum+Tree[rson].sum;
}
void build(int u,int l,int r){
    Tree[u].l=l;
    Tree[u].r=r;
    Tree[u].sum=0;
    Tree[u].add=0;
    //printf("%d %d %d\n",u,Tree[u].l,Tree[u].r);
    if(l==r){
        Tree[u].sum=val[l];
        return;
    }
    int mid=(l+r)>>1;
    build(u<<1,l,mid);
    build((u<<1)|1,mid+1,r);
    push_up(u);
}
void push_down(int u){
    Tree[u<<1].add+=Tree[u].add;
    Tree[(u<<1)|1].add+=Tree[u].add;
    Tree[u].sum+=(Tree[u].r-Tree[u].l+1)*Tree[u].add;
    Tree[u].add=0;
}

int query(int u,int k){
    if(Tree[u].l==k&&Tree[u].r==k){
        return Tree[u].sum+Tree[u].add;
    }
    push_down(u);
    int mid=(Tree[u].l+Tree[u].r)>>1;
    if(k<=mid) return query(u<<1,k);
    else return query((u<<1)|1,k);
}

void update(int u,int l,int r,int c){
    if(Tree[u].l==l&&Tree[u].r==r){
        Tree[u].add+=c;
        return;
    }
    push_down(u);//注意
    int mid=(Tree[u].l+Tree[u].r)>>1;
    if(r<=mid) update(u<<1,l,r,c);
    else if(l>mid) update((u<<1)|1,l,r,c);
    else{
        update(u<<1,l,mid,c);
        update((u<<1)|1,mid+1,r,c);
    }
}
void update_Tree(int u,int v,int c){
    int f1=top[u],f2=top[v];
    while(f1!=f2){
        //printf("%d %d\n",u,v);
        if(dep[f1]<dep[f2]){
            swap(f1,f2);
            swap(u,v);
        }
        update(1,w[f1],w[u],c);
        u=fa[f1];f1=top[u];
    }
    if(u==v){
        update(1,w[u],w[u],c);
        return;
    }
    if(dep[u]<dep[v]){
        swap(u,v);
    }
    update(1,w[v],w[u],c);
}
int main(){
    freopen("in.txt","r",stdin);
    while(scanf("%d%d%d",&n,&m,&p)!=EOF){
        init();
        for(int i=1;i<=n;i++){
            scanf("%d",&enemy[i]);
        }
        int u,v;
        for(int i=1;i<=m;i++){
            scanf("%d%d",&u,&v);
            node[tot].to=v;
            node[tot].next=head[u];
            head[u]=tot++;
            node[tot].to=u;
            node[tot].next=head[v];
            head[v]=tot++;
        }
        //连虚根
        node[tot].to=1;
        node[tot].next=head[n+1];
        head[n+1]=tot++;
        node[tot].to=n+1;
        node[tot].next=head[1];
        head[1]=tot++;
        dfs1(n+1,1);
        init2();
        dfs2(n+1);
        //show();
        updateval();
        build(1,1,n+1);
        char str[10];
        for(int i=0;i<p;i++){
            scanf("%s",str);
            int ta,tb,tc;
            if(str[0]=='Q'){
                scanf("%d",&ta);
                printf("%d\n",query(1,w[ta]));
            }else if(str[0]=='D'){
                scanf("%d%d%d",&ta,&tb,&tc);
                update_Tree(ta,tb,-tc);
            }else{
                scanf("%d%d%d",&ta,&tb,&tc);
                update_Tree(ta,tb,tc);
            }
        }
    }
    return 0;
}


```
