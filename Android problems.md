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
1 为什么list中onItemLongClickListener返回true就不会触发onItemClickListener?
2 从父布局到子布局事件传递机制是怎样的？

###主动显示输入法
在界面没有加载完时，直接调用显示输入法，不会起作用。
    
      /**
     * 显示软键盘, 延迟500ms 弹起键盘,有时当布局没有加载完,调用显示输入法会不起作用
     * @param editText 输入框
     * @param context Context对象
     */
    public static void showSoftKeyboard(EditText editText, Context context) {
        if (editText==null||context==null)return;
        editText.requestFocus();
        InputMethodManager imm = (InputMethodManager) context.getSystemService(Context
                .INPUT_METHOD_SERVICE);
        editText.postDelayed(()->imm.showSoftInput(editText,InputMethodManager.SHOW_IMPLICIT), 500);
    }
    
###布局由visible转gone,布局轮廓还在
显示布局边界情况下，会发现一个view由visible转gone，view的轮廓还在，除非一开始就是gone，没有绘制到界面上。
### byte[] bitmap 互相转换

    ByteArrayOutputStream baos=new  ByteArrayOutputStream();
     bitmap.compress(Bitmap.CompressFormat.PNG,100, baos);
     byte [] b=baos.toByteArray();
    Bitmap bitmap=BitmapFactory.decodeByteArray(encodeByte, 0, encodeByte.length);
      return bitmap;