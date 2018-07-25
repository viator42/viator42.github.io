---
layout: post
title:  "Android 单元测试"
date:   2018-7-25
categories: android,UnitTest
---

现在推荐的单元测试框架是Junit和mockito

导入库

    testImplementation 'junit:junit:4.12'
    testImplementation 'org.mockito:mockito-core:2.+'

## Junit相关



## Mockito相关

Mockito的特点是可以简化测试逻辑,对于不必要的对象可以虚拟一个出来,集中精力测试需要测试的类    
通过检验被测试的代码有没有执行来判断测试是否通过.比如测试列表加载的方法判断是否加载成功的方法是检查执行完毕后progressBar消失的代码有没有执行.有执行则测试通过.    
添加内容的方法测试是检查"添加成功"dialog弹出代码有没有执行     

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


