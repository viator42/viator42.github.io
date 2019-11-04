---
layout: post
title:  "Arouter笔记"
date:   "2019-1-4"
categories: Android
---

## 使用实例

0.添加第三方库

    dependencies {
        。。。
        implementation 'com.alibaba:arouter-api:1.5.0'
        annotationProcessor 'com.alibaba:arouter-compiler:1.2.2'
    }

1.在尽量早的地方初始化，比如Application类中

    public class AppContext extends Application {
        @Override
        public void onCreate() {
            super.onCreate();

            ARouter.openDebug();
            ARouter.init(this);
        }
    }

2.跳转的目标Activity添加注解，包括url参数

    @Route(path = "/app/test/basic")
    public class RouteDestActivityActivity extends AppCompatActivity {
        TextView routeMsgTextView;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_route_dest_activity);

            routeMsgTextView = findViewById(R.id.route_msg);

            Intent intent = getIntent();
            int testInt = intent.getIntExtra("testInt", 0);
            String testStr = intent.getStringExtra("testString");

            routeMsgTextView.setText(String.valueOf(testInt) + "  " + testStr);

        }
    }

3.跳转

    ARouter.getInstance().build("/app/test/basic")
        .withInt("testInt", 666)
        .withString("testString", "this is testString")
        .navigation();

