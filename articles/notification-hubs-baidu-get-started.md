<properties pageTitle="Azure 알림 허브 시작" description="Azure 알림 허브를 사용하여 알림을 푸시하는 방법에 대해 알아봅니다." services="notification-hubs" documentationCenter="android" authors="piyushjo" manager="dwrede" editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.tgt_pltfrm="" 
	ms.workload="mobile" 
	ms.date="10/03/2014" 
	ms.author="piyushjo"/>

# 알림 허브 시작

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-kr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Store C#">Windows 스토어 C#</a><a href="/ko-kr/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ko-kr/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ko-kr/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ko-kr/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ko-kr/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu" class="current">Baidu</a><a href="/ko-kr/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ko-kr/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Baidu 클라우드 푸시는 모바일 장치로 푸시 알림을 보내는 데 사용할 수 있는 중국어 클라우드 서비스입니다. 이 서비스는 서로 다른 앱 스토어, 푸시 서비스 및 일반적으로 GCM(Google 클라우드 메시징)에 연결되지 않은 Android 장치의 가용성이 있어서 Android로 푸시 알림을 전달하는 것이 복잡한 중국에서 특히 유용합니다. 

이 자습서를 사용하려면 다음이 필요합니다.

+ Android SDK(Eclipse를 사용하는 것으로 가정) - <a href="http://go.microsoft.com/fwlink/?LinkId=389797">여기에서 다운로드 가능</a>
+ [모바일 서비스 Android SDK]
+ [Baidu 푸시 Android SDK]

>[AZURE.NOTE] 이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 차례로 안내합니다.

