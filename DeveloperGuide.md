Applihelp SDK for Android
==================================================
[Applihelp](http://www.applihelp.com) |
[Facebook](http://facebook.com/applihelp) |
[Twitter](http://twitter.com/applihelp)

---

<a name="TOC">Table of Contents</a>
--------------------------------------------------
1. [Introduction](#Introduction)
1. [Requirements](#Requirements)
1. [Installtion](#Installtion)
1. [Usage](#Usage)
1. [Changelogs](#Changelogs)
1. [License](#License)

<a name="Introduction">Introduction</a>
--------------------------------------------------
ApplihelpはあなたのAndroidアプリケーションにヘルプサポート機能を提供します。  
Applihelp SDKはお問い合わせメッセージの送信と回答の受信をサポートします。

**[[⬆]](#TOC)**

<a name="Requirements">Requirements</a>
--------------------------------------------------
- Android 2.2(API Level8) or later
- JDK 6 or later

**[[⬆]](#TOC)**

<a name="Installtion">Installation</a>
--------------------------------------------------
### SDKについて
SDKを利用するためには事前に[Applihelp](http://console.applihelp.com)のアカウントを取得し、以下の情報を登録している必要があります。

- Androidアプリケーションパッケージ名(例：com.example.sample)  
- Google API Key(PUSH通知を利用する場合)  

SDKファイルの構成は下記の通りです。

<pre>
/
├─sample       // サンプルアプリケーションです。Eclipseでプロジェクトとしてインポートしてください。
└─sdk
    ├─apphelp_sdk.jar     // SDK本体です。
    └─res                 // SDKで利用するリソースファイルです。
        ├─drawable-xhdpi
        ├─layout
        ├─values
        ├─values-v11
        └─values-v14
</pre>

### Applihelpライブラリ追加
EclipseまたはIntelliJ(Android Studio)のAndroidアプリケーションプロジェクトへapphelp_sdk.jarを追加してください。

### 外部ライブラリ追加
以下の外部ライブラリをAndroidアプリケーションプロジェクトへ追加してください。

- [Asynchronous Http Client](https://github.com/loopj/android-async-http/releases)  
android-async-http-1.4.3.jarをダウンロードし、Androidアプリケーションプロジェクトへ追加してください。

- [Google Play Services SDK](http://loopj.com/android-async-http/)  
Android SDK ManagerからGoogle Play Services SDKをダウンロードし、Androidアプリケーションプロジェクトへ追加してください。
詳しいセットアップ方法は以下を参考にしてください。  
[Setup Google Play Services SDK | Android Developers](http://developer.android.com/google/play-services/setup.html)
**※ google-play-services.jar を直接アプリケーション内にコピーするとクラッシュする場合があります。必ずプロジェクトとして取り込んでください。**

### リソースファイル配置
SDKに含まれる以下のリソースファイルをあなたのAndroidアプリケーションプロジェクトへ配置してください。

#### レイアウト
- `res/layout/ah_main_activity.xml`
- `res/layout/ah_messages_activity.xml`
- `res/layout/ah_messages_list_item_admin.xml`
- `res/layout/ah_messages_list_item_user.xml`
- `res/layout/ah_register_issue_activity.xml`
- `res/layout/ah_register_profile_activity.xml`

#### 画像
- `res/drawable-xhdpi/message_balloon_admin.9.png`
- `res/drawable-xhdpi/message_balloon_user.9.png`

#### 文字列・数値・色など
- `res/values/ah_resources.xml`
- `res/values/ah_strings.xml`

#### テーマ
- Android 2.2 or later  
`res/values/ah_theme.xml`
- Android 3.0 or later  
`res/values-v11/ah_theme.xml`
- Android 4.0 or later    
`res/values-v14/ah_theme.xml`

Applihelp SDK for AndroidはAndroid OSバージョン毎にテーマを提供しています。  
Android OSバージョン毎のリソース配置については以下を参考してください。  
[Providing Resources | Android Developers](http://developer.android.com/guide/topics/resources/providing-resources.html)

### AndroidManifest.xmlの編集
SDKを利用するためにあなたのAndroidアプリケーションプロジェクトのAndroidManifest.xmlを編集してください。

#### Minimum SDK Version  
android:minSdkVersionは8以上を指定してください。

```xml
<uses-sdk
	android:minSdkVersion="8"
	android:targetSdkVersion="18" />
```

#### Permission
以下のパーミッションを追加してください。  
`your.application.package`はあなたのAndroidアプリケーションパッケージ名に置換えてください。

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.INTERNET" />
```

- `android.permission.ACCESS_NETWORK_STATE`はネットワーク接続情報を取得するために使用します。
- `android.permission.INTERNET`はApplihelpのサーバと通信するために使用します。

#### Activity
`<application>`〜`</application>`に以下を追加してください。

```xml
<activity android:name="jp.flexfirm.apphelp.view.AHMainActivity"
	android:configChanges="orientation|screenSize"
	android:theme="@style/AHTheme" />
<activity android:name="jp.flexfirm.apphelp.view.AHMessagesActivity"
	android:configChanges="orientation|screenSize"
	android:theme="@style/AHTheme" />
<activity android:name="jp.flexfirm.apphelp.view.AHRegisterIssueActivity"
	android:configChanges="orientation|screenSize"
	android:theme="@style/AHTheme" />
<activity android:name="jp.flexfirm.apphelp.view.AHRegisterProfileActivity"
	android:configChanges="orientation|screenSize"
	android:theme="@style/AHTheme" />
```

プロジェクト・ビルド・ターゲットがAPI Level13より低い場合`android:configChanges`にscreenSizeは指定できません。

**[[⬆]](#TOC)**

<a name="Usage">Usage</a>
--------------------------------------------------
### 初期化(アクティベーション)
Applihelpを初期化します。  
Applihelpを利用するために必ず実行してください。  
`android.intent.category.LAUNCHER`カテゴリのActivityクラスにて
`onCreate(Bundle savedInstanceState)`の`super.onCreate()`直後に実行してください。

#### PUSH通知受信(GCM)あり
```java
protected void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	setContentView(R.layout.activity_main);

	// Applihelpのインスタンス生成
	final AppHelp appHelp = new AppHelp(getApplicationContext());

	// Applihelp初期化
	appHelp.install(MainActivity.this, "your.application.package", "Your-Sender-ID");
}
```

- `your.application.package`：あなたのAndroidアプリケーションパッケージ名
- `Your Sender ID`：GCM(Google Cloud Messaging)のSender ID

まだSender IDを取得していない場合、以下を参考にSender IDを取得してください。  
[Google Cloud Messaging Getting Started](http://developer.android.com/google/gcm/gs.html)


#### PUSH通知受信(GCM)なし
```java
protected void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	setContentView(R.layout.activity_main);

	// Applihelpのインスタンス生成
	final AppHelp appHelp = new AppHelp(getApplicationContext());

	// Applihelp初期化
	appHelp.install(MainActivity.this, "your.application.package", null);
}
```

- `your.application.package`：あなたのAndroidアプリケーションパッケージ名

### メイン画面表示
Applihelpのメイン画面を表示します。

```java
appHelp.showAppHelp(MainActivity.this);
```

### お問い合わせ画面表示
Applihelpのお問い合わせ画面を表示します。  
ユーザ名が未設定の場合はプロフィール入力画面が表示されます。

```java
appHelp.showRegisterIssue(MainActivity.this);
```

### ユーザ名取得・設定
キャッシュに保存されているユーザ名を取得します。  

```java
String userName = appHelp.getUserName();
```

お問い合わせ時のユーザ名をあらかじめ設定することが可能です。  
初回お問い合わせ時にユーザ名の入力を求められます。しかし、事前にユーザ名を設定することによって、それをスキップすることが可能です。

```java
appHelp.setUserName("User-Name");
```

### GCM登録ID取得・設定
キャッシュに保存されているGCM登録IDを取得します。  

```java
String GcmRegistrationId = appHelp.getGcmRegistrationId();
```

GCM登録IDをあらかじめ設定することが可能です。    
Applihelpを初期化する際にGCM登録IDを取得します。しかし、事前にGCM登録IDを設定することによって、それをスキップすることが可能です。

```java
appHelp.setGcmRegistrationId("GCM-Registration-ID");
```

### 新着通知数取得
1度も取得されていない未読メッセージの件数を取得します。

```java
// 取得成功時ハンドラ
Handler successHandler = new Handler(){
	@Override
	public void handleMessage(Message msg) {
		super.handleMessage(msg);
		int count = Integer.parseInt((msg.obj).toString());
		Toast.makeText(getApplicationContext(), "新着通知数：" + count + "件", Toast.LENGTH_LONG).show();
	}
};

// 取得失敗時ハンドラ
Handler failHandler = new Handler(){
	@Override
	public void handleMessage(Message msg) {
		super.handleMessage(msg);
		Throwable e = (Throwable) msg.obj;
		e.printStackTrace();
		Toast.makeText(getApplicationContext(), "新着通知数取得失敗", Toast.LENGTH_LONG).show();
	}
};

// 新着通知数取得
appHelp.getNotificationCount(MainActivity.this, successHandler, failHandler);
```

### PUSH通知受信(GCM)
お問い合わせに対して新着回答がある場合、PUSH通知を受信することが可能です。  

#### Receiverを作成
以下のようなReceiverを作成してください。

```java
public class GcmBroadcastReceiver extends BroadcastReceiver {
	/** 通知 ID. */
	public static final int NOTIFICATION_ID = 1;

	/** 通知マネージャ. */
	private NotificationManager mNotificationManager;
	
	/** コンテキスト. */
	private Context mContext;
	
	/**
	 * コンストラクタ.
	 */
	public GcmBroadcastReceiver() {
		super();
	}

	/**
	 * {@inheritDoc}
	 */
	public void onReceive(Context aContext, Intent aIntent) {
		// コンテキスト設定
		mContext = aContext;
		
		// PUSH通知内容取得
		Bundle extras = aIntent.getExtras();
		String dataType = extras.getString("data_type");
		
		// GCM
		GoogleCloudMessaging gcm = GoogleCloudMessaging.getInstance(aContext);
		
		// GCMメッセージタイプ取得
		String messageType = gcm.getMessageType(aIntent);
		
		Log.d(LOG_TAG, "Applihelp PUSH通知受信:" + extras.toString());
		
		// AppHelpのPUSH通知に対して処理を行う
		if (!extras.isEmpty() && "Applihelp".equals(dataType)) {
			// 必要なメッセージタイプに対して処理を行う
			if (GoogleCloudMessaging.MESSAGE_TYPE_SEND_ERROR.equals(messageType)) {
				// ERRORタイプのGCMメッセージ
				Log.d(LOG_TAG, "Send Error:" + extras.toString());
			} else if (GoogleCloudMessaging.MESSAGE_TYPE_DELETED.equals(messageType)) {
				// DELETEタイプのGCMメッセージ
				Log.d(LOG_TAG, "Deleted messages on server:" + extras.toString());
			} else if (GoogleCloudMessaging.MESSAGE_TYPE_MESSAGE.equals(messageType)) {
				// 通常タイプのGCMメッセージ
				sendNotification(aIntent);
			}
		}
	}
	
	/**
	 * Notification登録.
	 * @param aIntent PUSH通知の受信インテント
	 */
	private void sendNotification(Intent aIntent) {
		// Notificationマネージャ
		mNotificationManager = 
				(NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
		
		// Notificationクリック時に発行するインテント生成
		AppHelp appHelp = new AppHelp(mContext);
		PendingIntent pendingIntent = appHelp.getPendingIntentForNotification(mContext, aIntent);

		// Notification内容作成
		NotificationCompat.Builder mBuilder = new NotificationCompat.Builder(mContext)
			.setSmallIcon(R.drawable.ic_launcher)
			.setTicker("問合せに対する新着回答があります")
			.setContentTitle("サンプルアプリ-問合せ-")
			.setContentText("新着回答有り")
			.setVibrate(new long[] {1000, 1000, 1000, 1000, 1000})
			.setWhen(System.currentTimeMillis())
			.setLights(Color.WHITE, 1000, 500)
			.setStyle(new NotificationCompat.BigTextStyle().bigText("sample"))
			.setAutoCancel(true)
			.setContentIntent(pendingIntent);
		
		// 同じIDのNotificationをクリア
		mNotificationManager.cancel(NOTIFICATION_ID);
		
		// 通知発行
		mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
	}
}
```

Notificationを使って通知を行う場合、通知をクリックした時にメッセージ画面へ遷移するIntent(Pending Intent)を発行することが可能です。

```java
// Notificationクリック時に発行するインテント生成
AppHelp appHelp = new AppHelp(mContext);
PendingIntent pendingIntent = appHelp.getPendingIntentForNotification(mContext, aIntent);
```

#### AndroidManifest.xmlの編集
PUSH通知を利用するために以下のパーミッションを追加してください。

```xml
<uses-permission android:name="android.permission.VIBRATE" />
<uses-permission android:name="android.permission.GET_ACCOUNTS" />
<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
<uses-permission android:name="your.application.package.permission.C2D_MESSAGE" />
<permission
	android:name="your.application.package.permission.C2D_MESSAGE"
	android:protectionLevel="signature" />
```

- `android.permission.VIBRATE`はPUSH通知を受信した時、振動させるために使用します。
- `android.permission.GET_ACCOUNTS`はGCMがGoogleアカウントを必要とするため使用します。
- `com.google.android.c2dm.permission.RECEIVE`はPUSH通知を受信するために使用します。
- `your.application.package.permission.C2D_MESSAGE`はPUSH通知を受信するために使用します。

作成したReceiverを`<application>`〜`</application>`に追加してください。  
`your.application.package`はあなたのAndroidアプリケーションパッケージ名に置換えてください。

```xml
<receiver
	android:name="your.application.package.GcmBroadcastReceiver"
	android:permission="com.google.android.c2dm.permission.SEND" >
	<intent-filter>
		<action android:name="com.google.android.c2dm.intent.RECEIVE" />
		<action android:name="com.google.android.c2dm.intent.REGISTRATION" />
		<category android:name="your.application.package" />
	</intent-filter>
</receiver>
```

### 文字列
Applihelpが使用する文字列はリソースファイル`res/values/ah_strings.xml`で定義されています。  

#### 文言の変更
画面に表示される文字列を変更したい場合はリソースファイルを修正してください。  
例えば問合せ完了後に表示されるメッセージを変更したい場合は以下の項目を変更してください。
```xml
<string name="success_register_issue">\n送信が完了しました。\nお問い合わせありがとうございます。\n</string>
```

#### 多言語対応(i18n)
多言語対応(i18n)は以下のようにvaluesフォルダを言語毎に作成し、編集したah_strings.xmlを配置してください。

- `res/values-en/ah_strings.xml`
- `res/values-fr/ah_strings.xml`
- `res/values-it/ah_strings.xml`

多言語対応については以下を参考してください。  
[Supporting Different Languages | Android Developers](http://developer.android.com/training/basics/supporting-devices/languages.html)

### 外観
Applihelpが使用するテーマは`res/values/ah_theme.xml`に定義されています。  
文字サイズや背景色のようなスタイル要素群は`res/values/ah_resources.xml`に定義されています。  
外観を変更したい場合、これらのファイルを編集してください。

**[[⬆]](#TOC)**

<a name="Changelogs">Changelogs</a>
--------------------------------------------------
- [Ver.1.0]Released on Oct 20, 2013

**[[⬆]](#TOC)**

<a name="License">License and Credits</a>
--------------------------------------------------
このソフトウェアは、以下のオープンソースソフトウェアを使用しています。  

- **[Asynchronous Http Client](http://loopj.com/android-async-http/)
© James Smith All rights reserved.**  
The Android Asynchronous Http Client is released under the Android-friendly Apache License, Version 2.0. Read the full license here: [http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0)

**[[⬆]](#TOC)**

---
© 2013 [KSK Co., Ltd.](http://www.flexfirm.jp) All rights reserved.
