---
layout: post
title:  "Android RecyclerView和CardView笔记"
date:   2016-03-22
categories: android
---

### 添加依赖
    
除了appcompat-v7, recyclerview和cardview需要单独添加.
        
    dependencies {
        compile fileTree(dir: 'libs', include: ['*.jar'])
        testCompile 'junit:junit:4.12'
        compile 'com.android.support:appcompat-v7:23.2.1'
        compile 'com.android.support:design:23.2.1'
        compile 'com.android.support:recyclerview-v7:23.2.1'
        compile 'com.android.support:cardview-v7:23.2.1'

    }        

### 定义layout

activity.xml    

    <android.support.v7.widget.RecyclerView
        android:id="@+id/recycler_view"
        android:scrollbars="vertical"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="#bbccaa"
        />

cardview_item.xml    

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:orientation="vertical" android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <RelativeLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content">

            <ImageView
                android:layout_width="100dp"
                android:layout_height="100dp"
                android:id="@+id/img"
                android:src="@drawable/logo" />

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:textAppearance="?android:attr/textAppearanceMedium"
                android:text="Medium Text"
                android:id="@+id/name"
                android:layout_alignParentTop="true"
                android:layout_toRightOf="@+id/img"
                android:layout_toEndOf="@+id/img"
                android:layout_marginLeft="8dp"
                android:layout_marginTop="8dp" />

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:textAppearance="?android:attr/textAppearanceMedium"
                android:text="Medium Text"
                android:id="@+id/age"
                android:layout_centerVertical="true"
                android:layout_alignLeft="@+id/name"
                android:layout_alignStart="@+id/name" />
        </RelativeLayout>
    </LinearLayout>
    
### 编写实现

需要定义RecyclerView,一个LayoutManager和RecyclerViewAdapter.

    private RecyclerView recyclerView;
    private LinearLayoutManager linearLayoutManager;
    private RecyclerViewAdapter recyclerViewAdapter;
    
    List listData = new ArrayList<Map<String,Object>>();
    for(int d=0; d<100; d++)
    {
        Map line = new HashMap();

        line.put("name", "AAAAA");
        line.put("age", d);

        listData.add(line);
    }

    recyclerView = (RecyclerView) findViewById(R.id.recycler_view);
    linearLayoutManager = new LinearLayoutManager(this);
    // gridLayoutManager = new GridLayoutManager(this, 2);
    recyclerView.setLayoutManager(gridLayoutManager);
    recyclerViewAdapter = new RecyclerViewAdapter(listData);
    recyclerView.setAdapter(recyclerViewAdapter);
        

LayoutManager有三种,分别对应不同的显示样式.    

1. LinearLayoutManager,纵向或横向的单列布局.
2. GridLayoutManager, 网格式的多列布局.
3. StaggeredGridLayoutManager, 瀑布流布局.

定义Adapter

    public class RecyclerViewAdapter extends RecyclerView.Adapter<RecyclerViewAdapter.ViewHolder>
    {
        List<Map<String,Object>> list =new ArrayList<Map<String,Object>>();

        public RecyclerViewAdapter(List<Map<String,Object>> list)
        {
            this.list = list;

        }

        @Override
        public RecyclerViewAdapter.ViewHolder onCreateViewHolder(ViewGroup viewGroup, int viewType) {
            // View view = LayoutInflater.from(viewGroup.getContext()).inflate(R.layout.recycler_item,viewGroup,false);
            View view = LayoutInflater.from(viewGroup.getContext()).inflate(R.layout.card_view_item,viewGroup,false);
            ViewHolder vh = new ViewHolder(view);
            return vh;
        }

        @Override
        public void onBindViewHolder(RecyclerViewAdapter.ViewHolder holder, int position) {
            holder.name.setText(list.get(position).get("name").toString());
            holder.age.setText(list.get(position).get("age").toString());

        }

        @Override
        public int getItemCount() {
            return list.size();

        }

        //自定义的ViewHolder，持有每个Item的的所有界面元素
        public class ViewHolder extends RecyclerView.ViewHolder {
            public TextView name;
            public TextView age;

            public ViewHolder(View view){
                super(view);
                name = (TextView) view.findViewById(R.id.name);
                age = (TextView) view.findViewById(R.id.age);

            }
        }

    }
    
    