* [Baidu 계정 만들기](#createBaiduAccount)
* [Baidu 개발자로 등록](#registerBaiduDeveloper)
* [Baidu 클라우드 푸시 프로젝트 만들기](#createBaiduPushProject)
* [알림 허브 구성](#configure-hub)
* [알림 허브에 앱 연결](#connecting-app)
* [앱에 알림 보내기](#send)

## <a id="createBaiduAccount"></a>Baidu 계정 만들기

Baidu를 사용하려면 계정을 만들어야 합니다. 계정이 이미 있는 경우 Baidu 계정을 사용하여 [Baidu 포털]에 로그인한 후 다음 단계로 건너뜁니다. 그렇지 않으면 새 Baidu 계정을 만드는 방법에 대한 아래 지침을 참조하세요.  

1. [Baidu 포털]로 이동하여 登录(로그인) 링크를 클릭합니다. 立即注册을 클릭하여 새 계정 등록 프로세스를 시작합니다. 

   	![][1]

2. 필요한 세부 정보(전화/메일 주소, 암호 및 확인 코드)를 입력하고 등록을 클릭합니다.

   	![][2]

3. 입력한 메일 주소로 Baidu 계정을 활성화할 수 있는 링크가 포함된 메일을 받게 됩니다. 

   	![][3]

4. 메일 계정으로 로그인하고 Baidu 활성화 메일을 연 다음 활성화 링크를 클릭하여 Baidu 계정을 활성화합니다. 

   	![][4]

활성화된 Baidu 계정이 있으면 해당 계정으로 [Baidu 포털]에 로그인합니다. 

## <a id="registerBaiduDeveloper"></a>Baidu 개발자로 등록

1. [Baidu 포털]에 로그인한 후 **更多>>(추가)**를 클릭합니다.

  	![][5]

2. **站长与开发者服务(웹 마스터 및 개발자 서비스)** 섹션까지 아래로 스크롤한 후 **百度开放云平台(Baidu 개방형 클라우드 플랫폼)**을 클릭합니다. 

  	![][6]

3. 다음 페이지의 오른쪽 위에서 **开发者服务(개발자 서비스)**를 클릭합니다. 

  	![][7]

4. 다음 페이지의 오른쪽 위에 있는 메뉴에서 **注册开发者(등록된 개발자)**를 클릭합니다. 

  	![][8]

5. 이름, 설명 및 확인 문자 메시지를 수신할 휴대폰 번호를 입력하고 **送验证码(확인 코드 보내기)**를 클릭합니다. 국제 전화 번호의 경우 국가 번호를 괄호로 묶어야 합니다. 예를 들어 미국 번호의 경우 **(1)1234567890**이 됩니다.

  	![][9]

6. 그러면 다음 예와 같이 확인 번호가 포함된 문자 메시지를 받게 됩니다.

  	![][10] 

7. 메시지에 포함된 확인 번호를 **验证码(확인 코드)**에 입력합니다. 

8. 마지막으로, Baidu 계약 내용에 동의하고 **提交(제출)**을 클릭하여 개발자 등록을 완료합니다. 등록이 완료되면 다음 페이지가 표시됩니다.

  	![][11] 

## <a id="createBaiduPushProject"></a>Baidu 클라우드 푸시 프로젝트 만들기

Baidu 클라우드 푸시 프로젝트를 만들면 앱 ID, API 키 및 비밀 키를 받게 됩니다.

1. [Baidu 포털]에 로그인한 후 **更多>>(추가)**를 클릭합니다.

  	![][5]

2. **站长与开发者服务(웹 마스터 및 개발자 서비스)** 섹션까지 아래로 스크롤한 후 **百度开放云平台(Baidu 개방형 클라우드 플랫폼)**을 클릭합니다. 

  	![][6]

3. 다음 페이지의 오른쪽 위에서 **开发者服务(개발자 서비스)**를 클릭합니다. 

  	![][7]

4. 다음 페이지의 **云服务(클라우드 서비스)** 섹션에서 **云推送(클라우드 푸시)**를 클릭합니다. 

  	![][12]

5. 등록된 개발자인 경우 최상위 메뉴에 **管理控制台(관리 콘솔)**이 표시됩니다. **开发者服务管理(개발자 서비스 관리)**를 클릭합니다. 

  	![][13]

6. 다음 페이지에서 **创建工程(프로젝트 만들기)**를 클릭합니다.

  	![][14]

7. 응용 프로그램 이름을 입력하고 **创建(만들기)**를 클릭합니다.

  	![][15]

8. 성공적으로 만들면 **AppID**, **API 키** 및 **비밀 키**가 포함된 페이지가 표시됩니다. 나중에 사용할 **API 키**와 **비밀 키**를 적어 둡니다. 

  	![][16]

9. 왼쪽 창에서 **云推送(클라우드 푸시)**를 클릭하여 푸시 알림에 대해 프로젝트를 구성합니다. 

  	![][31]

10. 다음 페이지에서 **推送设置(푸시 설정)** 단추를 클릭합니다.

	![][32]  

11. 구성 페이지에서 **应用包名(응용 프로그램 패키지)** 필드에 Android 프로젝트에 사용할 패키지 이름을 추가하고 **保存设置(저장)**를 클릭합니다.  

	![][33]

**保存成功！(저장되었습니다.)** 메시지가 표시됩니다.

## <a id="configure-hub"></a>알림 허브 구성

1. [Azure 관리 포털]에 로그온하고 화면 맨 아래에 있는 **+새로 만들기**를 클릭합니다.

2. **앱 서비스**, **서비스 버스**, **알림 허브**, **빠른 생성**을 차례로 클릭합니다.
 
3. **알림 허브**의 이름을 입력하고 이 알림 허브를 만들 **지역** 및 **네임스페이스**를 선택한 후 **새 알림 허브 만들기**를 클릭합니다.  

  	![][17]

4. 알림 허브를 만든 네임스페이스를 클릭한 후 맨 위에 있는 **알림 허브**를 클릭합니다. 

  	![][18]

5. 만든 알림 허브를 선택하고 최상위 메뉴에서 **구성**을 클릭합니다.

  	![][19]

6. **Baidu 알림 설정** 섹션까지 아래로 스크롤한 후 이전에 Baidu 콘솔에서 Baidu 클라우드 푸시 프로젝트에 대해 받은 **API 키**와 **비밀 키**를 입력합니다. 이러한 값을 입력한 후 **저장**을 클릭합니다. 

  	![][20]

7. 맨 위에서 알림 허브에 대한 **대시보드** 탭을 클릭한 후 **연결 문자열 보기**를 클릭합니다.

  	![][21]

8. Access 연결 정보 창에 있는 **DefaultListenSharedAccessSignature** 및 **DefaultFullSharedAccessSignature**를 적어 둡니다. 

    ![][22]

## <a id="connecting-app"></a>알림 허브에 앱 연결

1. Eclipse ADT에서 새로운 Android 프로젝트를 만듭니다(File -> New -> Android Application).

    ![][23]

2. **Application Name**을 입력하고 **Minimum Required SDK** 버전이 **API 16: Android 4.1**로 설정되었는지 확인합니다.

    ![][24]

3. **Next**를 클릭하고 **Create Activity** 창이 표시될 때까지 마법사를 계속 진행합니다. **Blank Activity**가 선택되었는지 확인하고 **Finish**를 선택하여 새로운 Android 응용 프로그램을 만듭니다. 

    ![][25]

4. **Project Build Target**이 올바르게 설정되었는지 확인합니다.

    ![][26]

5. [모바일 서비스 Android SDK]를 다운로드하여 압축을 풀고 **notificationhubs** 폴더를 연 후 **notification-hubs-x.y.jar** 파일을 Eclipse 프로젝트의  *libs* 폴더로 복사하고 *libs* 폴더를 새로 고칩니다.

6. [Baidu 푸시 Android SDK]를 다운로드하여 압축을 풀고 **libs** 폴더를 연 후  *pushservice-x.y.z* jar 파일과 Android 응용 프로그램의 **libs** 폴더에 있는  *armeabi* 및 *mips* 폴더를 복사합니다. 

7. Android 프로젝트의 **AndroidManifest.xml**을 열고 Baidu SDK에 필요한 권한을 추가합니다.

	    <uses-permission android:name="android.permission.INTERNET" />
	    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
	    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
	    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
	    <uses-permission android:name="android.permission.WRITE_SETTINGS" />
	    <uses-permission android:name="android.permission.VIBRATE" />
	    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
	    <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
	    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
	    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
	    <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
	    <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />

8.  *android:name* 속성을 *yourprojectname*을 대체하여 **AndroidManifest.xml**에 있는 *application* 속성을 추가합니다(예: **com.example.BaiduTest**). 이 프로젝트 이름은 Baidu 콘솔에서 구성한 이름과 일치해야 합니다. 

		<application android:name="yourprojectname.DemoApplication"

9.  *yourprojectname*을 대체하여 다음 구성을 .MainActivity 작업 요소 뒤의 응용 프로그램 요소 내에 추가합니다(예: **com.example.BaiduTest**).

		<receiver android:name="yourprojectname.MyPushMessageReceiver">
		    <intent-filter>
		        <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
		        <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
		        <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
		    </intent-filter>
		</receiver>
		
		<receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
		    android:process=":bdservice_v1">
		    <intent-filter>
		        <action android:name="android.intent.action.BOOT_COMPLETED" />
		        <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
				<action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
		    </intent-filter>
		</receiver>
        
        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>                   
        </receiver>
        
        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1"  >
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
            </intent-filter>
        </service>

9. **ConfigurationSettings.java**라는 새 클래스를 프로젝트에 추가합니다. 

    ![][28]

    ![][29]

10. 이 클래스에 다음 코드를 추가합니다.

		public class ConfigurationSettings {
		        public static String API_KEY = "...";
				public static String NotificationHubName = "...";
				public static String NotificationHubConnectionString = "...";
			}
	
	앞서 Baidu 클라우드 프로젝트에서 검색한 값으로 *API_KEY* 값을 설정하고, Azure 포털의 알림 허브 이름으로 *NotificationHubName*을 설정하고, Azure 포털의 DefaultListenSharedAccessSignature로 *NotificationHubConnectionString*을 설정합니다. 

11. **DemoApplication.java**라는 새 클래스를 추가하고 여기에 다음 코드를 추가합니다.

		import com.baidu.frontia.FrontiaApplication;
		
		public class DemoApplication extends FrontiaApplication {
		    @Override
		    public void onCreate() {
		        super.onCreate();
		    }
		}

12. **MyPushMessageReceiver.java**라는 다른 새 클래스를 추가하고 여기에 아래 코드를 추가합니다. 이 클래스는 Baidu 푸시 서버에서 수신된 푸시 알림을 처리합니다.

		import java.util.List;
		import android.content.Context;
		import android.os.AsyncTask;
		import android.util.Log;
		import com.baidu.frontia.api.FrontiaPushMessageReceiver;
		import com.microsoft.windowsazure.messaging.NotificationHub;
		
		public class MyPushMessageReceiver extends FrontiaPushMessageReceiver {
		    /** TAG to Log */
			public static NotificationHub hub = null;
			public static String mChannelId, mUserId;
		    public static final String TAG = MyPushMessageReceiver.class
		            .getSimpleName();
		    
			@Override
		    public void onBind(Context context, int errorCode, String appid,
		            String userId, String channelId, String requestId) {
		        String responseString = "onBind errorCode=" + errorCode + " appid="
		                + appid + " userId=" + userId + " channelId=" + channelId
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		        mChannelId = channelId;
		        mUserId = userId;
		        
		        try {
		       	 if (hub == null) {
		                hub = new NotificationHub(
		                		ConfigurationSettings.NotificationHubName, 
		                		ConfigurationSettings.NotificationHubConnectionString, 
		                		context);
		                Log.i(TAG, "Notification hub initialized");
		            }
		        } catch (Exception e) {
		           Log.e(TAG, e.getMessage());
		        }
		        
		        registerWithNotificationHubs();
			}
		    
		    private void registerWithNotificationHubs() {
		       new AsyncTask<Void, Void, Void>() {
		          @Override
		          protected Void doInBackground(Void... params) {
		             try {
		            	 hub.registerBaidu(mUserId, mChannelId);
		            	 Log.i(TAG, "Registered with Notification Hub - '" 
		     	    			+ ConfigurationSettings.NotificationHubName + "'" 
		     	    			+ " with UserId - '"
		     	    			+ mUserId + "' and Channel Id - '"
		     	    			+ mChannelId + "'");
		             } catch (Exception e) {
		            	 Log.e(TAG, e.getMessage());
		             }
		             return null;
		         }
		       }.execute(null, null, null);
		    }
		    
		    @Override
		    public void onSetTags(Context context, int errorCode,
		            List<String> sucessTags, List<String> failTags, String requestId) {
		        String responseString = "onSetTags errorCode=" + errorCode
		                + " sucessTags=" + sucessTags + " failTags=" + failTags
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onDelTags(Context context, int errorCode,
		            List<String> sucessTags, List<String> failTags, String requestId) {
		        String responseString = "onDelTags errorCode=" + errorCode
		                + " sucessTags=" + sucessTags + " failTags=" + failTags
		                + " requestId=" + requestId;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onListTags(Context context, int errorCode, List<String> tags,
		            String requestId) {
		        String responseString = "onListTags errorCode=" + errorCode + " tags="
		                + tags;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onUnbind(Context context, int errorCode, String requestId) {
		        String responseString = "onUnbind errorCode=" + errorCode
		                + " requestId = " + requestId;
		        Log.d(TAG, responseString);
		    }
		
		    @Override
		    public void onNotificationClicked(Context context, String title,
		            String description, String customContentString) {
		        String notifyString = "title=\"" + title + "\" description=\""
		                + description + "\" customContent=" + customContentString;
		        Log.d(TAG, notifyString);
		    }
		
		    @Override
		    public void onMessage(Context context, String message,
		            String customContentString) {
		        String messageString = "message=\"" + message + "\" customContentString=" + customContentString;
		        Log.d(TAG, messageString);
		    }
		}

13. **MainActivity.java**를 열고 **onCreate** 메서드에 다음 코드를 추가합니다.

	        PushManager.startWork(getApplicationContext(),
	                PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

그런 다음 맨 위에 다음 import 문을 추가합니다.
			import com.baidu.android.pushservice.PushConstants;
			import com.baidu.android.pushservice.PushManager;

## <a id="send"></a>앱에 알림 보내기

알림 허브를 사용하여 <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST 인터페이스</a>알림을 보냅니다. 이 자습서에서는 .NET 콘솔 앱을 사용하여 이를 보여 줍니다. 

1. 새 Visual C# 콘솔 응용 프로그램을 만듭니다.

	![][30]

2. 그런 다음 <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet 패키지</a>를 사용하는 Azure Service Bus SDK에 참조를 추가합니다. Visual Studio 주 메뉴에서 **도구**, **라이브러리 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다. 그런 다음 콘솔 창에서 다음을 입력하고 Enter 키를 누릅니다.

        Install-Package WindowsAzure.ServiceBus

3. Program.cs 파일을 열고 다음 using 문을 추가합니다.

        using Microsoft.ServiceBus.Notifications;

4.  `Program` 클래스에 다음 메서드를 추가하고 *DefaultFullSharedAccessSignatureSASConnectionString* 및 *NotificationHubName*을 보유한 값으로 대체합니다. 

		private static async void SendNotificationAsync()
		{
			NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
			string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
			var result = await hub.SendBaiduNativeNotificationAsync(message);
		}

5. 그런 다음 Main 메서드에 다음 줄을 추가합니다.

         SendNotificationAsync();
		 Console.ReadLine();

## <a name="test-app"></a>앱 테스트

실제 휴대폰에서 이 앱을 테스트하려면 USB 케이블로 컴퓨터에 연결하면 됩니다.

에뮬레이터로 이 앱을 테스트하려면:

1. Eclipse 상단 도구 모음에서 Run을 클릭하고 앱을 선택합니다. 

2. 앱이 연결된 휴대폰으로 로드되거나, 에뮬레이터가 시작된 후 앱이 로드되고 실행됩니다.

3. 앱이 Baidu 푸시 알림 서비스에서 'userId' 및 'channelId'를 검색하고 알림 허브를 등록합니다.
	
4.	.Net 콘솔 응용 프로그램을 사용할 때 테스트 알림을 보내려면 Visual Studio에서 F5 키를 눌러 응용 프로그램을 실행합니다. 그러면 장치나 에뮬레이터의 위쪽 알림 영역에 나타날 알림이 전송됩니다. 


<!-- Images. -->
[1]: ./media/notification-hubs-baidu-get-started/BaiduRegistration.png
[2]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png
[3]: ./media/notification-hubs-baidu-get-started/BaiduConfirmation.png
[4]: ./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png
[5]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png
[6]: ./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png
[7]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperServices.png
[8]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperRegistration.png
[9]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationInput.png
[10]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationConfirmation.png
[11]: ./media/notification-hubs-baidu-get-started/BaiduDevConfirmationFinal.png
[12]: ./media/notification-hubs-baidu-get-started/BaiduCloudPush.png
[13]: ./media/notification-hubs-baidu-get-started/BaiduDevSvcMgmt.png
[14]: ./media/notification-hubs-baidu-get-started/BaiduCreateProject.png
[15]: ./media/notification-hubs-baidu-get-started/BaiduCreateProjectInput.png
[16]: ./media/notification-hubs-baidu-get-started/BaiduProjectKeys.png
[17]: ./media/notification-hubs-baidu-get-started/AzureNHCreation.png
[18]: ./media/notification-hubs-baidu-get-started/NotificationHubs.png
[19]: ./media/notification-hubs-baidu-get-started/NotificationHubsConfigure.png
[20]: ./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png
[21]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionStringView.png
[22]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionString.png
[23]: ./media/notification-hubs-baidu-get-started/EclipseNewProject.png
[24]: ./media/notification-hubs-baidu-get-started/EclipseProjectCreation.png
[25]: ./media/notification-hubs-baidu-get-started/EclipseBlankActivity.png
[26]: ./media/notification-hubs-baidu-get-started/EclipseProjectBuildProperty.png
[27]: ./media/notification-hubs-baidu-get-started/EclipseBaiduReferences.png
[28]: ./media/notification-hubs-baidu-get-started/EclipseNewClass.png
[29]: ./media/notification-hubs-baidu-get-started/EclipseConfigSettingsClass.png
[30]: ./media/notification-hubs-baidu-get-started/ConsoleProject.png
[31]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig1.png
[32]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig2.png
[33]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig3.png

<!-- URLs. -->
[모바일 서비스 Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Baidu 푸시 Android SDK]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Azure 관리 포털]: https://manage.windowsazure.com/
[Baidu 포털]: http://www.baidu.com/



<!--HONumber=45--> 
