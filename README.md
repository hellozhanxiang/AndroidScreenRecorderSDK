# Android Screen Recorder SDK #

基于Android 5.0 API21，录制暂停、继续功能需要在API24及以上使用，5.0以下版本可以调用，但是无效，使用时不必区分版本

## AndroidManifest.xml 配置 ##

添加权限
    
     <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
     <uses-permission android:name="android.permission.RECORD_AUDIO"/>
注册服务

    <service android:name="com.bobo.recorder.RecordService"/>
## 使用##
### 1.Activity中获取Recorder实例，并完成初始化 ###
    private Recorder mRecorder;
    mRecorder = Recorder.getRecorder();
    mRecorder.init(this);
### 2.配置相关回调 ###
	@Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        mRecorder.onActivityResult(requestCode, resultCode, data);
    }
	@Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] 	grantResults) {
		mRecorder.onRequestPermissionsResult(requestCode, permissions, grantResults);
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
	//其他方法陆续开放中
	...
### 4.视频位置 ###
执行stop()方法后，视频文件（.mp4）默认存放在SD卡根目录"ScreenRecord"文件中，自定义位置方法正在开放中...
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
    	public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] 	grantResults) {
			//设置回调
        	mRecorder.onRequestPermissionsResult(requestCode, permissions, grantResults);
    	}

   		@Override
    	protected void onDestroy() {
			//结束释放
        	mRecorder.finish();
			super.onDestroy();
    	}
    }
