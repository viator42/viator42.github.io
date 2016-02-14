---
layout: post
title:  "Android L, RecyclerView,CardView的使用"
date:   2016-02-14
categories: android
---

build.gradle中导入库文件,cardview需要单独导入.

    dependencies {
        compile fileTree(dir: 'libs', include: ['*.jar'])
        testCompile 'junit:junit:4.12'
        compile 'com.android.support:appcompat-v7:23.1.1'
        compile 'com.android.support:design:23.1.1'
        compile 'com.android.support:cardview-v7:23.1.1'
    }

main activity layout

    <android.support.v7.widget.RecyclerView
        android:id="@+id/my_recycler_view"
        android:scrollbars="vertical"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>

MainActivity.java

    private RecyclerView mRecyclerView;
    private RecyclerView.Adapter mAdapter;
    private RecyclerView.LayoutManager mLayoutManager;
    
    mRecyclerView = (RecyclerView) findViewById(R.id.my_recycler_view);
    mRecyclerView.setHasFixedSize(true);

    // use a linear layout manager
    mLayoutManager = new LinearLayoutManager(this);
    mRecyclerView.setLayoutManager(mLayoutManager);

    String[] myDataset = { "朱茵", "张柏芝", "张敏", "巩俐", "黄圣依", "赵薇", "莫文蔚", "如花" };

    // specify an adapter (see also next example)
    mAdapter = new MyAdapter(myDataset);
    mRecyclerView.setAdapter(mAdapter);
    
需要声明一个Adapter

    public class MyAdapter extends RecyclerView.Adapter<MyAdapter.ViewHolder> {
        private String[] mDataset;
        private Context context;

        public MyAdapter(String[] myDataset)
        {
            mDataset = myDataset;
        }

        @Override
        public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
            // 给ViewHolder设置布局文件
            context = parent.getContext();
            View v = LayoutInflater.from(context).inflate(R.layout.card_view, parent, false);
            return new ViewHolder(v);

        }

        @Override
        public void onBindViewHolder(ViewHolder holder, final int position) {
            holder.nameView.setText(mDataset[position]);

            holder.nameView.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    Toast.makeText(context, mDataset[position], Toast.LENGTH_SHORT).show();

                }
            });
        }

        @Override
        public int getItemCount() {
            return mDataset.length;
        }

        // 重写的自定义ViewHolder
        public static class ViewHolder
                extends RecyclerView.ViewHolder
        {
            public TextView nameView;

            public ViewHolder( View v )
            {
                super(v);
                nameView = (TextView) v.findViewById(R.id.name);
            }
        }
    }
    
cart_view.xml

    <?xml version="1.0" encoding="utf-8"?>
    <android.support.v7.widget.CardView xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:card_view="http://schemas.android.com/apk/res-auto"
        android:id="@+id/card_view"
        android:layout_gravity="center"
        android:layout_width="fill_parent"
        android:layout_height="120dp" >

        <TextView
            android:clickable="true"
            android:id="@+id/name"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_marginBottom="10dp"
            android:layout_marginRight="10dp"
            android:gravity="center"
            android:textSize="24sp" />

    </android.support.v7.widget.CardView>

