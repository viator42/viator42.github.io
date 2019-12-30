---
layout: post
title:  "Android测试相关"
date:   2018-7-25
categories: android,UnitTest
---

## 测试分类

### Monkey 测试

在控制台输入

    adb shell monkey -p com.brand.ushopping -v 100

参数列表:    
* 最后的参数表示操作次数
* -v 输出结果
* -s 用于指定伪随机数生成器的seed值，如果seed相同，则两次Monkey测试所产生的事件序列也相同的

### Small Test    
单元测试，对每一个功能模块进行单独的测试

### Medium Test    
集成测试，一部分的模块进行协同测试，测试运行在虚拟机或者真机上

### Large Test    
集成UI的工作流测试，测试运行在虚拟机或者真机上

单元测试使用的框架是Junit和mockito

Mockito 用来模拟测试中的依赖对象，只创建测试中用到的对象    
单元测试只在本地机器上运行使用JVM虚拟机，如果测试依赖Android Framework，使用Robolectric模拟依赖    

导入库

    testImplementation 'junit:junit:4.12'
    testImplementation 'org.mockito:mockito-core:2.+'

## Junit相关

    在所有测试调用指令发起前的 setUp() 方法。
    在测试方法运行后的 tearDown() 方法。


@Before 注解的方法在测试开始前执行,进行测试环境初始化工作     
@After 注解的方法在测试方法结束后执行    
@Test 注解的是测试方法    
@BeforeClass 该方法需要在类中所有方法前运行    
@AfterClass 它将会使方法在所有测试结束后执行。这个可以用来进行清理活动。    
@Ignore 这个注释是用来忽略有关不需要执行的测试的    

beforeClass() 方法首先执行，并且只执行一次。    
afterClass() 方法最后执行，并且只执行一次。    
before() 方法针对每一个测试用例执行，但是是在执行测试用例之前。    
after() 方法针对每一个测试用例执行，但是是在执行测试用例之后。    
在 before() 方法和 after() 方法之间，执行每一个测试用例。    


执行顺序    
BeforeClass -> Before -> Test -> After -> AfterClass

    @Before
        public void setUp() throws Exception {
    }

    @After
        public void tearDown() throws Exception {
    }
    
    @Test
    public void testFunc() {
    }

Assert断言

1 	void assertEquals(boolean expected, boolean actual)    
    检查两个变量或者等式是否平衡

2 	void assertTrue(boolean expected, boolean actual)    
    检查条件为真

3 	void assertFalse(boolean condition)    
    检查条件为假

4 	void assertNotNull(Object object)    
    检查对象不为空

5 	void assertNull(Object object)    
    检查对象为空

6 	void assertSame(boolean condition)    
    assertSame() 方法检查两个相关对象是否指向同一个对象

7 	void assertNotSame(boolean condition)    
    assertNotSame() 方法检查两个相关对象是否不指向同一个对象

8 	void assertArrayEquals(expectedArray, resultArray)    
    assertArrayEquals() 方法检查两个数组是否相等

## Mockito相关

Mockito的特点是可以简化测试逻辑,对于不必要的对象可以虚拟一个出来,集中精力测试需要测试的类    
通过检验被测试的代码有没有执行来判断测试是否通过.比如测试列表加载的方法判断是否加载成功的方法是检查执行完毕后progressBar消失的代码有没有执行.有执行则测试通过.    
添加内容的方法测试是检查"添加成功"dialog弹出代码有没有执行         
verify()方法的参数必须是mock对象

    import org.mockito.Mock;
    import static org.mockito.Mockito.*;
    @Mock
    private MainpageContract.View view;

    private MainpagePresenter mainpagePresenter;

    @Before
    public void setUp() throws Exception {
        mainpagePresenter = new MainpagePresenter(view);
    }

    @Test
    public void loadCompletedTasksFromRepositoryAndLoadIntoView() {
        // Given an initialized TasksPresenter with initialized tasks
        when(mTasksRepository.getTasks()).thenReturn(Flowable.just(TASKS));
        // When loading of Tasks is requested
        mTasksPresenter.setFiltering(TasksFilterType.COMPLETED_TASKS);
        mTasksPresenter.loadTasks(true);

        // Then progress indicator is hidden and completed tasks are shown in UI
        verify(mTasksView).setLoadingIndicator(false);
    }

