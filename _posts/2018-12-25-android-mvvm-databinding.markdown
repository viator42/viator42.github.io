---
layout: post
title:  "Android MVVM架构和Data binding"
date:   "2018-12-25"
categories: android
---

## 概念：

* Model: 数据模型,业务逻辑和验证逻辑
* View: 画面展示
* ViewModel: View和Model之间的连接

跟MVP模式类似，ViewModel相当于Presenter。不同的是ViewModel跟View和Model双向绑定，View改变时ViewModel通知Model更新数据，Model数据更新的时候View跟着改变。

Model   ←→  ViewModel   ←→  View

## 使用

启用data binding

    android {
        。。。
        dataBinding {
            enabled = true
        }
    }

## 简单Databing交互

创建model类（POJO）

    public class Swordman extends BaseObservable {
        private String name;
        private String level;

        @Bindable
        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
            notifyPropertyChanged(BR.name);
        }

        @Bindable
        public String getLevel() {
            return level;
        }

        public void setLevel(String level) {
            this.level = level;
            notifyPropertyChanged(BR.level);
        }
    }

修改activity_binding_tester.xml文件

    <layout xmlns:android="http://schemas.android.com/apk/res/android" >
        <data>
            <variable
                name="swordman"
                type="com.viator42.ugo.module.dev.Swordman" />
        </data>
        。。。
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{swordman.name}" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{swordman.level}" />
        。。。
        <EditText
            android:id="@+id/swordman_name"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:ems="10"
            android:inputType="textPersonName"
            android:text="@={swordman.name}" />

        <EditText
            android:id="@+id/swordman_level"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:ems="10"
            android:inputType="textPersonName"
            android:text="@={swordman.level}" />
        。。。
        <Button
            android:id="@+id/fetch_data"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="fetch" />
    </layout>

使用@{swordman.name}把model值绑定到view上

重新build就能在/build/genetated目录下生成辅助类 ActivityBindingTesterBinding.java

BindingTesterActivity.java

public class BindingTesterActivity extends AppCompatActivity {
    @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_binding_tester);
        
            //创建binding
            ActivityBindingTesterBinding binding = DataBindingUtil.setContentView(this, R.layout.activity_binding_tester);
            
            Swordman swordman = new Swordman();
            swordman.setName("John");
            swordman.setLevel("36");;

            //model类赋值给binding对象
            binding.setSwordman(swordman);

            //给view中的组件设置事件
            binding.fetchData.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    CommonUtils.makeToast(BindingTesterActivity.this, "button clicked");
                    swordman.setName("harry");
                    swordman.setLevel("19");
                }
            });

        }

    

}

### 双向绑定

设置双向绑定 在get方法上添加@Bindable注解，set方法上调用notifyPropertyChanged();    
这样binding中的数据修改的时候就会实时对应到view上面    

--------

## 使用RecyclerView

activity的样式xml文件，注意这里需要嵌套在<layout>里面否则不会生成辅助类

activity_binding_list_tester.xml

    <?xml version="1.0" encoding="utf-8"?>
    <layout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto">
        <FrameLayout
            xmlns:tools="http://schemas.android.com/tools"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            tools:context=".module.dev.BindingListTesterActivity">

            <android.support.v7.widget.RecyclerView
                android:id="@+id/empolyee_list"
                android:layout_width="match_parent"
                android:layout_height="match_parent">
            </android.support.v7.widget.RecyclerView>

        </FrameLayout>

    </layout>

列表项的xml文件,需要定义<data>绑定对象

item_employee.xml

    <?xml version="1.0" encoding="utf-8"?>
    <layout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto" >

        <data>
            <import type="com.viator42.ugo.module.dev.Employee" />
            <variable
                name="employee"
                type="Employee" />
        </data>

        <LinearLayout
            android:layout_width="match_parent"
            android:orientation="horizontal"
            android:layout_height="wrap_content">

            <ImageView
                android:id="@+id/headimg"
                android:layout_width="48dp"
                android:layout_height="48dp"
                android:layout_weight="1" />

            <TextView
                android:id="@+id/name"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:text="@{employee.name}" />

            <TextView
                android:id="@+id/age"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:text="@{String.valueOf(employee.age)}" />
        </LinearLayout>

    </layout>

Adapter类

BindingListAdapter.xml

    public class BindingListAdapter extends RecyclerView.Adapter<BindingListAdapter.BindingListViewHolder> {
        private List<Employee> list;

        public BindingListAdapter(List<Employee> list) {
            this.list = list;
        }

        @NonNull
        @Override
        public BindingListViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
            ItemEmployeeBinding binding = DataBindingUtil.inflate(
                    LayoutInflater.from(parent.getContext()), R.layout.item_employee, parent, false
            );

            return new BindingListViewHolder(binding);
        }

        @Override
        public void onBindViewHolder(@NonNull BindingListViewHolder holder, int position) {
            Employee employee = list.get(position);
            holder.getBinding().setEmployee(employee);
        }

        @Override
        public int getItemCount() {
            return list.size();
        }

        public class BindingListViewHolder extends RecyclerView.ViewHolder {
            ItemEmployeeBinding binding;

            public BindingListViewHolder(ViewDataBinding binding) {
                super(binding.getRoot());
                this.binding = (ItemEmployeeBinding) binding;
            }

            public ItemEmployeeBinding getBinding() {
                return binding;
            }
        }

    }

Activity类

BindingListTesterActivity.java

    public class BindingListTesterActivity extends AppCompatActivity {
        private ActivityBindingListTesterBinding binding;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_binding_list_tester);

            binding = DataBindingUtil.setContentView(this, R.layout.activity_binding_list_tester);

            initRecyclerView();
        }

        private void initRecyclerView() {
            LinearLayoutManager layoutManager = new LinearLayoutManager(this);
            binding.empolyeeList.setLayoutManager(layoutManager);
            BindingListAdapter adapter = new BindingListAdapter(initData());
            binding.empolyeeList.setAdapter(adapter);
        }

        private List<Employee> initData() {
            List<Employee> list = new ArrayList<>();
            for(int a=0; a<10; a++) {
                list.add(new Employee("/haedimg.jpg", "John", 20+a));
            }

            return list;
        }

    }
