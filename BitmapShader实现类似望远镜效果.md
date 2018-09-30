### 1.使用一张Bitmap图片用作背景，无论你利用绘图函数绘多大一块，在哪绘制，与Shader无关。因为Shader总是在控件的左上角开始，而你绘制的部分只是显示出来的部分而已。没有绘制的部分虽然已经生成，但只是不会显示出来罢了。
主要代码：

	public class TelescopeView extends View {private Paint mPaint;
    private Bitmap mBitmap,mBitmapBG;
    private int mDx = -1, mDy = -1;
    public TelescopeView(Context context) {
        super(context);
        init();
    }

    public TelescopeView(Context context, AttributeSet attrs) {
        super(context, attrs);
        init();
    }

    public TelescopeView(Context context, AttributeSet attrs, int defStyle) {
        super(context, attrs, defStyle);
        init();
    }

    private void init() {
        mPaint = new Paint();
        mBitmap = BitmapFactory.decodeResource(getResources(), R.drawable.scenery);
    }

    @Overridepublic boolean onTouchEvent(MotionEvent event) {
        switch (event.getAction()) {
            case MotionEvent.ACTION_DOWN:
                mDx = (int) event.getX();
                mDy = (int) event.getY();
                postInvalidate();
                return true;
            case MotionEvent.ACTION_MOVE:
                mDx = (int) event.getX();
                mDy = (int) event.getY();
                break;
            case MotionEvent.ACTION_UP:
            case MotionEvent.ACTION_CANCEL:
                mDx = -1;
                mDy = -1;
                break;
        }


        postInvalidate();
        return super.onTouchEvent(event);
    }

    @Overrideprotected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        if (mBitmapBG == null){
            mBitmapBG = Bitmap.createBitmap(getWidth(),getHeight(), Bitmap.Config.ARGB_8888);
            Canvas canvasbg = new Canvas(mBitmapBG);
                    //设置bitmap背景
            canvasbg.drawBitmap(mBitmap,null,new Rect(0,0,getWidth(),getHeight()),mPaint);
        }

        if (mDx != -1 && mDy != -1) {
                    //设置shader
            mPaint.setShader(new BitmapShader(mBitmapBG, Shader.TileMode.REPEAT, Shader.TileMode.REPEAT));
            canvas.drawCircle(mDx, mDy, 150, mPaint);
        }
    }
	}
