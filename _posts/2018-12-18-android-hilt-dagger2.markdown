---
layout: post
title:  "Hilt & Dagger2 笔记"
date:   "2018-12-18"
categories: android
---

# Hilt

Hilt是用来代替Dagger的依赖注入框架，解决了Dagger存在的问题并更适用于Android开发。

## Hilt相对于Dagger做的优化有

1.无需编写大量的Component代码
2.Scope也会与Component自动绑定
3.预定义绑定，例如 Application与Activity
4.预定义的限定符，例如@ApplicationContext与@ActivityContext

## Hilt集成

build.gradle (项目根目录)

    dependencies {
            。。。
            classpath 'com.google.dagger:hilt-android-gradle-plugin:2.28-alpha'
        }

build.gradle （app）

    apply plugin: 'kotlin-android'
    apply plugin: 'kotlin-kapt'
    apply plugin: 'dagger.hilt.android.plugin'

    dependencies {
        。。。
        implementation "com.google.dagger:hilt-android:2.28-alpha"
        kapt "com.google.dagger:hilt-android-compiler:2.28-alpha"
    }

## Hilt的用法

### 1.必须要自定义一个Application并添加@HiltAndroidApp注解，否则Hilt将无法正常工作。

    @HiltAndroidApp
    class MyApplication : Application() {
        。。。
    }

### 2.@AndroidEntryPoint定义入口点

    class MainActivity : AppCompatActivity() {
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_main)
        }
        
    }

Hilt大幅简化了Dagger2的用法，使得我们不用通过@Component注解去编写桥接层的逻辑，但是也因此限定了注入功能只能从几个Android固定的入口点开始。

    * Application
    * Activity
    * Fragment
    * View
    * Service
    * BroadcastReceiver

### 3.对于被注入的类要定义@Inject注解，通过这个构造函数来创建类

    class Truck @Inject constructor() {
        fun deliver() {
            println("Truck is delivering cargo.")
        }
    }

### 4.注入类实现

    @AndroidEntryPoint
    class MainActivity : AppCompatActivity() {

        @Inject
        lateinit var truck: Truck

        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_main)
            truck.deliver()
        }

    }

## 带参数的依赖注入

    class Truck @Inject constructor(val driver: Driver) {
        fun deliver() {
            println("Truck is delivering cargo. Driven by $driver")
        }
    }

构造函数中所依赖的所有其他对象都支持依赖注入了，那么本类才可以被依赖注入。

## 接口的依赖注入

正常的创建接口，不需要特殊处理

    interface Engine {
        fun start()
        fun shutdown()
    }

创建实现接口的类，实现接口方法，@Inject标注构造方法

    class GasEngine @Inject constructor(): Engine {
        override fun start() {
            println("")
            Log.v("HiltTester","Gas engine start.")
        }

        override fun shutdown() {
            println("")
            Log.v("HiltTester","Gas engine shutdown.")
        }

    }

创建一个Module抽象类，类名不限，@InstallIn标明作用范围，作用是标识接口使用哪个实现类进行初始化

    @Module
    @InstallIn(ActivityComponent::class)
    abstract class EngineModule {
        @Binds
        abstract fun bindEngine(gasEngine: GasEngine): Engine
    }

接下来就可以对接口进行注入

    class Truck @Inject constructor() {
        @Inject
        lateinit var engine: Engine

        fun startEngine() {
            engine.start()
        }
    }

## 第三方类的注入

对于第三方类，没法在类中添加@Inject入口，解决方法是在Module类中手动实现构造方法，@Provides方法注解构造方法。

    @Module
    @InstallIn(ActivityComponent::class)
    class NetworkModule {

        @Singleton  //如果在作用域中只需要一个对象，就添加@Single注解
        @Provides
        fun provideOkHttpClient(): OkHttpClient {
            return OkHttpClient.Builder()
                .connectTimeout(20, TimeUnit.SECONDS)
                .readTimeout(20, TimeUnit.SECONDS)
                .writeTimeout(20, TimeUnit.SECONDS)
                .build()
        }

    }

## 组件的作用域

nstallIn，就是安装到的意思。那么@InstallIn(ActivityComponent::class)，就是把这个模块安装到Activity组件当中。

既然是安装到了Activity组件当中，那么自然在Activity中是可以使用由这个模块提供的所有依赖注入实例。另外，Activity中包含的Fragment和View也可以使用，但是除了Activity、Fragment、View之外的其他地方就无法使用了。

* Hilt 组件       注入器面向的对象
* ApplicationComponent    Application
* ActivityRetainedComponent	ViewModel
* ActivityComponent	Activity
* FragmentComponent	Fragment
* ViewComponent	View
* ViewWithFragmentComponent	带有 @WithFragmentBindings 注释的 View
* ServiceComponent	Service

## 预置Qualifier

如果需要注入的类需要Context参数，参数前面加入ApplicationContext注解即可

    @Singleton
        class Driver @Inject constructor(@ApplicationContext val context: Context) {
    }

如果是Activity参数

    @Singleton
        class Driver @Inject constructor(@ActivityContext val context: Context) {
    }

--------

# Dagger2 已废弃

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


