---
layout: post
title:  "Dagger2 笔记"
date:   "2018-12-18"
categories: android
---

## 概念

Dagger是Android使用的依赖注入框架

控制反转（Inversion of Control，缩写为IoC），是面向对象编程中的一种设计原则，可以用来减低计算机代码之间的耦合度。其中最常见的方式叫做依赖注入（Dependency Injection，简称DI），还有一种方式叫“依赖查找”（Dependency Lookup）。通过控制反转，对象在被创建的时候，由一个调控系统内所有对象的外界实体，将其所依赖的对象的引用传递给它。也可以说，依赖被注入到对象中.

通俗的来讲呢,就是一个类中需要依赖其他对象时,不需要你亲自为那些需要依赖的对象赋值,为那些对象赋值的操作交给了IOC框架.

--------

## 注解

### @Inject

标记被注解的对象

1. 标记构造器

告诉Dagger2可以使用这个构造器来构筑对象，如果有多个构造方法，只能标注其中一个，不能标注多个

2. 标记属性

这个属性在所属类被实例化的时候被创建，被标记的属性不能为private

3. 方法注入

标注在public方法上，Dagger2会在构造器执行之后立即调用这个方法

## 使用方法

### 使用@Inject和@Component完成注入

需要注入依赖的地方,@Inject标记在构造方法上,通过Dagger创建类实例的时候就通过调用这个构造方法    
也可以把@Inject标记在类成员上,如果只标记类成员不标记构造方法的话类将不会实例化    

    public class Cloth {
        public String color;

        @Inject
        public Cloth() {
        }

        @Override
        public String toString() {
            return "布料的颜色是" + color;
        }
    }

@Component 可以理解成为注入器,是@Inject和@Module的桥梁,从@Module中获取依赖并注入@Inject

    @Component
    public interface MainActivityComponent {
        void inject(MainActivity mainActivity);
    }

被注入的类

    public class MainActivity {
        MainActivityComponent.create().inject(this);
    }

--------

### 另一种方法是使用@Module和@Component注入

如果注入的对象属于第三方，无法标记@Inject，就需要@Module类手动定义对象的实例化方式，里面的方法用@Provides修饰,每一个provide方法用于实例化一个类    
注入的对象是抽象类也需要使用@Module生成示例

    @Module
    public class ClothModule {
        @Provides
        static Cloth provideCloth() {
            Cloth cloth = new Cloth();
            cloth.color = "red";
            return cloth;
        }
    }

@Component的写法，需要指定module类

    @Component(modules = ClothModule.class)
    public interface MainActivityComponent {
        void inject(MainActivity mainActivity);
    }

被注入的类

    public class MainActivity {
        MainActivityComponent.create().inject(this);
    }

---------

### @Named

@Module多个方法生成同样的Model的时候，需要添加@Named注解来标识使用哪一个方法生成对象

---------

## Android MVP架构集成Dagger2

__在Application类中定义获取Context的Module__

AppModule.java

    @Module
    public class AppModule {
        private Context context;

        public AppModule(Context context) {
            this.context = context;
        }

        @Provides
        public Context provideContext(){
            return this.context;
        }
    }

AppComponent.java

    @Component(modules = AppModule.class)
    public interface AppComponent {
        Context getContext();
    }

AppContext.java

    public class AppContext extends Application {
        public AppComponent appComponent;

        @Override
        public void onCreate() {
            super.onCreate();

            appComponent = DaggerAppComponent.builder().appModule(new AppModule(this)).build();
        }
    }

__App中每个模块的定义__

DevPresenter.java

    public class DevPresenter implements DevContract.Presenter {
        DevContract.View view;

        @Inject
        public DevPresenter(DevContract.View view) {
            this.view = view;
        }

        @Override
        public String iamHere() {
            return "Dev Presenter loaded";
        }
    }

PresenterModule.java

    @Module
    public class PresenterModule {
        private DevContract.View view;

        public PresenterModule(DevContract.View view) {
            this.view = view;
        }

        @Provides
        DevContract.View providePresenter() {
            return view;
        };
    }

DevActivityComponent.java

    @Component(dependencies = AppComponent.class, modules={PresenterModule.class})
    public interface DevActivityComponent {
        void inject(DevActivity activity);
    }

__目标类完成注入__

AppCompatActivity.java

    public class DevActivity extends AppCompatActivity implements DevContract.View {
        private AppContext appContext;

        @Inject
        DevPresenter devPresenter;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_dev);

            appContext = (AppContext) getApplicationContext();
            DaggerDevActivityComponent.builder()
                .presenterModule(new PresenterModule(this)) //初始化用到的Module
                .appCompone(appContext.appComponent)    //以及继承的Component
                .build()
                .inject(this);
        }

        @Override
        protected void onStart() {
            super.onStart();

            CommonUtils.makeToast(this, devPresenter.iamHere());
        }
    }


