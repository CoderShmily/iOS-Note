# 瀑布流

1. 使用UICollectionView实现

2. UICollectionViewFlowLayout 流水布局对象的方法讲解

    2.1 -layoutAttributesForElementsInRect
        
    - 布局子控件时会调用下面的方法，有用区域的所有cell的布局属性
    - 默认此方法只会程序启动时调用一次，停止拖拽或者反弹时会调用
    
    2.2 -shouldInvalidateLayoutForBoundsChange
        - 设置cell的bounds改变时，之前的布局无效，要重新调用layoutAttributesForElementsInRect
    
    
 2.3 targetContentOffsetForProposedContentOffset
  - 返回最终可见区域的offset

