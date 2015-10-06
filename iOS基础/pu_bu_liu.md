# 瀑布流

1. 使用UICollectionView实现

2. UICollectionViewFlowLayout 流水布局对象的方法讲解

```
2.1 -layoutAttributesForElementsInRect
        
    - 布局子控件时会调用下面的方法，有用区域的所有cell的布局属性
    - 默认此方法只会程序启动时调用一次，停止拖拽或者反弹时会调用
    
2.2 -shouldInvalidateLayoutForBoundsChange
    - 设置cell的bounds改变时，之前的布局无效，要重新调用layoutAttributesForElementsInRect
    
    
2.3 targetContentOffsetForProposedContentOffset
  - 返回最终可见区域的offset
```

3.滚动时，实现图片缩放
  - 监听滚动事件
  - 设置cell的transform（缩放）
    - 计算缩放比例-(0,1)
    - 计算cell的中心点x 与collectionView的中心点的间距
    - 获取间距后，再计算缩放比例
    - 不管cell最后停止滑动时候，始终有一个cell是显示在中心
    - 停止滚动
    - cell是显示在中心

