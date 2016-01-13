# OKDriveSDK_Android
- 复制libs下所有jar包到项目libs下
- 复制jniLibs下所有文件到项目src/main/jniLibs下
- 复制assets下icudt46l.zip到项目assets下
- 复制以下内容到配置文件
```java
		<service android:name="com.okdrive.others.DriverService">
            <intent-filter android:priority="1000">
                <action android:name="com.okdrive.others.DriverService" />
            </intent-filter>
        </service>
        <service android:name="com.okdrive.hand.HandDriverService">
            <intent-filter android:priority="1000">
                <action android:name="com.okdrive.hand.HandDriverService" />
            </intent-filter>
        </service>
        <service android:name="com.okdrive.others.UploadDriverDataService">
            <intent-filter android:priority="1000">
                <action android:name="com.okdrive.others.UploadDriverDataService" />
            </intent-filter>
        </service>
        <receiver android:name="com.okdrive.others.WifiReceiver">
            <intent-filter>
                <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
            </intent-filter>
        </receiver>
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