# Android Screen Recorder SDK #

基于Android 5.0 API21录屏SDK，适用于API21及以上版本，API21以下版本可以调用但是无效，使用时不必区分版本

## AndroidManifest.xml 配置 ##

添加权限
    
     <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
     <uses-permission android:name="android.permission.RECORD_AUDIO"/>
注册服务

    <service android:name="com.bobo.recorder.RecordService">
            <intent-filter>
                <action android:name="com.bobo.recorder"/>
            </intent-filter>
    </service>
## 使用##
### 1.Activity中获取Recorder实例，并完成初始化 ###
	
    private Recorder mRecorder;
    mRecorder = Recorder.getRecorder();
    mRecorder.init(this);

	/*以下设置可选，不设置时为默认值*/

	//设置帧率，默认30
 	mRecorder.setVideoFrameRate(15);
	//设置视频宽高，默认设备原始宽高
    mRecorder.setVideoSize(500,600);
	//设置编码比特率，默认5 * 1024 * 1024
    mRecorder.setVideoEncodingBitRate(5 * 1024 * 1024);
	//设置视频输出完整路径（含文件名，文件名以".mp4"结尾）,默认SD卡根目录/ScreenRecord/“时间戳".mp4
    mRecorder.setOutputFile(Environment.getExternalStorageDirectory().getAbsolutePath()+ File.separator++System.currentTimeMillis()+".mp4");
### 2.配置相关回调 ###
	@Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        mRecorder.onActivityResult(requestCode, resultCode, data);
    }
	@Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        if(! mRecorder.onRequestPermissionsResult(requestCode,permissions,grantResults)){
            super.onRequestPermissionsResult(requestCode, permissions, grantResults);
            //TODO：SD卡读写&麦克风权限申请失败的处理方法
            Toast.makeText(this, "未获取麦克风或SD卡读取权限", Toast.LENGTH_SHORT).show();
        }
    }
	@Override
    protected void onDestroy() 
        mRecorder.finish();
		super.onDestroy();
    }
### 3.执行相关方法 ###
	//判断是否正在运行
 	mRecorder.isRunning();
	//录制开始
    mRecorder.start();
	//录制结束
    mRecorder.stop();

### 4.视频位置 ###
执行stop()方法后，视频文件（.mp4）默认存放在SD卡根目录"ScreenRecord"文件中
## 调用SDK实例 ##
    public class MainActivity extends AppCompatActivity {
    
    	private Recorder mRecorder;
    	private Button btn;

   		@Override
    	protected void onCreate(Bundle savedInstanceState) {
        	super.onCreate(savedInstanceState);
        	setContentView(R.layout.activity_main);

			//获取Recorder实例
        	mRecorder = Recorder.getRecorder();
			//传入当前Activity实例，完成初始化
        	mRecorder.init(this);
			//设置帧率，不设置为默认值30
			mRecorder.setVideoFrameRate(15);
			//设置视频宽高，不设置为设备原始宽高
        	mRecorder.setVideoSize(500,600);
			//设置视频输出完整路径（含文件名，文件名以".mp4"结尾）,不设置默认SD卡根目录/ScreenRecord/“时间戳".mp4
			mRecorder.setOutputFile(Environment.getExternalStorageDirectory().getAbsolutePath()+File.separator+System.currentTimeMillis()+".mp4");

        	btn = (Button) findViewById(R.id.start_record);
        	btn.setOnClickListener(new View.OnClickListener() {
            	@Override
            	public void onClick(View v) {
                	if (mRecorder.isRunning()) {
                    	mRecorder.stop();
                    	btn.setText("开始录屏");
                	} else {
                    	mRecorder.start();
                    	btn.setText("停止录屏");
                	}
            	}
        	});
    	}

    	@Override
    	protected void onActivityResult(int requestCode, int resultCode, Intent data) {
			//设置回调
        	mRecorder.onActivityResult(requestCode, resultCode, data);
    	}

    	@Override
   		public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        	if(! mRecorder.onRequestPermissionsResult(requestCode,permissions,grantResults)){
            	super.onRequestPermissionsResult(requestCode, permissions, grantResults);
           		//TODO：SD卡写入&麦克风权限申请失败的处理方法
            	Toast.makeText(this, "未获取麦克风或SD卡写入权限", Toast.LENGTH_SHORT).show();
        }

    }
   		@Override
    	protected void onDestroy() {
			//结束释放
        	mRecorder.finish();
			super.onDestroy();
    	}
    }
