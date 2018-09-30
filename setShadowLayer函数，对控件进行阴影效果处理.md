	public void setShadowLayer(float radius, float dx, float dy, int color) 
###构造方法
模糊半径使用的是高斯模糊算法
   画笔颜色对图片的阴影无效，复制本身的图片进行边缘的高斯模糊，看起来舒服

###2.clearShadowLayer()消除阴影

###3.setMaskFilter之BlurMaskFilter概述
  BlurMaskFilter(float radius,Blur style)
 style:发光样式，内发光，外发光，内外发光

###4.图片添加阴影
 步骤：
    1.绘制出一个一样大小的灰色图形
       同样大小的图形，通过Bitmap的extractAlpha()方法获得其值高与宽

    2.对灰色图像应用BlurMaskFilter使其内外发光
        mPaint.setMaskFilter(new BlurMaskFilter(10,BlurMaskFilter.Blur.NORMAL))

    3.偏移原图形一段距离绘制出来
在DrawBitmap时使用new React()偏移10个像素

###封装后的代码：
	public class BitmapShadowView extends View {private Paint mPaint;
    private Bitmap mBmp,mShadowBmp;
    private int mDx = 10,mDy = 10;
    private float mRadius = 0;
    private int mShadowColor;

    public BitmapShadowView(Context context, AttributeSet attrs) throws Exception{
        super(context, attrs);

        init(context,attrs);
    }

    public BitmapShadowView(Context context, AttributeSet attrs, int defStyle) throws Exception{
        super(context, attrs, defStyle);
        init(context,attrs);
    }


    private void init(Context context,AttributeSet attrs) throws Exception {
        setLayerType(LAYER_TYPE_SOFTWARE,null);
        /**
         * 提取属性定义
         */
        TypedArray typedArray = context.obtainStyledAttributes(attrs,R.styleable.BitmapShadowView);
        int BitmapID = typedArray.getResourceId(R.styleable.BitmapShadowView_src,-1);
        if (BitmapID == -1){
            throw new Exception("BitmapShadowView 需要定义Src属性,而且必须是图像");
        }
        mBmp = BitmapFactory.decodeResource(getResources(),BitmapID);
        mDx = typedArray.getInt(R.styleable.BitmapShadowView_shadowDx,0);
        mDy = typedArray.getInt(R.styleable.BitmapShadowView_shadowDy,0);
        mRadius = typedArray.getFloat(R.styleable.BitmapShadowView_shadowRadius,0);
        mShadowColor = typedArray.getColor(R.styleable.BitmapShadowView_shadowColor,Color.BLACK);

        typedArray.recycle();
        /**
         * 其它定义
         */
        mPaint = new Paint();
        mShadowBmp = mBmp.extractAlpha();

    }

    @Overrideprotected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);


        int measureWidth = MeasureSpec.getSize(widthMeasureSpec);
        int measureHeight = MeasureSpec.getSize(heightMeasureSpec);
        int measureWidthMode = MeasureSpec.getMode(widthMeasureSpec);
        int measureHeightMode = MeasureSpec.getMode(heightMeasureSpec);

        int width = mBmp.getWidth();
        int height = mBmp.getHeight();
        setMeasuredDimension((measureWidthMode == MeasureSpec.EXACTLY) ? measureWidth: width, (measureHeightMode == MeasureSpec.EXACTLY) ? measureHeight: height);
    }

    @Overrideprotected void onDraw(Canvas canvas) {
        super.onDraw(canvas);

        int width = getWidth()-mDx;
        int height = width * mBmp.getHeight()/mBmp.getWidth();

        //绘制阴影
        mPaint.setColor(mShadowColor);
        mPaint.setMaskFilter(new BlurMaskFilter(mRadius, BlurMaskFilter.Blur.NORMAL));
        canvas.drawBitmap(mShadowBmp,null,new Rect(mDx,mDy,width,height),mPaint);

        //绘制原图像
        mPaint.setMaskFilter(null);
        canvas.drawBitmap(mBmp,null,new Rect(0,0,width,height),mPaint);
    }
	}











