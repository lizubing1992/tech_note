##1.原理很简单，监听Viewpager.OnPageChangeListener来实现切换效果的，有两个明显的区别：
###1.1：MagicIndicator没有提供setViewPager方法和ViewPager强绑定，可以很好的切换不同的指示器
###  1.2：将指示器进行了抽象，可以扩展不同的切换效果，实际就是一个FramLayout帧布局

##2.MagicIndicator,导航器被抽象成IPagerNavigatir,其作为唯一的子元素添加到其中，onPageXXX系列回调原封不动的传递给了IPagerNavigator,实现不同的指示器效果，只需要继承任意View并实现IPagerNavitor接口即可；

##3.OnPagerXXX系列回调被NavigtorHelper转换成onEnter ,onLeave,onSeleceted,onDeselected四个回调传递给IPagerTitleView

## 4.onPageXXX 系列回调原封不动的传递给了它，此外，还有个最重要的 onPositionDataProvide 回调，这个是干嘛的呢？试想一下，如果要使得扩展 IPagerIndicator 可以实现任意的切换效果，那最起码应该把每一个 IPagerTitleView 的位置信息传递给 IPagerIndicator 吧，有了这些位置信息，继承 View 并实现 IPagerIndicator 后，不论是画圆还是画直线，或是画上图中的贝塞尔吸附式效果，才有坐标可循啊


  