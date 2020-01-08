---
layout: post
title:  "Android MVVM架构和Data binding"
date:   "2018-12-25"
categories: android
---

## 概念：

* Model: 负责数据实现和逻辑处理
* View: 对应于Activity和XML，负责View的绘制以及与用户交互
* ViewModel: 创建关联，将model和view绑定起来,如此之后，我们model的更改，通过viewmodel反馈给view,从而自动刷新界面

跟MVP模式类似，ViewModel相当于Presenter。不同的是ViewModel跟View和Model双向绑定，View改变时ViewModel通知Model更新数据，Model数据更新的时候View跟着改变。

Model   ←→  ViewModel   ←→  View

在Android上使用Databinding作为MVVM的实现方式。Databinding是一种框架，MVVM是一种架构，一种模式。DataBinding是一个实现数据和UI绑定的框架，是实现MVVM模式的工具，而MVVM中的VM（ViewModel）和View可以通过DataBinding来实现数据绑定（目前已支持双向绑定）。绑定之后对Model的数据修改就能直接反映到View上。

MVP和MVVM的区别

* view model这一层替换掉了presenter这个中间层。
* Presenter会有一个view的引用，但是viewmodel没有。
* Presenter通过传统的方式，用激活事件的方法来更新视图。
* view model会发送数据流。
* Presenter和view是1对1的关系。
* 而view和view model是一对多的关系。
* view model不知道view在聆听。

在安卓编程中有两种方式实现mvvm。    
一种是data binding即数据绑定。    
另一种是RxJava。    

--------

## 数据绑定的方法

实现数据变化自动驱动 UI 刷新的方式有三种：BaseObservable、ObservableField、ObservableCollection

### BaseObservable

model类继承BaseObservable方法，BaseObservable 提供了 notifyChange() 和 notifyPropertyChanged() 两个方法，前者会刷新所有的值域，后者则只更新对应 BR 的 flag，该 BR 的生成通过注释 @Bindable 生成，可以通过 BR notify 特定属性关联的视图

### ObservableField

继承于 Observable 类相对来说限制有点高，且也需要进行 notify 操作，因此为了简单起见可以选择使用 ObservableField。ObservableField 可以理解为官方对 BaseObservable 中字段的注解和刷新等操作的封装，官方原生提供了对基本数据类型的封装，例如 ObservableBoolean、ObservableByte、ObservableChar、ObservableShort、ObservableInt、ObservableLong、ObservableFloat、ObservableDouble 以及 ObservableParcelable ，也可通过 ObservableField 泛型来申明其他类型

    public class ObservableGoods {
        private ObservableField<String> name;
        private ObservableFloat price;
        private ObservableField<String> details;

        public ObservableGoods(String name, float price, String details) {
            this.name = new ObservableField<>(name);
            this.price = new ObservableFloat(price);
            this.details = new ObservableField<>(details);
        }
        ```
    }

### ObservableCollection

dataBinding 也提供了包装类用于替代原生的 List 和 Map，分别是 ObservableList 和 ObservableMap,当其包含的数据发生变化时，绑定的视图也会随之进行刷新

layout.xml

    <?xml version="1.0" encoding="utf-8"?>
    <layout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools">

        <data>
            <import type="android.databinding.ObservableList"/>
            <import type="android.databinding.ObservableMap"/>
            <variable
                name="list"
                type="ObservableList&lt;String&gt;"/>
            <variable
                name="map"
                type="ObservableMap&lt;String,String&gt;"/>
            <variable
                name="index"
                type="int"/>
            <variable
                name="key"
                type="String"/>
        </data>

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical"
            tools:context="com.leavesc.databinding_demo.Main12Activity">

            <TextView
                ···
                android:padding="20dp"
                android:text="@{list[index],default=xx}"/>

            <TextView
                ···
                android:layout_marginTop="20dp"
                android:padding="20dp"
                android:text="@{map[key],default=yy}"/>

            <Button
                ···
                android:onClick="onClick"
                android:text="改变数据"/>

        </LinearLayout>
    </layout>

Activity.java

    private ObservableMap<String, String> map;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        ActivityMain12Binding activityMain12Binding = DataBindingUtil.setContentView(this, R.layout.activity_main12);
        map = new ObservableArrayMap<>();
        map.put("name", "leavesC");
        map.put("age", "24");
        activityMain12Binding.setMap(map);
        ObservableList<String> list = new ObservableArrayList<>();
        list.add("Ye");
        list.add("leavesC");
        activityMain12Binding.setList(list);
        activityMain12Binding.setIndex(0);
        activityMain12Binding.setKey("name");
    }

    public void onClick(View view) {
        map.put("name", "leavesC,hi" + new Random().nextInt(100));
    }

---------

## 事件绑定



--------

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

--------

## BindingAdapter

BindingAdapter的工作机制。认为BindingAdapter实现了数据绑定中，对于某一个绑定值（被观察者）改变后，绑定一个方法，然后执行相应逻辑。

### 适用情况

* 属性在类中没有对应的setter，如ImageView的android:src，ImageView中没有setSrc()方法，
* 属性在类中有setter，但是接收的参数不是自己想要的，如android:background属性，对应的setter是setBackgound(drawable)，但是我想传一个int类型的id进去，这时候android:background = “@{imageId}”就不行。
* 没有对应的属性，但是却要实现相应的功能。

这时候可以使用@BindingAdapter来定义方法，解决上面的问题。

### 特点

* 作用于方法
* 它定义了xml的属性赋值的java实现
* 方法必须为公共静（public static）方法，可以有一到多个参数。

### 使用方法

在项目任意地方定义Adapter方法，关键是@BindingAdapter("attribute_name")注解，定义为哪一个属性绑定处理方法    
方法参数包括View的引用其他的可以自定义

    public class BindingAdpterUtils {
        @BindingAdapter("src")
        public static void setSrc(ImageView view, int resId) {
            view.setImageResource(resId);
        }
    }

这样在layou xml中就可以绑定值，View的属性改变的时候自动调用定义的Adapter方法

    <ImageView
        app:src="@{imageId}"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content">
    </ImageView>


