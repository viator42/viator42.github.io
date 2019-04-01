---
layout: post
title:  "Android View相关"
date:   2019-03-25
categories: android
---

## View绘制的过程

各种组件都是ViewGroup的子类,ViewGroup是View的子类,view结构如下

![view结构](http://zhaowen.io/post/Android_Dive_Deep_In_View/decor_view.jpg)

Android的UI界面是一个树形结构,View的嵌套.子View在父View中，这些View都经过一个相同的流程最终显示到屏幕上    
每一个View的绘制都有Measure,Layout,Draw三个步骤的过程

measure -> onMeasure() -> layout -> onLayout() -> draw -> onDraw()

* Measure()    
测量视图的大小

* Layout()    
计算视图的位置

* Draw()    
视图绘制到屏幕上

meaure会经历performMeaure -> meaure -> onMeaure 的调用过程,其他两个也是同样    
视图绘制过程中会回调onMeasure(), onLayout(), onDraw()方法,自定义View的时候需要重写这三个方法    

    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
    }

    @Override
    protected void onLayout(boolean changed, int l, int t, int r, int b) {
        super.onLayout(changed, l, t, r, b);
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
    }

点击屏幕的事件顺序 DOWN->UP
滑动屏幕的事件顺序 DOWN->MOVE...->UP

widthMeasureSpec和heightMeasureSpec是测量的view的尺寸    
其中高两位是模式    
模式分为一下三种    

* EXACTLY 当View的layout_width和layout_height设置的是固定值的时候
* AT_MOST 控件的layout_width和layout_height设置成wrap_content的时候,控件的大小随着子控件的大小变化.
* UNSPECIFIED 不指定测量的大小

__重写onMeasure__

    //widthMeasureSpec和heightMeasureSpec是当前的测量宽高
    onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        //getMode用来获取测量模式
        int wMode = MeasureSpec.getMode(widthMeasureSpec);
        int hMode = MeasureSpec.getMode(widthMeasureSpec);

        //getSize用来从Spec中获取尺寸
        int width = MeasureSpec.getSize(widthMeasureSpec);
        int height = MeasureSpec.getSize(heightMeasureSpec);

        getPaddingLeft/Right/Top/Bottom获取Padding尺寸,需要在view中手动减去padding否则设置的padding值无效

        //mode和size重新组合成Spec
        int resultWidthMeasureSpec = MeasureSpec.makeMeasureSpec(width, wMode);
        int resultHeightMeasureSpec = MeasureSpec.makeMeasureSpec(height, hMode);

        //返回设置的尺寸
        super.onMeasure(resultWidthMeasureSpec, resultHeightMeasureSpec);
    }

__ViewGroup的Measure过程__

ViewGroup除了测量自己的尺寸还要测量所有子元素的尺寸.

    getChildCount();    //获取子元素的个数
    View view = getChildAt(index); //获取子元素

__重写onLayout__

onLayout方法确定ViewGroup所有的子元素的位置

__自定义View需要注意的地方__

* View支持wrap_content    
* View支持padding,就是设置padding值的时候onMeasure函数返回的尺寸减去padding值    
* 使用post方法来传递信息,尽量不使用Handler   
* onDetatchedFromVindow方法加载动画和线程,onDetachedFromWindow方法停止动画和线程,防止一直占用系统资源导致OOM   
* View有滑动时处理滑动冲突

---------

## View的事件分发机制

每一个view都有三个方法来处理点击事件

* dispatchTouchEvent 用于事件的分发,如果传递到当前view,一定会调用这个   
* onInterruptTouchEvent 用于判断是否拦截某个事件,被dispatchTouchEvent调用
* onTouchEvent 拦截之后处理点击事件,被onInterruptTouchEvent调用

点击事件是递归传递的按顺序为 Activity -> Window -> View,从顶级ViewGroup一级一级向下直到目标view进行处理.    
每个view收到点击事件后会寻找遍历自己的子view分发事件.如果子view能处理则由子view处理,没有的话就由当前view进行处理.    
判断的依据是 1.能处理click事件 2.点击的坐标在view的范围内 3.view设置的允许响应事件enable=true.   

如果View设置了touchListener,onTouch的返回值(boolean)决定onTouchEvent是否会被回调.如果onTouch返回了false,则会调用上级的onTouchEvent

    @Override
    public boolean dispatchTouchEvent(MotionEvent ev) {
        return super.dispatchTouchEvent(ev);
    }

MotionEvent 是手指触控屏幕产生的一系列事件, 主要有以下几种

* ACTION_DOWN    
* ACTION_UP    
* ACTION_MOVE

--------

### View滑动

* getX（）：获取点击事件距离控件左边的距离，即视图坐标。
* getY（）：获取点击事件距离控件顶边的距离，即视图坐标。
* getRawX（）：获取点击事件距离整个屏幕左边的距离，即绝对坐标。
* getRawY（）：获取点击事件距离整个屏幕顶边的距离，即绝对坐标。

--------

### 处理滑动冲突的问题

* 内外滑动方向一致的情况
* 内外滑动方向不一致的情况

__解决方法__

1. 重写父容器的onInterruptTouchEvent方法, 判断是否拦截事件.    
2. 父容器不拦截任何的方法,

解决滑动冲突的一个示例

    public class ViewPagerX extends ViewPager {
        private int lastX;
        private int lastY;

        public ViewPagerX(Context context, AttributeSet attrs) {
            super(context, attrs);
        }

        @Override
        public boolean dispatchTouchEvent(MotionEvent ev) {
            int x = (int) ev.getX();
            int y = (int) ev.getY();

            switch (ev.getAction()) {
                case MotionEvent.ACTION_DOWN:
                    super.requestDisallowInterceptTouchEvent(true);
                    break;

                case MotionEvent.ACTION_MOVE:
                    int deltaX = x - lastX;
                    int deltaY = y - lastY;

                    if (Math.abs(deltaX) < Math.abs(deltaY)) {
                        super.requestDisallowInterceptTouchEvent(false);
                    }
                    break;

                case MotionEvent.ACTION_UP:
                    super.requestDisallowInterceptTouchEvent(false);
                    break;
            }
            lastX = x;
            lastY = y;

            return super.dispatchTouchEvent(ev);
        }
    }

--------

## 自定义控件的实现过程

* 自定义属性的声明和获取    
    分析需要的自定义属性
    在res/values/attrs.xml定义声明
    在layout文件中进行使用
    在View的构造方法中进行获取

* 测量onMeasure

* 布局onLayout(ViewGroup)

* 绘制onDraw

* onTouchEvent

* onInterceptTouchEvent(ViewGroup)

* 状态的恢复与保存

---------


