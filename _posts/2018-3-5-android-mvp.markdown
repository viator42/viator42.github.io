---
layout: post
title:  "Android架构MVP模式"
date:   2018-3-5
categories: Android
---
todo

Android最初使用的是MVC模式,即Model,View,Controller.这三个部分是相互调用的,View可以直接调用model.    
MVC模式的缺点是三个部分互相产生交互,结构混乱.在Android开发中Activity会存在一部分的业务逻辑.作为View的实际承担了Controller的功能,造成Activity的臃肿.    
MVP中添加了Prsernter类代替Controller,Model和View之间通过Prsernter控制而不直接交互    
Model ←→ Prsernter ←→ View

MVP模式的优点:    

* Activity剥离了业务逻辑,只用于展示和事件响应
* 直接对Prsernter的接口,进行单元测试,不需要操作UI

## 总结

定义一个Connector接口,下属两个子接口分别供V和P继承.V的接口方法定义操作UI.P包含V的引用并调用V的接口方法操作V.在V中新建P并把V的引用赋值给P.V调用P的接口方法来进行获取数据等操作.

碰到的问题是P和V的解耦,ListView和RecyclerView的listData, Adapter, LayoutManager到底是放到View还是Prsenter中的选择.     
UI相关的应该全部放到View中,P只进行业务流程相关的操作

## 分析Google的MVP实例应用代码

定义Contract接口,作为连接View和Presenter的接口,定义一系列方法

    public interface TasksContract {
        interface View extends BaseView<Presenter> {
            void setLoadingIndicator(boolean active);
            void showTasks(List<Task> tasks);
            void showAddTask();
            void showTaskDetailsUi(String taskId);
            void showTaskMarkedComplete();
            void showTaskMarkedActive();
            void showCompletedTasksCleared();
            void showLoadingTasksError();
            void showNoTasks();
            void showActiveFilterLabel();
            void showCompletedFilterLabel();
            void showAllFilterLabel();
            void showNoActiveTasks();
            void showNoCompletedTasks();
            void showSuccessfullySavedMessage();
            boolean isActive();
            void showFilteringPopUpMenu();
        }

        interface Presenter extends BasePresenter {
            void result(int requestCode, int resultCode);
            void loadTasks(boolean forceUpdate);
            void addNewTask();
            void openTaskDetails(@NonNull Task requestedTask);
            void completeTask(@NonNull Task completedTask);
            void activateTask(@NonNull Task activeTask);
            void clearCompletedTasks();
            void setFiltering(TasksFilterType requestType);
            TasksFilterType getFiltering();
        }
    }

View类继承TasksContract.View接口,实现方法

    public class TasksFragment extends Fragment implements TasksContract.View {
        @Override
        public void showActiveFilterLabel() {
            mFilteringLabelView.setText(getResources().getString(R.string.label_active));
        }

        @Override
        public void showCompletedFilterLabel() {
            mFilteringLabelView.setText(getResources().getString(R.string.label_completed));
        }

        @Override
        public void showAllFilterLabel() {
            mFilteringLabelView.setText(getResources().getString(R.string.label_all));
        }

        @Override
        public void showAddTask() {
            Intent intent = new Intent(getContext(), AddEditTaskActivity.class);
            startActivityForResult(intent, AddEditTaskActivity.REQUEST_ADD_TASK);
        }

    }

Presenter类继承TasksContract.Presenter接口并实现方法

    public class TasksPresenter implements TasksContract.Presenter {
        private final TasksContract.View mTasksView;

        public TasksPresenter(@NonNull TasksRepository tasksRepository, @NonNull TasksContract.View tasksView) {
            mTasksRepository = checkNotNull(tasksRepository, "tasksRepository cannot be null");
            mTasksView = checkNotNull(tasksView, "tasksView cannot be null!");
            mTasksView.setPresenter(this);
        }


        @Override
        public void loadTasks(boolean forceUpdate) {
            // Simplification for sample: a network reload will be forced on first load.
            loadTasks(forceUpdate || mFirstLoad, true);
            mFirstLoad = false;
        }
        
    }