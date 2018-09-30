### 1.ACTION_CANCEL:
 触发条件事件被上层拦截，是只有上层View回收事件处理权的时候，具体的讲就ChildView才会收到一个ACTION_CANCEL事件，具体讲，Down事件传到子View,然后被父View确认为Move事件

### 2.ACTION_OUTSIDE:
一个触摸事件已经发生了UI元素的正常范围之外。因此不再提供完整的手势，只提供 运动/触摸 的初始位置
具体讲对话框点击外面对话框消失