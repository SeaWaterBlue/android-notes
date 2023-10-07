
RecycleView和ListView区别 2023-10-07
===


## 1. 布局

### ListView

    只支持一种布局，即竖直方向滑动

### RecycleView

    支持三种布局（线性布局，网格布局，瀑布流布局）
    可以通过设置不同的LayoutManager实现

## 2. 数据刷新

### ListView

    默认采用notifyDataChanged()实现全局数据刷新，比较消耗资源。同时也可以通过自定义的方法再Adapter中实现一个onItemChanged()方法实现局部数据刷新。

### RecycleView

    默认即可通过ontifyItemChanged()实现局部数据刷新

## 3. Item的重复利用

### ListView

    默认不支持Item的重用，即加载一个新的Item就新建一个view，可以通过再Adapter中判断convertView是否为空来实现view的重用，同时，ListView中的ViewHolder需要由自己实现。

### RecycleView

    默认实现view的重用，并且可以继承已经实现好的ViewHolder.

## 4. 缓存机制

### ListView

    两级缓存机制，分别为ActiveViews和ScrapViews.

    ActiveViews为一级缓存，存放屏幕上显示的Views。

    ScrapViews为二级缓存，存放那些可能会被重用的Views.

### RecycleView

    四级缓存机制，分别为（ChangedScrap、AttachedScrap），CachedViews，RecycledViewPool，ViewCacheExtension。

    ChangedScrap、AttachedScrap都是一级缓存，其中ChangedScrap存放数据已经改变的ViewHolder，AttachedScrap存放未与RecycleView分离的ViewHolder（个人理解是显示在界面上的）；

    CachedViews是二级缓存，存放滑出屏幕外的ViewHolder；

    RecycledViewPool是三级缓存，存放在这里面的ViewHolder可以通过设置etRecycledViewPool(RecycledViewPool)来实现多个RecycleView共共享其中的ViewHolder；

    ViewCacheExtension是四级缓存，此缓存没有规划的作用，是预留用于开发者自定义的。发者可实现方法getViewForPositionAndType(Recycler recycler, int position, int type)来实现自己的缓存。

## 5. 嵌套滑动

### ListView

    不支持

### RecycleView

    为了支持嵌套滑动，子View必须实现NestedScrollingChild接口，父View必须实现NestedScrollingParent接口。

## 6. 空数据处理

### ListView

    ListView 提供了 setEmptyView 这个 API 来让我们处理 Adapter 中数据为空的情况

### RecycleView

    不支持

## 7. 分割线实现

### ListView

```Java
    android:divider="@android:color/transparent"
    android:dividerHeight="5dp"  
```

### RecycleView

    需要在Item的Layout中自定义添加分割线