### 滚动到底部加载更多功能实现

添加一个接口实现类.这个对应的是单列列表(LinearLayoutManager),如果是Grid替换成GridLayoutManager即可.

    public abstract class EndlessRecyclerOnScrollListener extends
            RecyclerView.OnScrollListener {

        private int previousTotal = 0;
        private boolean loading = true;
        int firstVisibleItem, visibleItemCount, totalItemCount;

        private int currentPage = 1;

        private LinearLayoutManager mLinearLayoutManager;

        public EndlessRecyclerOnScrollListener(
                LinearLayoutManager linearLayoutManager) {
            this.mLinearLayoutManager = linearLayoutManager;
        }

        @Override
        public void onScrolled(RecyclerView recyclerView, int dx, int dy) {
            super.onScrolled(recyclerView, dx, dy);

            visibleItemCount = recyclerView.getChildCount();
            totalItemCount = mLinearLayoutManager.getItemCount();
            firstVisibleItem = mLinearLayoutManager.findFirstVisibleItemPosition();

            if (loading) {
                if (totalItemCount > previousTotal) {
                    loading = false;
                    previousTotal = totalItemCount;
                }
            }
            if (!loading
                    && (totalItemCount - visibleItemCount) <= firstVisibleItem) {
                currentPage++;
                onLoadMore(currentPage);
                loading = true;
            }
        }

        public abstract void onLoadMore(int currentPage);
    }
    
Activity中使用

    recyclerView.addOnScrollListener(new EndlessGridRecyclerOnScrollListener(layoutManager) {
        @Override
        public void onLoadMore(int currentPage) {
            Log.v("recycler test", "load more");
            Toast.makeText(MainActivity.this, "load more", Toast.LENGTH_SHORT).show();

        }
    });
    

### 下拉刷新功能的实现

使用SwipeRefreshLayout来实现

把需要刷新的列表SwipeRefreshLayout包裹.

layout.xml

    <android.support.v4.widget.SwipeRefreshLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/swipe"
        >
        <android.support.v7.widget.RecyclerView
            android:id="@+id/recycler_view"
            android:scrollbars="vertical"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="#bbccaa"
            />
    </android.support.v4.widget.SwipeRefreshLayout>

Activity.java

    swipeRefreshLayout = (SwipeRefreshLayout) findViewById(R.id.swipe);
    swipeRefreshLayout.setOnRefreshListener(new SwipeRefreshLayout.OnRefreshListener() {
        @Override
        public void onRefresh() {
            swipeRefreshLayout.setRefreshing(true);
            Toast.makeText(MainActivity.this, "reload", Toast.LENGTH_SHORT).show();

            swipeRefreshLayout.setRefreshing(false);
        }
    });

setRefreshing用于显示/隐藏loading动画.


### 添加/删除

修改绑定的数据然后调用Adapter的方法告知ListView.一般使用以下方法.    

1. notifyItemChanged(int position)     通知位置position的Item的数据改变
2. notifyItemInserted(int)             通知位置position的Item的数据插入
3. notifyItemRemoved(int)              通知位置position的Item的数据移除
4. notifyDataSetChanged()              跟ListView兼容,但没有动画效果

## RecyclerView和ListView的区别

1. 添加LayoutManager单独管理布局方式,默认包含三种布局方式

* LinearLayoutManager，可以支持水平和竖直方向上滚动的列表。
* StaggeredGridLayoutManager，可以支持交叉网格风格的列表，类似于瀑布流或者Pinterest。
* GridLayoutManager，支持网格展示，可以水平或者竖直滚动，如展示图片的画廊。

2. 强制使用ViewHolder来保存视图引用

3. 列表项添加,删除,移动的时候可以添加动画

4. ListView通过AdapterView.OnItemClickListener接口来探测点击事件。而RecyclerView则通过RecyclerView.OnItemTouchListener接口来探测触摸事件



