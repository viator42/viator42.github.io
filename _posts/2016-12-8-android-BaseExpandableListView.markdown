---
layout: post
title:  "Android二级列表BaseExpandableList"
date:   2016-12-08
categories: android
---

首先定义ListView    

    <ExpandableListView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/list_view" />


新建BaseExpandableListAdapter    
必须重载的方法    
getGroupCount          返回分组的个数    
getChildrenCount       返回子列表的个数    
getGroup                    返回分组对象    
getChild                      返回子列表对象    
getGroupId                  返回分组的Id    
getChildId                    返回子列表对象的Id    
getGroupView            分组的View绘制    
getChildView               子列表的View绘制    

#### Adapter.java

    public class ListViewAdapter extends BaseExpandableListAdapter {
        private Context context;
        private ArrayList<String> titleList;
        private ArrayList<List<String>> childrenList;

        public ListViewAdapter(Context context, ArrayList<String> titleList, ArrayList<List<String>> childrenList)
        {     
            this.context = context;
            this.titleList = titleList;
            this.childrenList = childrenList;
        }

        @Override
        public int getGroupCount() {
            return titleList.size();
        }

        @Override
        public int getChildrenCount(int groupPosition) {
            return childrenList.get(groupPosition).size();
        }

        @Override
        public Object getGroup(int groupPosition) {
            return titleList.get(groupPosition);
        }

        @Override
        public Object getChild(int groupPosition, int childPosition) {
            return childrenList.get(groupPosition).get(childPosition);
        }

        @Override
        public long getGroupId(int groupPosition) {
            return groupPosition;
        }

        @Override
        public long getChildId(int groupPosition, int childPosition) {
            return childPosition;
        }

        @Override
        public boolean hasStableIds() {
            return true;
        }

        @Override
        public View getGroupView(int groupPosition, boolean isExpanded, View convertView, ViewGroup parent) {
            //获取文本
            String text = titleList.get(groupPosition);
            if(convertView == null){
                //组列表界面只有一个文本框，直接生成
                convertView = new TextView(context);
                //设定界面，AbsListView:用于实现条目的虚拟列表的基类
                AbsListView.LayoutParams lp = new AbsListView.LayoutParams(
                        ViewGroup.LayoutParams.FILL_PARENT, 60);
                ((TextView) convertView).setGravity(Gravity.CENTER_VERTICAL | Gravity.LEFT); //文本框放在中央
                convertView.setPadding(45, 0, 0, 0);                              //设置文本里那个下拉的图标远一点
                convertView.setLayoutParams(lp);
            }
            ((TextView) convertView).setText(text);
            return convertView;
        }

        @Override
        public View getChildView(int groupPosition, int childPosition, boolean isLastChild, View convertView, ViewGroup parent) {
            //子列表控件通过界面文件设计
            if(convertView ==null){//convert在运行中会重用，如果不为空，则表明不用重新获取
                LayoutInflater layoutInflater;//使用这个来载入界面
                layoutInflater = LayoutInflater.from(context);
                convertView = layoutInflater.inflate(R.layout.list_item, null);
            }
            TextView tv = (TextView)convertView.findViewById(R.id.text);
            String text = childrenList.get(groupPosition).get(childPosition);
            tv.setText(text);
            //获取文本控件，并设置值
            return convertView;
        }

        @Override
        public boolean isChildSelectable(int groupPosition, int childPosition) {
            return false;
        }
    }

#### Activity.java

    public class MainActivity extends AppCompatActivity {
        private ExpandableListView expandableListView;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
            setSupportActionBar(toolbar);

            expandableListView = (ExpandableListView) findViewById(R.id.list_view);

            ArrayList<String> titleList = new ArrayList<String>();
            ArrayList<List<String>> childrenList = new ArrayList<List<String>>();

            titleList.add("AAA");
            titleList.add("BBB");
            titleList.add("CCC");

            ArrayList<String> cat1 = new ArrayList<String>();
            cat1.add("AAA_1");
            cat1.add("AAA_2");
            cat1.add("AAA_3");
            childrenList.add(cat1);

            ArrayList<String> cat2 = new ArrayList<String>();
            cat2.add("BBB_1");
            cat2.add("BBB_2");
            cat2.add("BBB_3");
            childrenList.add(cat2);

            ArrayList<String> cat3 = new ArrayList<String>();
            cat3.add("CCC_1");
            cat3.add("CCC_2");
            cat3.add("CCC_3");
            childrenList.add(cat3);

            ListViewAdapter listViewAdapter = new ListViewAdapter(MainActivity.this, titleList, childrenList);
            expandableListView.setAdapter(listViewAdapter);

            //展开所有组,也可以单独选择某一组展开
            for(int a=0; a<titleList.size(); a++) {
                expandableListView.expandGroup(a);
            }

            expandableListView.setGroupIndicator(null);     //设置组展开标记图,设为null表示没有
            expandableListView.setOnGroupClickListener(new ExpandableListView.OnGroupClickListener() {
                @Override
                public boolean onGroupClick(ExpandableListView parent, View v, int groupPosition, long id) {
                    return true;
                }
            });

            //分组项点击事件
            expandableListView.setOnGroupClickListener(new ExpandableListView.OnGroupClickListener() {
                @Override
                public boolean onGroupClick(ExpandableListView parent, View v, int groupPosition, long id) {
                    return false;
                }
            });


            //子列表项点击事件
            expandableListView.setOnChildClickListener(new ExpandableListView.OnChildClickListener() {
                @Override
                public boolean onChildClick(ExpandableListView parent, View v, int groupPosition, int childPosition, long id) {
                    return false;
                }
            });

        }
    }
