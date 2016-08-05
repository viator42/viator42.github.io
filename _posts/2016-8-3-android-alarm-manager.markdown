---
layout: post
title:  "Android 使用AlarmManager设置定时任务"
date:   2016-07-05
categories: android
---

最近在做的项目里面需要定时操作的任务,开始的方案是企图在后台跑一个service然后不停的判断时间来时间.写着写着知道了安卓系统自带有定时的功能,顿时感觉自己的方法low爆了.
还是一边研究一边把步骤记录下来吧.

### Alarm类型

1. RTC_WAKEUP   指定的时间激活PendingIntent并唤醒设备
2. RTC          指定的时间激活PendingIntent
3. ELAPSED_REALTIME     一段时间之后激活PendingIntent
4. ELAPSED_REALTIME_WAKEUP       一段时间之后激活PendingIntent并唤醒设备

### 设置BroadcastReceiver

    public class ScheduleReceiver  extends BroadcastReceiver {
        @Override
        public void onReceive(Context context, Intent intent) {
            Log.d("ErikaNote", "TestReceiver is called");
        }
    }

AndroidMenifest.xml添加

        <receiver android:name=".receiver.ScheduleReceiver">
            <intent-filter>
                <action android:name="com.ai.android.intents.testbc" />
            </intent-filter>
        </receiver>
    ...
    </application>

### 设置Alarm

    Calendar cal = CommonUtils.getTimeAfterInSecs(10);
    Intent intent =  new Intent(DevActivity.this, ScheduleReceiver.class);
    PendingIntent pendingIntent = PendingIntent.getBroadcast(DevActivity.this, 1, intent, PendingIntent.FLAG_ONE_SHOT);
    AlarmManager alarmManager = (AlarmManager) getSystemService(Context.ALARM_SERVICE);
    alarmManager.set(AlarmManager.RTC,
            cal.getTimeInMillis(),
            pendingIntent);