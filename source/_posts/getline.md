---
title: getline
date: 2018-08-07 21:06:10
tags: getline
categorites: ACM
---

#接受空格输入
1. gets() //可能会报错
2. fgets(str,count,stdin);
3. cin.getline(str,count);
4. getline(cin,str);(str为string)

---
#string转char[]
- string.c_str() 返回char指针
- strcpy(tmp,str.c_str());

---
#char[]转string
- string tmp(str);
- string tmp=str;
- string tmp;tmp=str;

