---
layout: post
title:  "Android 5.0 Material Design 笔记"
date:   2016-01-02
categories: android
---

### Android Design Support Library笔记

首先打开 Android SDK Manager安装支持库Android Support Library.
然后再build.gradle中添加

    dependencies {
        compile fileTree(dir: 'libs', include: ['*.jar'])
        testCompile 'junit:junit:4.12'
        compile 'com.android.support:appcompat-v7:23.1.1'
        compile 'com.android.support:design:23.1.1'

    }

Snackbar，用来代替Toast的提示框。

    Snackbar.make(view, "Replace with your own action", Snackbar.LENGTH_LONG)
                            .setAction("Action", null).show();

Floating Action Button

用于展示页面主操作的按钮，一般在右下角

Layout    

    <android.support.design.widget.FloatingActionButton
        android:id="@+id/fab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="end|bottom"
        android:layout_margin="@dimen/fab_margin"
        android:src="@drawable/ic_done"/>
        
Activity    

    private FloatingActionButton fab;
    fab = (FloatingActionButton) findViewById(R.id.fab);
        fab.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Snackbar.make(view, "Replace with your own action", Snackbar.LENGTH_LONG)
                        .setAction("Action", null).show();
            }
        });


NavigationView

侧边弹出的导航抽屉，分为

使用方法

将layout的根view定义为DrawerLayout，然后嵌套NavigationView

Activity_Layout

NavigationView分为两部分，包括可自定义样式的headerLayout，和下面的menu。必须指定这两个的样式属性

    <android.support.v4.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/drawer_layout"
        android:fitsSystemWindows="true">

        <include layout="@layout/include_mainpage"/>

        <android.support.design.widget.NavigationView
        android:id="@+id/nav_view"
        android:layout_height="match_parent"
        android:layout_width="wrap_content"
        android:layout_gravity="start"
        android:fitsSystemWindows="true"
        app:headerLayout="@layout/nav_header"
        app:menu="@menu/drawer_view" />

    </android.support.v4.widget.DrawerLayout>

nav_header layout

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:orientation="vertical"
        android:layout_width="match_parent"
        android:layout_height="190dp"
        android:background="?attr/colorPrimaryDark"
        android:theme="@style/ThemeOverlay.AppCompat.Dark">

        <ImageView
            android:layout_width="48dp"
            android:layout_height="wrap_content"
            android:id="@+id/imageView"
            android:src="@drawable/logo"
            android:layout_gravity="center_horizontal" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textAppearance="?android:attr/textAppearanceMedium"
            android:text="未命名"
            android:id="@+id/username"
            android:layout_below="@+id/logo"
            android:layout_centerHorizontal="true"
            android:layout_marginTop="8dp"
            android:layout_marginLeft="13dp"
            android:layout_gravity="center_horizontal" />

    </LinearLayout>

drawer_view layout

    <?xml version="1.0" encoding="utf-8"?>
    <menu xmlns:android="http://schemas.android.com/apk/res/android">
        <group android:checkableBehavior="single">
            <item
                android:id="@+id/nav_home"
                android:icon="@drawable/menu_item"
                android:title="Home" />
            <item
        </group>

        <item android:title="Sub items">
            <menu>
                <item
                    android:icon="@drawable/menu_item"
                    android:title="Sub item 1" />
                <item
                    android:icon="@drawable/menu_item"
                    android:title="Sub item 2" />
            </menu>
        </item>

    </menu>

Activity.java

Activity类继承AppCompatActivity。

    private DrawerLayout mDrawerLayout;
    final ActionBar ab = getSupportActionBar();
    ab.setHomeAsUpIndicator(R.drawable.ic_menu);
    ab.setDisplayHomeAsUpEnabled(true);

    mDrawerLayout = (DrawerLayout) findViewById(R.id.drawer_layout);

    NavigationView navigationView = (NavigationView) findViewById(R.id.nav_view);
    if (navigationView != null) {
        setupDrawerContent(navigationView);
    }    

    
## TabLayout

layout

    <android.support.design.widget.TabLayout
        android:id="@+id/tabs"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />

    <android.support.v4.view.ViewPager
        android:id="@+id/viewpager"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />
    

Activity.java

    ViewPager viewPager = (ViewPager) findViewById(R.id.viewpager);
    if (viewPager != null) {
        Adapter adapter = new Adapter(getSupportFragmentManager());
        adapter.addFragment(new ViewPagerItemFragment(), "Category 1");
        adapter.addFragment(new ViewPagerItemFragment(), "Category 2");
        adapter.addFragment(new ViewPagerItemFragment(), "Category 3");
        viewPager.setAdapter(adapter);
        
    }

    TabLayout tabLayout = (TabLayout) findViewById(R.id.tabs);
    tabLayout.setupWithViewPager(viewPager);

    
    static class PagerAdapter extends FragmentPagerAdapter {
        private final List<Fragment> mFragments = new ArrayList<>();
        private final List<String> mFragmentTitles = new ArrayList<>();

        public PagerAdapter(FragmentManager fm) {
            super(fm);
        }

        public void addFragment(Fragment fragment, String title) {
            mFragments.add(fragment);
            mFragmentTitles.add(title);
        }

        @Override
        public Fragment getItem(int position) {
            return mFragments.get(position);
        }

        @Override
        public int getCount() {
            return mFragments.size();
        }

        @Override
        public CharSequence getPageTitle(int position) {
            return mFragmentTitles.get(position);
        }
    }
    

## CoordinatorLayout

layout

根元素使用CoordinatorLayout，里面嵌套AppBarLayout作为展开时的toolbar样式。AppBarLayout中再嵌套CollapsingToolbarLayout。


    <android.support.design.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:fitsSystemWindows="true"
        tools:context="com.brand.advancenote.Main2Activity">

        <android.support.design.widget.AppBarLayout
            android:layout_width="match_parent"
            android:layout_height="256dp"
            android:theme="@style/AppTheme.AppBarOverlay">

            <android.support.design.widget.CollapsingToolbarLayout
                android:id="@+id/collapsing_toolbar"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                app:layout_scrollFlags="scroll|exitUntilCollapsed"
                android:fitsSystemWindows="true"
                app:contentScrim="?attr/colorPrimary"
                app:expandedTitleMarginStart="48dp"
                app:expandedTitleMarginEnd="64dp">

                <android.support.v7.widget.Toolbar
                    android:id="@+id/toolbar"
                    android:layout_width="match_parent"
                    android:layout_height="?attr/actionBarSize"
                    android:background="?attr/colorPrimary"
                    app:popupTheme="@style/AppTheme.PopupOverlay" />

                </android.support.design.widget.CollapsingToolbarLayout>


        </android.support.design.widget.AppBarLayout>

        <!- 自定义的layout内容 ->
        <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior"
        tools:context="com.brand.advancenote.Main2Activity"
        tools:showIn="@layout/activity_main2">

        <android.support.design.widget.FloatingActionButton
            android:id="@+id/fab"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="bottom|end"
            android:layout_margin="@dimen/fab_margin"
            android:src="@android:drawable/ic_dialog_email" />

    </android.support.design.widget.CoordinatorLayout>
    
Activity.java

    private Toolbar toolbar;
    private CollapsingToolbarLayout collapsingToolbar;
    
    toolbar = (Toolbar) findViewById(R.id.toolbar);
    setSupportActionBar(toolbar);
    getSupportActionBar().setDisplayHomeAsUpEnabled(true);

    collapsingToolbar = (CollapsingToolbarLayout) findViewById(R.id.collapsing_toolbar);
    collapsingToolbar.setTitle("collapse tollbar");
    
    