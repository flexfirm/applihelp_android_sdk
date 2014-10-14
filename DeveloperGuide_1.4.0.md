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
ヘルプサポート機能とは以下の機能を指します。
- よくある質問とその回答（FAQ）を簡単にアプリに組み込める機能
- お問い合わせメッセージの送信と回答の受信をサポートする機能  

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

Installationについて[導入動画（音声付き）](https://www.youtube.com/watch?v=HTOHgYxcrsw&feature=youtu.be/)をご用意しておりますのでご利用ください。
[![アプリヘルプ導入動画](https://i.ytimg.com/vi/HTOHgYxcrsw/sddefault.jpg)](https://www.youtube.com/watch?v=HTOHgYxcrsw&feature=youtu.be/)

SDKファイルの構成は下記の通りです。

<pre>
/
├─docs         // APIリファレンスです。
├─sample       // サンプルアプリケーションです。Eclipseでプロジェクトとしてインポートしてください。
└─sdk
    ├─apphelp_sdk.jar     // SDK本体です。
    └─res                 // SDKで利用するリソースファイルです。
        ├─anim
        ├─drawable-hdpi
        ├─layout
        ├─values
        ├─values-ja
        ├─values-v11
        └─values-v14
</pre>


### Applihelpライブラリ追加
EclipseまたはIntelliJ(Android Studio)のAndroidアプリケーションプロジェクトへapphelp_sdk.jarを追加してください。

### 外部ライブラリ追加
以下の外部ライブラリをAndroidアプリケーションプロジェクトへ追加してください。

- [Asynchronous Http Client](https://github.com/loopj/android-async-http/releases)  
android-async-http-1.4.3.jarをダウンロードし、Androidアプリケーションプロジェクトへ追加してください。  
**※ 1.4.3以降では動作しません。必ず1.4.3を利用してください。**

- [Google Play Services SDK](http://loopj.com/android-async-http/)  
Android SDK ManagerからGoogle Play Services SDKをダウンロードし、Androidアプリケーションプロジェクトへ追加してください。
詳しいセットアップ方法は以下を参考にしてください。  
[Setup Google Play Services SDK | Android Developers](http://developer.android.com/google/play-services/setup.html)  
**※ google-play-services.jar を直接アプリケーション内にコピーするとクラッシュする場合があります。必ずAndroidライブラリプロジェクトとして取り込み、アプリと一緒にビルドしてください。**


### リソースファイル配置
SDKに含まれる以下のリソースファイルをあなたのAndroidアプリケーションプロジェクトへ配置してください。

#### アニメーション
- `res/anim/ah_current_activity_slide_left_out.xml`
- `res/anim/ah_current_activity_slide_right_out.xml`
- `res/anim/ah_next_activity_slide_left_in.xml`
- `res/anim/ah_next_activity_slide_right_in.xml`

#### レイアウト
- `res/layout/ah_footer.xml`
- `res/layout/ah_main_activity.xml`
- `res/layout/ah_messages_activity.xml`
- `res/layout/ah_messages_list_item_admin.xml`
- `res/layout/ah_messages_list_item_user.xml`
- `res/layout/ah_register_issue_activity.xml`
- `res/layout/ah_faq_items_activity.xml`
- `res/layout/ah_faq_item_detail_activity.xml `

#### 画像
- `res/drawable-hdpi/message_balloon_admin.9.png`
- `res/drawable-hdpi/message_balloon_user.9.png`

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
`<manifest>`～`</manifest>`に以下のパーミッションを追加してください。  

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.INTERNET" />
```

- `android.permission.ACCESS_NETWORK_STATE`はネットワーク接続情報を取得するために使用します。
- `android.permission.INTERNET`はApplihelpのサーバと通信するために使用します。
</br>

Push通知を利用する場合
`<manifest>`～`</manifest>`に以下のパーミッションを追加してください。  

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
なお、`your.application.package`はあなたのAndroidアプリケーションパッケージ名に変更する必要があります。  

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
<activity android:name="jp.flexfirm.apphelp.view.AHFaqItemsActivity"
        android:configChanges="orientation|screenSize"
        android:theme="@style/AHTheme" />
<activity android:name="jp.flexfirm.apphelp.view.AHFaqItemDetailActivity"
        android:configChanges="orientation|screenSize"
        android:theme="@style/AHTheme" />
```
プロジェクト・ビルド・ターゲットがAPI Level13より低い場合`android:configChanges`にscreenSizeは指定できません。  
開発環境のバージョンによっては、`<application>`〜`</application>`に以下の追記が必要な場合があります。

```xml
<meta-data
        android:name="com.google.android.gms.version"
        android:value="@integer/google_play_services_version" />
```

### Receiver  
Push通知を利用する場合 `<application>`〜`</application>`に以下の receiver を追加してください。 

```xml
<receiver
    android:name="jp.flexfirm.apphelp.AppHelpGcmBroadcastReceiver"
    android:permission="com.google.android.c2dm.permission.SEND" >
    <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="your.application.package" />
    </intent-filter>
</receiver>
```

`your.application.package`はあなたのAndroidアプリケーションパッケージ名に変更する必要があります。 


**[[⬆]](#TOC)**

<a name="Usage">Usage</a>
--------------------------------------------------
### Applihelpインスタンスを生成する

Applihelpを利用するために必ず実行してください。  

```java
final AppHelp appHelp = new AppHelp(getApplicationContext());
```
</br>
<h3 name="initialize">SenderID設定</h3>
このメソッドによりGCMのSenderIDをセットします。  
Push通知(GCM)を利用する場合は、__Applihelpの各機能を利用する前に必ず実行してください。__  
Push通知(GCM)を利用しない場合は実行する必要はありません。
```java
appHelp.setSenderID("Your-Sender-ID");
```
- `Your Sender ID`：GCM(Google Cloud Messaging)のSender ID  
まだSender IDを取得していない場合、以下を参考にSender IDを取得してください。  
[Google Cloud Messaging Getting Started](http://developer.android.com/google/gcm/gs.html)  

<br>
### FAQ画面、もしくは問合せ履歴を表示
FAQが登録されていればFAQ画面を表示します。  
登録されていなければ問合せ履歴を表示します。  

```java
appHelp.showAppHelp(MainActivity.this);
```
例えば以下のように実装します。
```java

@Override
protected void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	setContentView(R.layout.activity_main);
	
	// Applihelpのインスタンス生成
	final AppHelp appHelp = new AppHelp(getApplicationContext());
	//GCMのSenderIDを設定
	appHelp.setSenderID("123456789123");

	Button showAppHelpButton = (Button) findViewById(R.id.showFaqButton);
	showAppHelpButton.setOnClickListener(new OnClickListener() {
		@Override
		public void onClick(View v) {

			//FAQ画面、もしくは問合せ履歴を表示
			appHelp.showAppHelp(MainActivity.this);
		}
	});
}

```
※ActionBarActivityを利用する場合は`onCreateView()`内で実装するなどしてください。

### FAQ画面表示
ApplihelpのFAQ画面を表示します。

```java
appHelp.showFaq(MainActivity.this);
```

### お問い合わせ履歴画面表示
Applihelpのお問い合わせ履歴画面を表示します。

```java
appHelp.showIssueHistory(MainActivity.this);
```

### お問い合わせ画面表示
Applihelpのお問い合わせ画面を表示します。  
```java
appHelp.showRegisterIssue(MainActivity.this);
```

</br>

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
</br>

### カスタム情報を設定する  
アプリで取得できる任意の情報（以降、「カスタム情報」）を、ApplihelpのWebコンソールで確認することができます。  
任意のkeyとvalueの組み合わせで複数のカスタム情報を以下のように設定できます。  
この情報は、以下のメソッドを呼び出した後のユーザーの問合せ送信ごと、もしくはメッセージ送信ごとに一緒に送信されます。  
送信した情報は、Messages画面で最新の1件のみ確認できます。

```java
LinkedHashMap<String, String> map = new LinkedHashMap<String, String>();
map.put( "user_id", "3000" );
map.put( "name", "たけし" );
map.put( "appli_point", "4" );
map.put( "in_app_purchase", "true" );

appHelp.setCustomInfo(map);
```
カスタム情報をクリアする場合は以下のメソッドを呼び出します。  
```java
appHelp.clearCustomInfo();
```
</br>

### GCM登録ID取得・設定
キャッシュに保存されているGCM登録IDを取得します。  
```java
String GcmRegistrationId = appHelp.getGcmRegistrationId();
```
<br>

GCM登録IDをあらかじめ設定することが可能です。  
Applihelpを内部で初期化する際にGCM登録IDを取得します。
しかし、事前にGCM登録IDを設定することによって、それをスキップすることが可能です。

```java
appHelp.setGcmRegistrationId("GCM-Registration-ID");
```
</br>
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
</br>

### PUSH通知受信(GCM)
お問い合わせに対して新着回答がある場合、PUSH通知を受信することが可能です。  
なお、Push通知受信時の端末の挙動（通知メッセージや、バイブ・音・ライトなど）は  
`res/values-ja/ah_strings.xml`で設定します。  
設定値の詳細は上記ファイルのコメントをご参照ください。  
__※アプリヘルプ以外のPush通知機能を利用する際の注意点__   
アプリヘルプ以外のPush通知機能を導入している場合、アプリヘルプのPush通知にそのReceiverが  
反応する場合があります。  
これは、AndroidのReceiverが通知をBroadcastで受けとるためです。
もしアプリヘルプからのPush通知に対して、他のReceiverが反応する場合は、そのReceiverに
「自分のPushにのみ反応する」ように修正する必要があります。



### PUSH通知受信(GCM)をカスタマイズする
PUSH通知を受信したときの挙動をカスタマイズすることが可能です。  
カスタマイズしない場合はこの作業は必要ありません。

#### Receiverの作成
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

		// メッセージタイプによってNotificationに表示するメッセージを切替え
		Bundle extras = aIntent.getExtras();
		int type = Integer.parseInt(extras.getString("type"));
		
		String ticker = "";
		String contentTitle = "";
		String contentText = "";
		
		switch (type) {
		case 0:
			// 通常メッセージ
			ticker = "問合せに対する新着回答があります";
			contentTitle = "サンプルアプリ-問合せ-";
			contentText = "新着回答有り";
			break;
		case 1:
			// レビュー依頼メッセージ
			ticker = "アプリのレビュー依頼があります。";
			contentTitle = "サンプルアプリ-レビューのお願い-";
			contentText = "是非アプリのレビューをお願いします。";
			break;
		default:
			ticker = "問合せに対する新着回答があります";
			contentTitle = "サンプルアプリ-問合せ-";
			contentText = "新着回答有り";
			break;
		}

		// Notification内容作成
		NotificationCompat.Builder mBuilder = new NotificationCompat.Builder(mContext)
			.setSmallIcon(R.drawable.ic_launcher)
			.setTicker(ticker)
			.setContentTitle(contentTitle)
			.setContentText(contentText)
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
#### Push通知受信データ形式
PUSH通知で受信するデータ形式は以下の通りです。  

キー			| 値			| 説明 			
:-------- 	| :-------: | :------------------------------------------
data_type 	| string  	| `Applihelp`固定。ApplihelpからのPushを示すキー
issue_id 	| int  		| 問合せID
type 		| int  		| メッセージタイプ。（0:通常、1:レビューリクエスト）

#### メッセージ画面へ遷移する  
Push通知で受信した問合せID（isuue_id）を利用することにより、直接メッセージ画面へ遷移させることができます。

```java
//問合せIDを指定してメッセージ画面へ遷移する
AppHelp apphele = new AppHelp(mContext);
apphele.showMessage(issue_id);
```
#### AndroidManifest.xmlの編集
`<receiver   android:name="jp.flexfirm.apphelp.AppHelpGcmBroadcastReceiver"`
～`</receiver>`
を削除してください。  
代わりに、あなたが作成したReceiverを`<application>`〜`</application>`に追加してください。  
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
<meta-data android:name="com.google.android.gms.version"
        android:value="@integer/google_play_services_version" />
```
`your.application.package`はあなたのAndroidアプリケーションパッケージ名に置換えてください。  

### 文字列
Applihelpが使用する文字列は英語および日本語を用意しています。それぞれ下記ファイルが対応します。対応言語がない場合は英語が表示されます。
* 英語（デフォルト）：`res/values/ah_strings.xml`  
* 日本語：`res/values-ja/ah_strings.xml`  

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

#### ブランド表記の削除
通常Applihelpの全画面にはブランド表記がありますが、任意で削除することが可能です。  
もし削除したい場合は`res/layout/ah_footer.xml`を編集してください。  
例えば、以下のように`<TextView   android:id="@+id/ah_brand"`～`/>`に`android:visibility="gone""`を追記します。

```xml
<TextView
	android:id="@+id/ah_brand"
	：
	android:visibility="gone" />
```

### 「バックアップと復元」に関する注意  
※（SDK Ver.1.4.0以降）  
OSの機能「バックアップと復元」によってアプリのデータを復元した場合、GCMのRegistrationIDを更新しなければ、Pushが正しく飛ばなくなる可能性があります（詳細は下記をご参照ください）  
http://developer.android.com/google/gcm/adv.html  
データの復元時には以下のメソッドで一度RegistrationIDをクリアしてください。
```java
// GCM　の設定情報をクリア
AppHelp appHelp = new AppHelp(mContext);
appHelp.clearGcmSettings();

```

**[[⬆]](#TOC)**

<a name="Changelogs">Changelogs</a>
--------------------------------------------------
- [Ver.1.4.0]Released on 
	- `apphelp_sdk.jar`  
		- [追加]Webコンソールで設定したFAQを表示できる機能    
		- [追加]デフォルトのPush通知Receiver`AppHelpGcmBroadcastReceiver`  
	- [追加][Installation](#Installation)／ソースファイル配置／アニメーション  
	- [Installation](#Installation)／ソースファイル配置／レイアウト  
		- [追加]`res/layout/ah_faq_items_activity.xml`  
		- [追加]`res/layout/ah_faq_item_detail_activity.xml` 
		- [削除]`res/layout/ah_register_profile_activity.xml`  
	- [Installation](#Installation)／AndroidManifest.xmlの編集／Activity  
		- [追加]`jp.flexfirm.apphelp.view.AHFaqItemsActivity`  
		- [追加]`jp.flexfirm.apphelp.view.AHFaqItemDetailActivity`  
		- [追加]`<meta-data　…/>`
	- [Usage](#Usage)／Applihelpの初期設定を行う　
		- [追加] SenderID設定  
		- [追加] FAQ画面表示  
		- [更新] PUSH通知受信(GCM)  
		- [追加] PUSH通知受信(GCM)をカスタマイズする  
		- [追加] 「バックアップと復元」に関する注意   
- [Ver.1.3.0] Unity対応
- [Ver.1.2.1]
	- **[Usage](#Usage)**	
		- [削除]初期化（アクティベーション）  
		- [追加]SenderID設定  
		- [更新]Applihelp　メイン画面表示  
		初期化（アクティベーション）を実行しておく旨の記載を削除
		- [更新]カスタム情報を設定する  
		問合せもしくはメッセージを送信するごとに一緒に送信する（1度きりではない）ように変更  
		カスタム情報をクリアするメソッドを追加
		- [更新]お問い合わせ画面表示  
		初期化（アクティベーション）を実行しておく旨の記載を削除
		- [更新]GCM登録ID取得・設定  
		初期化（アクティベーション）を実行しておく旨の記載を削除
		- [更新]新着通知数取得  
		初期化（アクティベーション）を実行しておく旨の記載を削除
		- [更新]PUSH通知受信(GCM)  
		初期化（アクティベーション）を実行しておく旨の記載を削除
		Manifest内にmeta-dataの記載を追加
- [Ver.1.2.0]
	- **[Usage](#Usage)**	
		- [追加]Applihelpインスタンスを生成する  
		- [更新]初期化（アクティベーション）  
		呼出すタイミングをApplihelpの一部のメソッドを呼出す直前に変更
		- [追加]カスタム情報を設定する  
- [Ver.1.1.1] Only Applihelp for iOS
- [Ver.1.1.0]Released on Dec 7, 2013  
	- `apphelp_sdk.jar`  
		- メッセージとして「ストアレビューリクエスト」を受信できる機能を追加
		- 端末情報の取得項目に「着信モード」を追加
	- [移動]`res/values/ah_strings.xml` -> `res/values-ja/ah_strings.xml`  
	下記項目の記述が更新／追加されています。  
		- [更新]`ah_main_activity_footer_button`  
		- [更新]`ah_register_issue_activity_device_label`  
		- [追加]`ah_footer_brand`  
	- [追加]`res/values/ah_strings.xml`  
	英語定義として既存の日本語ファイルと置き換わっています。
	- [追加]`res/layout/ah_footer.xml`  
	- [更新]`res/layout/ah_main_activity.xml`  
	- [更新]`res/layout/ah_messages_activity.xml`  
	- [更新]`res/layout/ah_messages_list_item_admin.xml`  
	- [更新]`res/layout/ah_messages_list_item_user.xml`  
	- [更新]`res/layout/ah_register_issue_activity.xml`  
	- [更新]`res/layout/ah_register_profile_activity.xml`  

- [Ver.1.0.0]Released on Oct 20, 2013

**[[⬆]](#TOC)**

<a name="License">License and Credits</a>
--------------------------------------------------
このソフトウェアは、以下のオープンソースソフトウェアを使用しています。  

- **[Asynchronous Http Client](http://loopj.com/android-async-http/)
© James Smith All rights reserved.**  
The Android Asynchronous Http Client is released under the Android-friendly Apache License, Version 2.0. Read the full license here: [http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0)

**[[⬆]](#TOC)**

---
© [KSK Co., Ltd.](http://www.flexfirm.jp) All rights reserved.
