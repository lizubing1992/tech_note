###完成功能：
    1.手指按下时，绘制一个默认大小的圆；
    2.手指移动时，所绘制的默认圆的位置需要跟随手指移动；
    3.手指放开时，圆逐渐变大
    4.动画结束时，波纹效果消失

主要是使用RadiusGradient的放射性渐变功能，再加上TileMode的填充模式

主要代码：

	public class RippleView extends Button {private int mX, mY;
    private ObjectAnimator mAnimator;
    private int DEFAULT_RADIUS = 50;
    private int mCurRadius = 0;
    private RadialGradient mRadialGradient;
    private Paint mPaint;

    public RippleView(Context context) {
        super(context);
        init();
    }

    public RippleView(Context context, AttributeSet attrs) {
        super(context, attrs);
        init();
    }

    public RippleView(Context context, AttributeSet attrs, int defStyle) {
        super(context, attrs, defStyle);
        init();
    }

    private void init() {
        setLayerType(LAYER_TYPE_SOFTWARE,null);
        mPaint = new Paint();
    }


    @Overridepublic boolean onTouchEvent(MotionEvent event) {

   //不管是按下还是移动，统一的默认圆大小
        if (mX != event.getX() || mY != mY) {
            mX = (int) event.getX();
            mY = (int) event.getY();

            setRadius(DEFAULT_RADIUS);
        }

        if (event.getAction() == MotionEvent.ACTION_DOWN) {

            return true;
        } else if (event.getAction() == MotionEvent.ACTION_UP) {

            if (mAnimator != null && mAnimator.isRunning()) {
                mAnimator.cancel();
            }

            if (mAnimator == null) {
        //默认大小到控件的宽度的动画
                mAnimator = ObjectAnimator.ofInt(this,"radius",DEFAULT_RADIUS, getWidth());
            }

            mAnimator.setInterpolator(new AccelerateInterpolator());
            mAnimator.addListener(new Animator.AnimatorListener() {
                @Overridepublic void onAnimationStart(Animator animation) {

                }

                @Overridepublic void onAnimationEnd(Animator animation) {
           //圆消失
                    setRadius(0);
                }

                @Overridepublic void onAnimationCancel(Animator animation) {

                }

                @Overridepublic void onAnimationRepeat(Animator animation) {

                }
            });
            mAnimator.start();
        }

        return super.onTouchEvent(event);
    }

    public void setRadius(final int radius) {
        mCurRadius = radius;
        if (mCurRadius > 0) {
            mRadialGradient = new RadialGradient(mX, mY, mCurRadius, 0x00FFFFFF, 0xFF58FAAC, Shader.TileMode.CLAMP);
            mPaint.setShader(mRadialGradient);
        }
        postInvalidate();
    }


    @Overrideprotected void onDraw(Canvas canvas) {
        super.onDraw(canvas);

        canvas.drawCircle(mX, mY, mCurRadius, mPaint);
    }
	}

