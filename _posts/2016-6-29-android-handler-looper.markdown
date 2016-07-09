---
layout: post
title:  "Android Handler Looper笔记"
date:   2016-06-29
categories: android
---

Message：消息，其中包含了消息ID，消息处理对象以及处理的数据等，由MessageQueue统一列队，终由Handler处理。

Handler：处理者，负责Message的发送及处理。使用Handler时，需要实现handleMessage(Message msg)方法来对特定的Message进行处理，例如更新UI等。

MessageQueue：消息队列，用来存放Handler发送过来的消息，并按照FIFO规则执行。当然，存放Message并非实际意义的保存，而是将Message以链表的方式串联起来的，等待Looper的抽取。

Looper：消息泵，不断地从MessageQueue中抽取Message执行。因此，一个MessageQueue需要一个Looper。

Thread：线程，负责调度整个消息循环，即消息循环的执行场所。

用途: 主要是在主线程和子线程之间传递信息.

Handler负责多个线程间的消息发送和接收.
Message传递的消息实体. 参数类型包括arg1, arg2两个int类型变量和一个Object类型. what(int类型)一般用于给消息分类.

参考链接: http://www.cnblogs.com/xirihanlin/archive/2011/04/11/2012746.html

主线程和子线程通过Handler交互的实例

    package com.viator42.handlertester;

    import android.os.Bundle;
    import android.os.Handler;
    import android.os.Looper;
    import android.os.Message;
    import android.support.design.widget.FloatingActionButton;
    import android.support.design.widget.Snackbar;
    import android.support.v7.app.AppCompatActivity;
    import android.support.v7.widget.Toolbar;
    import android.util.Log;
    import android.view.Menu;
    import android.view.MenuItem;
    import android.view.View;
    import android.widget.Button;
    import android.widget.TextView;

    public class MainActivity extends AppCompatActivity {
        private Handler handler;
        private Handler handler2;
        private Button startBtn;
        private TextView subMsgTextView;
        private TextView subMsg2TextView;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
            setSupportActionBar(toolbar);

            FloatingActionButton fab = (FloatingActionButton) findViewById(R.id.fab);
            fab.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View view) {
                    Snackbar.make(view, "Replace with your own action", Snackbar.LENGTH_LONG)
                            .setAction("Action", null).show();
                }
            });

            startBtn = (Button) findViewById(R.id.start);
            startBtn.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {

                    Message message1 = Message.obtain();
                    String msg1 = "This is an message from main thread";
                    message1.obj = msg1;
                    message1.what = 2;

                    handler2.sendMessage(message1);

                }
            });

            handler = new Handler(){
                @Override
                public void handleMessage(Message msg) {
                    super.handleMessage(msg);

                    String subMsg = (String) msg.obj;

                    subMsgTextView.setText(subMsg);

                }
            };

            subMsgTextView = (TextView) findViewById(R.id.sub_msg);
            subMsg2TextView = (TextView) findViewById(R.id.sub_msg_2);
        }

        @Override
        protected void onStart() {
            super.onStart();

            Thread thread = new Thread( new SubThread());
            thread.start();

        }

        @Override
        public boolean onCreateOptionsMenu(Menu menu) {
            // Inflate the menu; this adds items to the action bar if it is present.
            getMenuInflater().inflate(R.menu.menu_main, menu);
            return true;
        }

        @Override
        public boolean onOptionsItemSelected(MenuItem item) {
            // Handle action bar item clicks here. The action bar will
            // automatically handle clicks on the Home/Up button, so long
            // as you specify a parent activity in AndroidManifest.xml.
            int id = item.getItemId();

            //noinspection SimplifiableIfStatement
            if (id == R.id.action_settings) {
                return true;
            }

            return super.onOptionsItemSelected(item);
        }

        public class SubThread implements Runnable
        {
            private Message message;
            public SubThread()
            {

            }

            @Override
            public void run() {
                message = Message.obtain();
                message.arg1 = 1001;
                String msg1 = "This is an message from sub thread";
                message.obj = msg1;
                message.what = 1;

                handler.sendMessage(message);

                Looper.prepare();
                handler2 = new Handler(){
                    @Override
                    public void handleMessage(Message msg) {
                        super.handleMessage(msg);

                        Log.v("thread msg", msg.obj.toString());

                    }
                };
                Looper.loop();

            }
        }

    }
