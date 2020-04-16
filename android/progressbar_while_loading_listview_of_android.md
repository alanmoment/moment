# ProgressBar while loading ListView of Android

ListView通常是用來顯示列表的Widget，但是一般不可能直接從不管是遠端的Database或是Sqlite撈出全部的資料然後直接顯示，一定是分批撈取分批顯示。

這時就需要一個Loading畫面讓Client知道說，當前正在載入資料。不管是跳Dialog或是像Facebook一樣在畫面Bottom顯示Loading layout都可以。

我採取的是像Facebook一樣的方式呈現。

## 建立MainActivity.java ##

`simpleAdapter`可另外設置R.layout.listview_item讓ListView中的資料吃別隻xml。

	package com.example.test;
	
	import android.app.Activity;
	import android.content.Context;
	import android.os.Bundle;
	import android.util.Log;
	import android.view.Gravity;
	import android.widget.AbsListView;
	import android.widget.LinearLayout;
	import android.widget.ListView;
	import android.widget.ProgressBar;
	import android.widget.SimpleAdapter;
	import android.widget.TextView;
	
	import java.util.ArrayList;
	import java.util.HashMap;
	import java.util.List;
	import java.util.Map;
	
	public class MainActivity extends Activity {
	
	    private String TAG_LOG = MainActivity.class.getName();
	    // ViewList需要用到的制定鍵值
	    String[] adapterKey;
	    // ViewList需要用到的制定鍵值
	    int[] adapterViewKey;
	    // 顯示讀取畫面的容器
	    LinearLayout loadingLayout;
	    // 顯示列表的容器
	    ListView listView;
	    // 列表
	    SimpleAdapter simpleAdapter;
	    // 列表資料
	    List<map object>> items = new ArrayList<map object>>();
	
	    /**
	     * 取資料
	     * @param items
	     * @return
	     */
	    private List<map object>> getData(List<map object>> items, String[] adapterKey)
	    {
	        Integer limitCount = items.size() + 10;
	        for (Integer count = items.size(); count<limitcount count map object> item = new HashMap<string object>();
	            item.put(adapterKey[0], count+1);
	            items.add(item);
	        }
	        return items;
	    }
	
	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);
	
	        listView = (ListView) findViewById(R.id.listView);
	
	        // 設定ListView相對應的View
	        adapterKey = new String[]{"number"};
	        adapterViewKey = new int[]{R.id.txtNumber};
	
	        // 取得資料並且設定Adapter
	        items = getData(items, adapterKey);
	        simpleAdapter = new SimpleAdapter(
	                MainActivity.this,
	                items,
	                R.layout.listview_item,
	                adapterKey,
	                adapterViewKey
	        );
	
	
	        // Set loading view before of set setAdapter
	        loadingLayout = getLoadingLayout(MainActivity.this, null);
	        listView.addFooterView(loadingLayout);
	        listView.setAdapter(simpleAdapter);
	        listView.setOnScrollListener(moreItemScrollListener);
	    }
	
	    // 已經到資料結尾
	    boolean isEnd = false;
	
	    /**
	     * 聆聽往上滑動讀取更多物件的事件，會在下方顯示讀取狀態
	     */
	    private AbsListView.OnScrollListener moreItemScrollListener = new AbsListView.OnScrollListener() {
	
	        // 可以再次載資料
	        boolean canLoadData = false;
	        // 在讀取資料中
	        boolean isLoadingMode = false;
	
	        @Override
	        public void onScroll(AbsListView view, int firstVisibleItem, int visibleItemCount, int totalItemCount) {
	
	            //如果不是在讀取資料的狀態、還有資料可以讀取
	            if (firstVisibleItem + visibleItemCount == totalItemCount && totalItemCount > 0 && !isLoadingMode && !isEnd) {
	                canLoadData = true;
	                Log.d(TAG_LOG, "can load");
	            }
	        }
	
	        @Override
	        public void onScrollStateChanged(AbsListView view, int scrollState) {
	
	            //當ListView拉到底,且沒在載入資料時，而且還有資料可以讀取
	            if (scrollState == AbsListView.OnScrollListener.SCROLL_STATE_IDLE && canLoadData && !isLoadingMode && !isEnd) {
	                // 再次撈取資料前先關閉可以撈取資料的狀態
	                Log.d(TAG_LOG, "loading");
	                isLoadingMode = true;
	                loadMoreData();
	
	                //通知Adapter更新
	                simpleAdapter.notifyDataSetChanged();
	                canLoadData = false;
	                isLoadingMode = false;
	            }
	        }
	    };
	
	    /**
	     * 處理要求更多物件
	     */
	    private void loadMoreData() {
	        if (this.items.size() = 30 && !isEnd) {
	            isEnd = true;
	            this.listView.removeFooterView(this.loadingLayout);
	            Log.d(TAG_LOG, "can't load again");
	        }
	    }
	
	    /**
	     * Get loading view layout object
	     *
	     * @param context
	     * @param msg
	     * @return LinearLayout
	     */
	    public LinearLayout getLoadingLayout(Context context, String msg) {
	        if (msg == null || msg == "")
	            msg = context.getResources().getString(R.string.loading_text);
	
	        Log.d("Util", "Loading screen");
	        LinearLayout.LayoutParams WClayoutParams = new LinearLayout.LayoutParams(LinearLayout.LayoutParams.WRAP_CONTENT, LinearLayout.LayoutParams.WRAP_CONTENT);
	        LinearLayout.LayoutParams FFlayoutParams = new LinearLayout.LayoutParams(LinearLayout.LayoutParams.FILL_PARENT, LinearLayout.LayoutParams.FILL_PARENT);
	
	        LinearLayout layout = new LinearLayout(context);
	        layout.setOrientation(LinearLayout.HORIZONTAL);
	        ProgressBar progressBar = new ProgressBar(context);
	        progressBar.setPadding(0, 0, 15, 0);
	        layout.addView(progressBar, WClayoutParams);
	
	        TextView textView = new TextView(context);
	        textView.setText(msg);
	        textView.setGravity(Gravity.CENTER_VERTICAL);
	
	        layout.addView(textView, FFlayoutParams);
	        layout.setGravity(Gravity.CENTER);
	
	        LinearLayout loadingLayout = new LinearLayout(context);
	        loadingLayout.addView(layout, WClayoutParams);
	        loadingLayout.setGravity(Gravity.CENTER);
	
	        return loadingLayout;
	    }
	    
	}

`getLoadingLayout`函式是用在當手勢往上滑畫面在最底部時要顯示讀取資料中的Loading畫面。

Loading layout並不是當讀取時才出現，而是他一直置於ListView最底部，所以讀取不到資料時要將Loading layout移除

	this.listView.removeFooterView(this.loadingLayout);

執行後就會是這樣

![Loading](https://lh6.googleusercontent.com/-GL_0hweWTdk/Uk5jHLiaynI/AAAAAAAAAPI/brLeVgUGudM/w281-h500-no/loading.png)

![Loading end](https://lh3.googleusercontent.com/-frGkmZt1NCI/Uk5jG6FmNtI/AAAAAAAAAPA/pKj8070O0Yk/w281-h500-no/load+end.png) 

> Oct 11th, 2013 10:24:00am