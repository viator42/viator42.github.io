---
layout: post
title:  "Android Handler Looper笔记"
date:   2016-06-29
categories: android
---

作用 用于在线程之间传递信息,子线程向主线程传递结果,进度信息.

Message：封装的消息体.    
其中包含了消息ID，消息处理对象以及处理的数据等，由MessageQueue统一列队，终由Handler处理。

参数:
* what 自定义消息id
* arg1, arg2 消息参数
* obj 消息内容,可以为任意类型
          
Handler：处理者，负责Message的发送及处理。在接收消息的地方定义.重写handleMessage(Message msg)方法来处理消息.

MessageQueue：消息队列，用来存放Handler发送过来的消息，并按照FIFO规则执行。当然，存放Message并非实际意义的保存，而是将Message以链表的方式串联起来的，等待Looper的抽取。内部封装的类,使用时不可见.

Looper：消息泵，不断地从MessageQueue中抽取Message执行。因此，一个MessageQueue需要一个Looper。UI线程自带Looper不需要定义.
子线程在定义Hander的时候需要先初始化Looper,初始化Hander的代码写在Looper.prepare();和Looper.loop();之间.

主线程和子线程通过Handler交互的实例

    public class MainActivity extends AppCompatActivity {
        private Button startThreadBtn;
        private Button pushMsgToSubBtn;
        private TextView msgTextView;
        private Handler toSubThreadHandler;
        private Handler toMainThreadHandler;
        private final static int MSG_TO_MAIN = 1;
        private final static int MSG_TO_SUB = 2;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            msgTextView = (TextView) findViewById(R.id.msg);
            startThreadBtn = (Button) findViewById(R.id.start_thread);
            startThreadBtn.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    SubThread subThread = new SubThread();
                    subThread.start();

                    pushMsgToSubBtn.setEnabled(true);
                }
            });

            pushMsgToSubBtn = (Button) findViewById(R.id.push_msg_to_sub);
            pushMsgToSubBtn.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    Message message1 = Message.obtain();
                    String msg1 = "This is an message to sub thread";
                    message1.obj = msg1;
                    message1.what = MSG_TO_SUB;
                    toMainThreadHandler.sendMessage(message1);
                }
            });
            pushMsgToSubBtn.setEnabled(false);

            toSubThreadHandler = new Handler() {
                @Override
                public void handleMessage(Message msg) {
                    super.handleMessage(msg);

                    switch (msg.what)
                    {
                        case MSG_TO_MAIN:
                            msgTextView.setText((String) msg.obj);
                            break;
                    }
                }
            };
        }

        private class SubThread extends Thread
        {
            @Override
            public void run() {
                super.run();

                Message toMainThreadMsg = new Message();
                toMainThreadMsg.what = 1;
                String msg = "A Messgae to Main Thread";
                toMainThreadMsg.obj = msg;

                toSubThreadHandler.sendMessage(toMainThreadMsg);

                Looper.prepare();
                toMainThreadHandler = new Handler(){
                    @Override
                    public void handleMessage(Message msg) {
                        super.handleMessage(msg);
                        switch (msg.what)
                        {
                            case MSG_TO_SUB:
                                Toast.makeText(MainActivity.this, (String) msg.obj, Toast.LENGTH_SHORT).show();
                                break;
                        }
                    }
                };
                Looper.loop();
            }
        }
    }

参考链接    
http://www.cnblogs.com/xirihanlin/archive/2011/04/11/2012746.html