## Rxjava2单元测试出现：Android RxJava2 JUnit test - getMainLooper in android.os.Looper not mocked RuntimeException

出现这个问题的原因是：AndroidSchedulers.mainThread返回的是LooperScheduler的一个实例，但是LooperScheduler依赖于Android，我们在纯Java的单元测试中是无法使用它的。

解决方法

自定义一个RxSchedulersOverrideRule让它实现TestRule:

    import android.support.annotation.NonNull;

    import org.junit.rules.TestRule;
    import org.junit.runner.Description;
    import org.junit.runners.model.Statement;

    import java.util.concurrent.Callable;
    import java.util.concurrent.Executor;
    import java.util.concurrent.TimeUnit;

    import io.reactivex.Scheduler;
    import io.reactivex.android.plugins.RxAndroidPlugins;
    import io.reactivex.disposables.Disposable;
    import io.reactivex.functions.Function;
    import io.reactivex.internal.schedulers.ExecutorScheduler;
    import io.reactivex.plugins.RxJavaPlugins;

    public class RxSchedulersOverrideRule implements TestRule {
        private Scheduler immediate = new Scheduler() {
            @Override
            public Disposable scheduleDirect(@NonNull Runnable run, long delay, @NonNull TimeUnit unit) {
                return super.scheduleDirect(run, 0, unit);
            }

            @Override
            public Worker createWorker() {
                return new ExecutorScheduler.ExecutorWorker(new Executor() {
                    @Override
                    public void execute(@android.support.annotation.NonNull Runnable command) {
                        command.run();
                    }
                });
            }
        };

        @Override
        public Statement apply(final Statement base, Description description) {
            return new Statement() {
                @Override
                public void evaluate() throws Throwable {
                    RxJavaPlugins.setInitIoSchedulerHandler(new Function<Callable<Scheduler>, Scheduler>() {
                        @Override
                        public Scheduler apply(@NonNull Callable<Scheduler> schedulerCallable) throws Exception {
                            return immediate;
                        }
                    });
                    RxJavaPlugins.setInitComputationSchedulerHandler(new Function<Callable<Scheduler>, Scheduler>() {
                        @Override
                        public Scheduler apply(@NonNull Callable<Scheduler> schedulerCallable) throws Exception {
                            return immediate;
                        }
                    });
                    RxJavaPlugins.setInitNewThreadSchedulerHandler(new Function<Callable<Scheduler>, Scheduler>() {
                        @Override
                        public Scheduler apply(@NonNull Callable<Scheduler> schedulerCallable) throws Exception {
                            return immediate;
                        }
                    });
                    RxJavaPlugins.setInitSingleSchedulerHandler(new Function<Callable<Scheduler>, Scheduler>() {
                        @Override
                        public Scheduler apply(@NonNull Callable<Scheduler> schedulerCallable) throws Exception {
                            return immediate;
                        }
                    });
                    RxAndroidPlugins.setInitMainThreadSchedulerHandler(new Function<Callable<Scheduler>, Scheduler>() {
                        @Override
                        public Scheduler apply(@NonNull Callable<Scheduler> schedulerCallable) throws Exception {
                            return immediate;
                        }
                    });
                    try {
                        base.evaluate();
                    } finally {
                        RxJavaPlugins.reset();
                        RxAndroidPlugins.reset();
                    }
                }
            };
        }
    }

然后在测试类里面定义

    @ClassRule
    public static RxSchedulersOverrideRule sSchedulersOverrideRule = new RxSchedulersOverrideRule();

## Espresso相关


