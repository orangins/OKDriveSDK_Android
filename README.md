# OKDriveSDK_Android
- 复制libs下所有jar包到项目libs下
- 复制jniLibs下所有文件到项目src/main/jniLibs下
- 配置以下权限
```java
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.LOCATION_HARDWARE" />
<uses-permission android:name="android.permission.ACCESS_LOCATION_EXTRA_COMMANDS" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
<uses-permission android:name="android.permission.BROADCAST_STICKY" />
<uses-permission android:name="android.permission.WAKE_LOCK" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>
```
- 复制以下内容到配置文件
```java
<service
    android:name="com.okdrive.daemon.NotificationMonitor"
    android:permission="android.permission.BIND_NOTIFICATION_LISTENER_SERVICE">
    <intent-filter>
        <action android:name="android.service.notification.NotificationListenerService"/>
    </intent-filter>
</service>
<service android:name="com.okdrive.daemon.DaemonService"/>
<service android:name="com.okdrive.others.DriverService"/>
<service android:name="com.okdrive.others.GrayInnerService"/>
<service android:name="com.okdrive.others.UploadDriverDataService"/>

<receiver android:name="com.okdrive.others.WifiReceiver">
    <intent-filter>
        <action android:name="android.net.conn.CONNECTIVITY_CHANGE"/>
    </intent-filter>
</receiver>
<receiver android:name="com.okdrive.others.PhoneStatusReceiver">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="android.intent.action.ACTION_SHUTDOWN"/>
    </intent-filter>
</receiver>

<service android:name="com.okdrive.others.AlarmService"/>

<receiver android:name="com.okdrive.others.AlarmReceiver"/>

<service android:name="com.okdrive.others.UploadAlarmService"/>

<receiver android:name="com.okdrive.others.UploadAlarmReceiver"/>
<receiver android:name="com.okdrive.others.PhoneReceiver">
    <intent-filter>
        <action android:name="android.intent.action.PHONE_STATE"/>
    </intent-filter>
</receiver>
```
- 获取行程状态
####复制以下内容
```java
OKDriveSDK.getInstance(this).getDriverStatus(new ICallBack1MWithObject() {
    @Override
    public void callBack(Object object) {
        try {
            JSONObject jsonObject = new JSONObject(object.toString());
            long driveStatus = jsonObject.getLong("driveStatus");  //行程状态 -1:行程未开始 1:行程开始 0:行程结束
			long timestamp = jsonObject.getLong("timestamp");  //行程开启时间 当行程结束后才会返回此参数
			long timestampEnd = jsonObject.getLong("timestampEnd");  //行程结束时间 同上
        } catch (JSONException e) {
            e.printStackTrace();
        }
    }
});
```
- 开启守护

####复制以下内容
```java
private static boolean isEnabled(Activity activity) {
	String pkgName = activity.getPackageName();
	final String flat = Settings.Secure.getString(activity.getContentResolver(), "enabled_notification_listeners");
	if (!TextUtils.isEmpty(flat)) {
		final String[] names = flat.split(":");
		for (int i = 0; i < names.length; i++) {
			final ComponentName cn = ComponentName.unflattenFromString(names[i]);
			if (cn != null) {
				if (TextUtils.equals(pkgName, cn.getPackageName())) {
					return true;
				}
			}
		}
	}
	return false;
}

public static void checkNotify(final Activity activity) {
	if (!isEnabled(activity) && Build.VERSION.SDK_INT >= Build.VERSION_CODES.JELLY_BEAN_MR2) {
		new AlertDialog.Builder(activity)
				.setMessage("是否开启通知使用权，确保行程管理正常运行，从而改善您的驾驶行为？")
				.setTitle("通知使用权")
				.setCancelable(true)
				.setPositiveButton(android.R.string.ok, new DialogInterface.OnClickListener() {
					public void onClick(DialogInterface dialog, int id) {
						activity.startActivity(new Intent("android.settings.ACTION_NOTIFICATION_LISTENER_SETTINGS"));
					}
				})
				.setNegativeButton(android.R.string.cancel, null)
				.create().show();
	}
}
```
- 调用样例

####手动开启行程：
```java
       CheckBox cb = new CheckBox(this);
       OKDriveConfig config = new OKDriveConfig();
        config.setApp_key("请替换此处");
        config.setAuto_drive(false);//
        config.setUser_id("请替换此处");
        final OKDriveSDK okDriveSDK = OKDriveSDK.getInstance(getApplicationContext());
        okDriveSDK.setup(config, new CallBack() {
            @Override
            public void onSuccess() {
            }

            @Override
            public void onFail(String s) {
            }
        });
        cb.setChecked(okDriveSDK.isDriving());
        cb.setOnCheckedChangeListener(new CompoundButton.OnCheckedChangeListener() {
            @Override
            public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
                if(isChecked){
                    okDriveSDK.startDriving();
                }else{
                    okDriveSDK.endDriving();
                }
            }
        });
```
####自动开启行程：
```java
    OKDriveConfig config = new OKDriveConfig();
    config.setApp_key("请替换此处");
    config.setAuto_drive(true);
    config.setUser_id("请替换此处");
    final OKDriveSDK okDriveSDK = OKDriveSDK.getInstance(getApplicationContext());
    okDriveSDK.setup(config, new CallBack() {
        @Override
        public void onSuccess() {
        }

        @Override
        public void onFail(String s) {
        }
    });
```
# <a id="Examples"></a>【注意事项】
- 集成SDK后会在后台运行并跟踪您的驾驶行为，可能会显著增加电量消耗，因此我们建议您在夜间或非开车时间对手机充电

# <a id="Examples"></a>【联系方式】
- 有问题请致信：okdrive#okchexian.com（请将#替换成@）