Applihelp SDK for Unity（Android）
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
ApplihelpはあなたのUnity（Android）アプリケーションにヘルプサポート機能を提供します。  
Applihelp SDKはお問い合わせメッセージの送信と回答の受信をサポートします。

**[[⬆]](#TOC)**

<a name="Requirements">Requirements</a>
--------------------------------------------------
- Android 2.3.3(API Level10) or later
- JDK 6 or later

**[[⬆]](#TOC)**

<a name="Installtion">Installation</a>
--------------------------------------------------  

### SDKについて
SDKを利用するためには事前に[Applihelp](http://console.applihelp.com)のアカウントを取得し、以下の情報を登録している必要があります。

- Androidアプリケーションパッケージ名(例：com.example.sample)  
- Google API Key(PUSH通知を利用する場合)  


### SDKのインポート
SDKを利用したいUnityプロジェクトにおいて、以下の手順でapplihelp.unitypackageをインポートします。  
Unityの統合開発環境で、Assets > Import Package > Custom Package...を選択し、applihelp.unitypackageを選択します  
なお、Applihelpでは下記【1】～【3】の3つの外部ライブラリを利用しています。  
**既に利用しているライブラリがある場合はチェックをはずしてください。**  
※ google-play-services_libのチェックをはずす場合は、チェックの箇所が複数に分かれる場合がありますのでご注意ください。  
※ google-play-services.jar を直接アプリケーション内にコピーするとクラッシュする場合があります。  
【3】の様にAndroidライブラリプロジェクトとして取り込み、アプリと一緒にビルドする必要があります。

チェックの確認が済んだらImportボタンをクリックします。  
applihelp.unitypackage展開後のフォルダ構成は下記の通りです。

<pre>
/
└─Assets	// UnityのAssetsフォルダです。
   └─Plugins
  　　 ├─Android		//Applihelp SDKを含む、Android関連プログラム一式
 　　  |　├─libs
  　　 |　|　├─apphelp_sdk.jar		//Applihelp SDK本体
   　　|　|  ├─android-async-http-1.4.3.jar ---【1】外部ライブラリ
   　　|　|  └─android-support-v4.jar ---【2】外部ライブラリ
   　　|　|
   　　|　├─res		//Applihelp SDKが利用するresファイル群
   　　|　├─Applihelp_AndroidManifest_sample.xml
   　　|　├─applihelp_plugin.jar		//Applihelp SDKのメソッドを呼び出す関数群
   　　|  └─google-play-services_lib ---【3】外部ライブラリ
   　　|　
   　　└─AppliHelp
   　 　　└─Scripts
　　　　　　　├─AppHelpPlugin.cs　//※このクラスの各メソッドでAppliHelpの各機能を利用します
　　　　　　　├─AppHelpPluginAndroid.cs　　//Androidのapplihelp_plugin.jarのメソッドを呼び出す関数群
　　　　　　　└─AppHelpUsingSample.cs　　　//AppHelpPlugin.csを利用するサンプルプログラム
</pre>


外部ライブラリの詳細についてはApplihelp SDK for Androidの[Installation](https://github.com/flexfirm/applihelp_android_sdk/blob/master/DeveloperGuide_1.3.0.md#installation)「外部ライブラリ追加」をご参照ください。

### AndroidManifest.xmlの編集
SDKを利用するためにあなたのUnityプロジェクトの`Assets/Plugins/Android/AndroidManifest.xml`を編集してください。  
（もし上記の場所に`AndroidManifest.xml`が存在しない場合は  
`Assets/Plugins/Android/Applihelp_AndroidManifest_sample.xml`を参考にして作成してください。）

#### Minimum SDK Version  
android:minSdkVersionは10以上を指定してください。

```xml
<uses-sdk
	android:minSdkVersion="10"
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
Activityに関するAndroidManifest.xmlの詳細についてはApplihelp SDK for Androidの[Installation](https://github.com/flexfirm/applihelp_android_sdk/blob/master/DeveloperGuide_1.3.0.md#installation)「AndroidManifest.xmlの編集」をご参照ください。  


#### Receiver
Push通知を利用する場合`<application>`〜`</application>`に以下の`receiver`を追加してください。  

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
### Applihelpの初期設定を行う  

<h3 name="initialize">SenderID設定</h3>
このメソッドによりGCMのSenderIDをセットします。  
Push通知(GCM)を利用する場合は、__Applihelpの各機能を利用する前に必ず実行してください。__  
Push通知(GCM)を利用しない場合は実行する必要はありません。
```CS
//(C#)
void Start()
{
#if UNITY_ANDROID
	//GCMのSenderIDを設定
	AppHelpPlugin.setSenderID("Your-Sender-ID");
#endif
}
	
```
- `Your-Sender-ID`：GCM(Google Cloud Messaging)のSender ID  
まだSender IDを取得していない場合、以下を参考にSender IDを取得してください。  
[Google Cloud Messaging Getting Started](http://developer.android.com/google/gcm/gs.html)  


### Applihelp　メイン画面表示
Applihelpのメイン画面を表示します。  

```CS
//Applihelpメイン画面表示(C#)
AppHelpPlugin.showAppHelp();
```

例えばボタンを設置してApplihelpのメイン画面を呼び出す場合は以下のようになります。  
```CS
//(C#)
void OnGUI()
{
    // demo button show Applihelp
    if (GUI.Button(new Rect(50,50, 200, 100), "お問い合わせはこちら"))
    {
        AppHelpPlugin.showAppHelp();
    }
}

```
</br>


### お問い合わせ画面表示

Applihelpのお問い合わせ画面を表示します。  
ユーザ名が未設定の場合はプロフィール入力画面が表示されます。  
```CS
//(C#)
AppHelpPlugin.showRegisterIssue();
```

</br>

### ユーザ名取得・設定
キャッシュに保存されているユーザ名を取得します。  
```CS
//(C#)
string userName = AppHelpPlugin.getUserName();
```

お問い合わせ時のユーザ名をあらかじめ設定することが可能です。  
初回お問い合わせ時にユーザ名の入力を求められます。しかし、事前にユーザ名を設定することによって、それをスキップすることが可能です。  
```CS
//(C#)
AppHelpPlugin.setUserName("User-Name");
```
</br>

### カスタム情報を設定する  
アプリで取得できる任意の情報（以降、「カスタム情報」）を、ApplihelpのWebコンソールで確認することができます。
 任意のkeyとvalueの組み合わせで複数のカスタム情報を以下のように設定できます。
この情報は、以下のメソッドを呼び出した後のユーザーの問合せ送信ごと、もしくはメッセージ送信ごとに一緒に送信されます。
 送信した情報は、Messages画面で最新の1件のみ確認できます。  
```CS
//(C#)
Dictionary<string,string> dictionary 
	= new Dictionary<string,string> ();
dictionary.Add( "user_id", "3000" );
dictionary.Add( "name", "たけし" );
dictionary.Add( "in_app_purchase", "true" );
AppHelpPlugin.setCustomInfo(dictionary);
```  

カスタム情報をクリアする場合は以下のメソッドを呼び出します。  
```CS
//(C#)
AppHelpPlugin.clearCustomInfo();
```  

</br>

### GCM登録ID取得・設定
キャッシュに保存されているGCM登録IDを取得します。  
```CS
//(C#)
string GcmRegistrationId = AppHelpPlugin.getGcmRegistrationId();
```
<br>

GCM登録IDをあらかじめ設定することが可能です。  
Applihelpを内部で初期化する際にGCM登録IDを取得します。  しかし、事前にGCM登録IDを設定することによって、それをスキップすることが可能です。  
```CS
//(C#)
AppHelpPlugin.setGcmRegistrationId("GCM-Registration-ID");
```
</br>
### 新着通知数取得
1度も取得されていない未読メッセージの件数を取得します。 
```CS
//(C#)
AppHelpPlugin.getNotificationCount("ReceiveNotification-GameObjectName");
```
`ReceiveNotification-GameObjectName`：新着通知数を受け取るgameObject名
<br>

問い合わせた際の戻り値（新着通知数）は以下のコールバック
メソッドで取得します。  
取得に失敗した場合はcountに"fail"が渡されます。  

**なお、このコールバックメソッドを記述したscriptは  
`AppHelpPlugin.getNotificationCount("ReceiveNotification-GameObjectName")`メソッドで指定したGameObjectにアタッチされている必要があります**

```CS
//(C#)
public void getNotificationCountCallBack(string count){
	if (count != "fail") {
		Debug.Log("新着通知数＝"+count+"件です");
	}
}
```
</br>

### PUSH通知受信(GCM)
お問い合わせに対して新着回答がある場合、PUSH通知を受信することが可能です。  
なお、Push通知受信時の端末の挙動（通知メッセージや、バイブ・音・ライトなど）は  
`Assets/Plugins/Android/res/values-ja/ah_unity_strings.xml`で設定します。  
設定値の詳細は上記ファイルのコメントをご参照ください。

### 文字列
Applihelpが使用する文字列については、Applihelp SDK for Androidの[usage](https://github.com/flexfirm/applihelp_android_sdk/blob/master/DeveloperGuide_1.3.0.md#usage)「文字列」をご参照ください。

### 外観
Applihelpの外観については、Applihelp SDK for Androidの[usage](https://github.com/flexfirm/applihelp_android_sdk/blob/master/DeveloperGuide_1.3.0.md#usage)「外観」をご参照ください。


**[[⬆]](#TOC)**

<a name="Changelogs">Changelogs</a>
--------------------------------------------------
- [Ver.1.3]Released on July ??, 2014

- [Ver.1.0.0]～[Ver.1.2.1] for Android And for iOS

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
