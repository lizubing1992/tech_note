控件选择：使用linearLayout，简单，波纹效果需要频繁重绘，考虑性能选择LinearLayout
解决问题：
    
###1.如何得知用户点击了哪个元素
   首先元素必须可被点击，拦截所有的点击事件:dispatchTouchEvent,不推荐使用onInterceptTouchEvent这个不是一直被回调的，down up同时在一个可点击的元素中，判断是否是在一个空间的四个角的位置上；
代码实现：

	@Override  
	public boolean dispatchTouchEvent(MotionEvent event) {  
    int x = (int) event.getRawX();  
    int y = (int) event.getRawY();  
    int action = event.getAction();  
    if (action == MotionEvent.ACTION_DOWN) {  
        View touchTarget = getTouchTarget(this, x, y);  
        if (touchTarget.isClickable() && touchTarget.isEnabled()) {  
            mTouchTarget = touchTarget;  
            initParametersForChild(event, touchTarget);  
            postInvalidateDelayed(INVALIDATE_DURATION);  
        }  
    } else if (action == MotionEvent.ACTION_UP) {  
        mIsPressed = false;  
        postInvalidateDelayed(INVALIDATE_DURATION);  
        mDispatchUpTouchEventRunnable.event = event;  
        postDelayed(mDispatchUpTouchEventRunnable, 400);  
        return true;  
    } else if (action == MotionEvent.ACTION_CANCEL) {  
        mIsPressed = false;  
        postInvalidateDelayed(INVALIDATE_DURATION);  
    }  
  
    return super.dispatchTouchEvent(event);  
}  
来源： http://blog.csdn.net/singwhatiwanna/article/details/42614953
 
### 2.如何取得被点击元素的信息
	private View getTouchTarget(View view, int x, int y) {  
    View target = null;  
    ArrayList<View> TouchableViews = view.getTouchables();  
    for (View child : TouchableViews) {  
        if (isTouchPointInView(child, x, y)) {  
            target = child;  
            break;  
        }  
    }  
  
    return target;  
	}  
  
	private boolean isTouchPointInView(View view, int x, int y) {  
    int[] location = new int[2];  
    view.getLocationOnScreen(location);  
    int left = location[0];  
    int top = location[1];  
    int right = left + view.getMeasuredWidth();  
    int bottom = top + view.getMeasuredHeight();  
    if (view.isClickable() && y >= top && y <= bottom  
            && x >= left && x <= right) {  
        return true;  
    }  
    return false;  
	}  
###3.如何通过layout进行重绘绘制水波纹
   绘制时机在dispatchDraw绘制，还要使用延时机制来减少绘制次数，考虑性能；
代码：

	@Override  
	public boolean dispatchTouchEvent(MotionEvent event) {  
    int x = (int) event.getRawX();  
    int y = (int) event.getRawY();  
    int action = event.getAction();  
    if (action == MotionEvent.ACTION_DOWN) {  
        View touchTarget = getTouchTarget(this, x, y);  
        if (touchTarget.isClickable() && touchTarget.isEnabled()) {  
            mTouchTarget = touchTarget;  
            initParametersForChild(event, touchTarget);  
            postInvalidateDelayed(INVALIDATE_DURATION);  
        }  
    } else if (action == MotionEvent.ACTION_UP) {  
        mIsPressed = false;  
        postInvalidateDelayed(INVALIDATE_DURATION);  
        mDispatchUpTouchEventRunnable.event = event;  
        postDelayed(mDispatchUpTouchEventRunnable, 400);  
        return true;  
    } else if (action == MotionEvent.ACTION_CANCEL) {  
        mIsPressed = false;  
        postInvalidateDelayed(INVALIDATE_DURATION);  
    }  
  
    return super.dispatchTouchEvent(event);  
}  

### 4.如何延迟up事件的分发:
 强行消耗up事件再延迟分发
	private class DispatchUpTouchEventRunnable implements Runnable {  
    public MotionEvent event;  
  
    @Override  
    public void run() {  
        if (mTouchTarget == null || !mTouchTarget.isEnabled()) {  
            return;  
        }  
  
        if (isTouchPointInView(mTouchTarget, (int)event.getRawX(), (int)event.getRawY())) {  
            mTouchTarget.dispatchTouchEvent(event);  
        }  
    }  
}; 






