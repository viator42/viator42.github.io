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

## 拦截器

跳转过程中对请求进行拦截，常用的用法是未登录的时候跳转到登录页面。

### 步骤

1，新建拦截器类，用于确定哪些页面需要拦截，init()方法是初始化，只在应用启动的时候调用一次，process()方法进行拦截。多个拦截器的时候priority确定顺序，越小的先执行。

    * callback.onInterrupt(null)    进行拦截
    * callback.onContinue(postcard) 继续运行

    @Interceptor(priority = 8)
    public class LoginInterceptor implements IInterceptor {
        AppContext appContext;

        @Override
        public void process(Postcard postcard, InterceptorCallback callback) {
            String path = postcard.getPath();
            if (CommonUtils.isValueEmpty(appContext.token)) {
                // 如果没有登录
                switch (path) {
                    // 需要登录的直接拦截下来
                    case RouterPath.PATH_WISHLIST:
                    case RouterPath.PATH_MESSAGE:
                    case RouterPath.PATH_MESSAGE_LIST:
                    case RouterPath.PATH_ORDERS:
                    case RouterPath.PATH_CUSTOMER_SERVICE:
                    case RouterPath.PATH_CART:

                        callback.onInterrupt(null);
                        break;
                    //
                    default:
                        callback.onContinue(postcard);
                        break;
                }
            } else {
                // 如果已经登录不拦截
                callback.onContinue(postcard);
            }
        }

        @Override
        public void init(Context context) {
            appContext = (AppContext) context.getApplicationContext();
        }
    }

2，路由跳转的回调，定义被拦截之后执行的操作。

    public class LoginNavigationCallbackImpl implements NavigationCallback {
        /**
        * 找到了
        */
        @Override
        public void onFound(Postcard postcard) {

        }

        /**
        * 找不到了
        */
        @Override
        public void onLost(Postcard postcard) {

        }

        /**
        * 跳转成功了
        */
        @Override
        public void onArrival(Postcard postcard) {

        }

        @Override
        public void onInterrupt(Postcard postcard) {
            String path = postcard.getPath();
            Bundle bundle = postcard.getExtras();
            // 被登录拦截了下来了
            // 需要调转到登录页面，把参数跟被登录拦截下来的路径传递给登录页面，登录成功后再进行跳转被拦截的页面
            ARouter.getInstance().build(RouterPath.PATH_LOGIN)
                    .with(bundle)
                    .navigation();
        }
    }

3，启动 Activity

    ARouter.getInstance().build(RouterPath.PATH_CART)
        ·.navigation(WishlistActivity.this, new LoginNavigationCallbackImpl());