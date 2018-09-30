### 1.RecyclerView滚动定位：
 利用LinearLayoutManager可以方便实现
 int position = "指定的位置";
 layoutManager.scrollToPostionWithOffset(positiong,0);
 layoutManager.setStackFromEnd(true);

### 2.RecyclerView动态调整View的宽高；

 调整适配器就可以
public class HorizationAdapter extends BaseQuickAdapter<News,BaseViewHolder> {private LayoutInflater layoutInflater;

    private int N ;
	public HorizationAdapter(Context mContex, int N) {
        super(item, DataServer.getNews());
        this.N =N;
        layoutInflater =LayoutInflater.from(mContex);
    }


    @Override
	protected void convert(final BaseViewHolder newsViewHolder, final News news) {
        newsViewHolder.setText(R.id.tv_title,news.title);
    }
    @Override
	protected View getItemView(final int layoutResId, final ViewGroup parent) {
        View view = layoutInflater.inflate(R.layout.item_news_title, parent, false);
        view.setMinimumWidth(parent.getWidth() / N);
        LinearLayout.LayoutParams parms = new LinearLayout.LayoutParams(parent.getWidth() / N, ViewGroup.LayoutParams.MATCH_PARENT);
        view.setLayoutParams(parms);
        return view;
    }

	}