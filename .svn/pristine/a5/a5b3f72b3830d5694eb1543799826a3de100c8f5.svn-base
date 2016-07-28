package com.example.safe;

import java.io.File;

import org.json.JSONException;
import org.json.JSONObject;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.AlertDialog.Builder;
import android.app.ProgressDialog;
import android.content.DialogInterface;
import android.content.DialogInterface.OnCancelListener;
import android.content.DialogInterface.OnClickListener;
import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.os.Environment;
import android.os.Handler;
import android.view.View;
import android.widget.TextView;
import android.widget.Toast;

import com.example.Utils.Version;
import com.lidroid.xutils.HttpUtils;
import com.lidroid.xutils.exception.HttpException;
import com.lidroid.xutils.http.ResponseInfo;
import com.lidroid.xutils.http.callback.RequestCallBack;
import com.lidroid.xutils.http.client.HttpRequest.HttpMethod;


public class SplashActivity extends Activity {
	private static final int REQUST = 100;
	private String desc;
	private String apkurl;
	private String versionName;
	private ProgressDialog progressDialog;
    
	@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_splash);
        
       initVersion();
        
    }

	private void initVersion() {
		TextView tv = (TextView) findViewById(R.id.tv);
		   versionName = Version.getVersionName(this);
		   tv.setText("版本"+versionName);
		  new Handler().postDelayed(new Runnable() {
			
			@Override
			public void run() {
				
				updata();
				
			}
		}, 3000);
	}

	private void updata() {
		HttpUtils httpUtils = new HttpUtils();
		httpUtils.send(HttpMethod.GET, "http://192.168.56.1:8080/Noname2.html", new RequestCallBack<String>() {

			

			@Override
			public void onFailure(HttpException arg0, String arg1) {
				Toast.makeText(SplashActivity.this, "获取失败", Toast.LENGTH_LONG).show();
				
			}

			@Override
			public void onSuccess(ResponseInfo<String> responseInfo) {
				String result = responseInfo.result;
				try {
					JSONObject jsonObject = new JSONObject(result);
					String code = jsonObject.getString("code");
					apkurl = jsonObject.getString("apkurl");
					desc = jsonObject.getString("desc");
					if (code.equals(versionName)) {
						Toast
						.makeText(SplashActivity.this, "版本未更新", Toast.LENGTH_LONG).show();
						enterHome();
					}else {
						//版本更新，跳出对话框
						Toast.makeText(SplashActivity.this, "版本需要更新", Toast.LENGTH_LONG).show();
						showdialog();
					}
					
				} catch (JSONException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
				
			}
		});
		
	}

	protected void showdialog() {
		AlertDialog.Builder builder = new Builder(this);
		builder.setTitle("有新版本更新");
		builder.setIcon(R.drawable.ic_launcher);
		builder.setMessage(desc);
		builder.setOnCancelListener(new OnCancelListener() {
			
			@Override
			public void onCancel(DialogInterface dialog) {
				enterHome();
				
			}
		});
		builder.setPositiveButton("即刻下载", new OnClickListener() {
			
			@Override
			public void onClick(DialogInterface dialog, int which) {
				//开始下载
				downloading();
				
			}
		});
		builder.setNegativeButton("稍后再说", new OnClickListener() {
			
			@Override
			public void onClick(DialogInterface dialog, int which) {
				dialog.dismiss();
				enterHome();
			}
		});
		builder.show();
		
	}


	protected void downloading() {
		progressDialog = new ProgressDialog(this);
		progressDialog.setCancelable(false);
		progressDialog.setProgressStyle(progressDialog.STYLE_HORIZONTAL);
		progressDialog.show();
		
		HttpUtils httpUtils = new HttpUtils();
		httpUtils.download(apkurl, "mnt/sdcard/a.apk", new RequestCallBack() {

			@Override
			public void onFailure(HttpException arg0, String arg1) {
				// TODO Auto-generated method stub
				progressDialog.dismiss();
				Toast.makeText(SplashActivity.this, "下载失败", Toast.LENGTH_LONG).show();
				enterHome();
			}

			@Override
			public void onSuccess(ResponseInfo arg0) {
				//下载成功，开始安装
				progressDialog.dismiss();
				intaller();
				
				
			}
			@Override
			public void onLoading(long total, long current, boolean isUploading) {
				// TODO Auto-generated method stub
				super.onLoading(total, current, isUploading);
				progressDialog.setMax((int) total);
				progressDialog.setProgress((int) current);
			}
		});
		
	}
	/**
	 * <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <data android:scheme="content" />  content:// : 内容提供者协议
            <data android:scheme="file" /> file : 从文件中获取数据
            <data android:mimeType="application/vnd.android.package-archive" />
        </intent-filter>
	 */
	protected void intaller() {
		Intent intent = new Intent();
		intent.setAction("android.intent.action.VIEW");
		intent.addCategory("android.intent.category.DEFAULT");
		intent.setDataAndType(Uri.fromFile(new File("mnt/sdcard/a.apk")), "application/vnd.android.package-archive");
		startActivityForResult(intent, REQUST);
		
		
	}
	@Override
	protected void onActivityResult(int requestCode, int resultCode, Intent data) {
		enterHome()	;
		super.onActivityResult(requestCode, resultCode, data);
	}
	public void enterHome(){
		Intent intent = new Intent(this, HomeActivity.class);
		startActivity(intent);
		finish();
		
	}

	
    
}
