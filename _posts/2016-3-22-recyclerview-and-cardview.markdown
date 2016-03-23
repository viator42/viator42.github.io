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