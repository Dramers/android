## Android开发中遇到的问题
### Launcher Activity没有启动
  在某种特殊情况下，Launcher Activity没有启动直接进入曾经退出的activity，原因待查，需要知晓可能存在这种情况。
  
### Hander.postDelay屏幕关闭runnable有可能不执行
postDelay时间执行依赖SystemClock.uptimeMillis()，而这个时间在cpu休眠时不会计算在内，所以post的这个runnnable不会被执行，解决办法是使用AlarmMamanger,但是AlarmManager会增加手机耗电，慎重使用。

    AlarmManager alarmManager = (AlarmManager) getSystemService(ALARM_SERVICE);
    Intent  backIntent = new Intent(this, BackgroundReceiver.class);
    PendingIntent pendingIntent = PendingIntent.getBroadcast(this, 1, backIntent, PendingIntent.FLAG_UPDATE_CURRENT);
     if (FlowConfig.SYSTEM_VERSION >= 19) {//19上保证执行要用这个方法
            alarmManager.setExact(AlarmManager.ELAPSED_REALTIME_WAKEUP, disconnectDelay, pendingIntent);
        }else {
            alarmManager.set(AlarmManager.ELAPSED_REALTIME_WAKEUP, disconnectDelay, pendingIntent);
        }

### list点击、长按事件与item点击长按事件
item中元素如果定义了长按或者点击事件，list中的点击长按事件就会被item元素拦截。//TODO 看完源码再来补充具体逻辑