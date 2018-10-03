# 学习链接
[文章链接](https://zhuanlan.zhihu.com/p/36398198)

# 最高效的公式

```
List.RemoveNulls(
  List.TransformMany(
    Text.Split([网址],"/"),
    each {"py","pw","my","mw"},
    (x,y) => if Text.StartsWith(x,y) then x else null 
    )
  ){0}?
```
