###1.初始位置为左侧，大小和文字位置相同，使用边缘填充；
###2.LinearGradient的渐变长度是两个文字长度；

代码实现：

	public class ShimmerTextView extends TextView {private Paint mPaint;
    private int mDx;
    private LinearGradient mLinearGradient;
    public ShimmerTextView(Context context) {
        super(context);
        init();
    }

    public ShimmerTextView(Context context, AttributeSet attrs) {
        super(context, attrs);
        init();
    }

    public ShimmerTextView(Context context, AttributeSet attrs, int defStyle) {
        super(context, attrs, defStyle);
        init();
    }

    private void init(){
        mPaint =getPaint();
    }

    @Overrideprotected void onLayout(boolean changed, int left, int top, int right, int bottom) {
        super.onLayout(changed, left, top, right, bottom);
    //动态改变移动的位置
        ValueAnimator animator = ValueAnimator.ofInt(0,2*getMeasuredWidth());
        animator.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
            @Overridepublic void onAnimationUpdate(ValueAnimator animation) {
                mDx = (Integer) animation.getAnimatedValue();
                postInvalidate();
            }
        });
        animator.setRepeatMode(ValueAnimator.RESTART);
        animator.setRepeatCount(ValueAnimator.INFINITE);
        animator.setDuration(2000);
        animator.start();
    //初始位置在左侧
        mLinearGradient = new LinearGradient(- getMeasuredWidth(),0,0,0,new int[]{
                getCurrentTextColor(),0xff00ff00,getCurrentTextColor()
        },
                new float[]{
                        0,
                        0.5f,
                        1
                },
                Shader.TileMode.CLAMP
        );
    }

    @Overrideprotected void onDraw(Canvas canvas) {
    //巧妙之处，使用matrix做移动动画
        Matrix matrix = new Matrix();
        matrix.setTranslate(mDx,0);
        mLinearGradient.setLocalMatrix(matrix);
        mPaint.setShader(mLinearGradient);

        super.onDraw(canvas);
    }
	}

