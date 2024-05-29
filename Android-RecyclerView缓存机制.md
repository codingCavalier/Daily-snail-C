### Recycler 四层缓存（Scrap、CacheView、ViewCacheExtension、RecycledViewPool），

1、准确来讲，是三级缓存，Scrap主要用于静态Layout的时候保存临时变量，不太算缓存。<br>
2、CacheView用于精准快速查找是不是为刚刚移除的ViewHolder，不需要重新绑定数据，有数量限制，最大为2<br>
3、RecycledViewPool 里 根据viewType作为分组，缓存ViewHolder<br>

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/fe68a3bd-345e-4fed-a2e1-65db26332a8a)

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/2d124658-28bc-4f58-93d6-bd517b688b6d)

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/6c41c893-3eb5-4a38-b0fa-f3cdc149ff6e)

原文链接：https://juejin.cn/post/6984974879296585764
