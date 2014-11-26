<properties linkid="develop-notificationhubs-tutorials-get-started-nokia-x" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="piyushjo" solutions="" manager="kirillg" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-nokia-x" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="piyushjo" />

# 알림 허브 시작

<div class="dev-center-tutorial-selector sublanding"><a href="/ko--kr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows 범용">Windows 범용</a><a href="/ko--kr/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ko--kr/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ko--kr/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ko--kr/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ko--kr/documentation/articles/notification-hubs-nokia-x-get-started/" title="Nokia X" class="current">Nokia X</a><a href="/ko--kr/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ko--kr/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

이 항목에서는 **Azure 알림 허브**를 사용하여 **Nokia X**의 Android 응용 프로그램에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 Nokia 알림 서비스를 사용하여 푸시 알림을 받는 빈 Android 앱을 만듭니다. 완료하면 알림 허브를 사용하여 앱을 실행하는 모든 장치로 푸시 알림을 브로드캐스트할 수 있습니다.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 차례로 안내합니다.

-   [Nokia 알림 서비스 구성][Nokia 알림 서비스 구성]
-   [알림 허브 구성][알림 허브 구성]
-   [알림 허브에 앱 연결][알림 허브에 앱 연결]
-   [앱에 알림을 보내는 방법][앱에 알림을 보내는 방법]
-   [앱 테스트][앱 테스트]

<div class="dev-callout"><strong>참고</strong> <p>이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/ko--kr/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko--kr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.</p></div>

이 자습서를 사용하려면 다음이 필요합니다.

1.  Nokia X 개발 환경 - [여기][여기]에서 지침에 따라 구성 기능. Nokia X 특정 패키지를 설치하고 지침에 따라 Nokia X 에뮬레이터를 설정해야 합니다.
2.  Nokia X 장치 설정(선택 사항) - [여기][1]에서 지침에 따라 구성 가능
3.  Android SDK(Eclipse를 사용하는 것으로 가정) - [여기][2]에서 다운로드 가능
4.  Mobile Services Android SDK - [여기][3]<a>에서 다운로드 가능

## <span id="register"></span></a>Nokia 알림 서비스 구성

1.  [Nokia Notifications API Developer Console][Nokia Notifications API Developer Console]에 로그인합니다.

2.  **Create services** 탭으로 이동하고 **Sender ID** 및 **Service description**을 제공하여 새 서비스를 만듭니다.

    ![][0]

3.  서비스가 성공적으로 만들어지면 **Sender ID** 및 **Authorization Key**를 기록해 둡니다.

4.  **My services** 탭으로 이동하여 모든 생성된 서비스를 개별 **Sender ID** 및 **Authorization Key**와 함께 나열할 수 있습니다.

    ![][4]

5.  자세한 내용은 이 [링크][링크]를 참조하세요.

## <span id="configure-hub"></span></a>알림 허브 구성

1.  [Azure 관리 포털][Azure 관리 포털]에 로그온하고 화면 맨 아래에 있는 **+새로 만들기**를 클릭합니다.

2.  **앱 서비스**, **서비스 버스**, **알림 허브**, **빠른 생성**을 차례로 클릭합니다.

    ![][5]

3.  알림 허브의 이름을 입력하고 원하는 지역과 선택적으로 네임스페이스를 선택한 후 **Create a new Notification Hub**를 클릭합니다.

    ![][6]

4.  방금 만든 알림 허브로 이동합니다. 왼쪽에서 서비스 버스 탭을 클릭하고 알림 허브를 만든 네임스페이스, Notification Hubs 탭을 차례로 클릭합니다.

    ![][7]

5.  생성된 알림 허브의 위쪽에서 **Configure** 탭을 클릭합니다.

    ![][8]

6.  아래로 스크롤하여 **nokia x notification settings**을 찾고 Nokia 알림 서비스에서 얻은 인증 키를 붙여넣고 페이지 아래쪽에서 **Save** 단추를 클릭합니다.

    ![][9]

7.  페이지 위쪽에서 **Dashboard** 탭을 선택하고 아래쪽에서 **Connection Information**을 클릭합니다.

    ![][10]

8.  나중에 자습서에서 사용할 **DefaultListenSharedAccessSignature** 및 **DefaultFullSharedAccessSignature**에 대한 SAS 연결 문자열 두 개를 기록해 둡니다.

## <span id="connect-hub"></span></a>알림 허브에 앱 연결

### 새로운 Android 프로젝트 만들기

1.  Eclipse ADT에서 새로운 Android 프로젝트를 만듭니다(File -\> New -\> Android Application).

2.  Minimum Required SDK가 **API 16: Android 4.1 (Jelly Bean)**로 설정되어 있으며 다음 두 개의 SDK 항목이 사용 가능한 최신 버전으로 설정되어 있는지 확인합니다. Next를 선택하고 마법사의 지침을 따르며, **빈 활동**을 만들 수 있도록 **Create activity**를 선택했는지 확인합니다. 다음 상자에 있는 기본 시작 관리자 아이콘을 그대로 사용하고, 마지막 상자에서 **Finish**를 클릭합니다.

    ![][11]

3.  프로젝트 빌드 대상이 올바르게 설정되었는지 확인합니다(이 샘플에서는 Platform = 4.1.2 및 API Level = 16). 프로젝트를 마우스 오른쪽 단추로 클릭하고 Properties를 선택하고 Project Properties 대화 상자에서 Android를 선택합니다.

    ![][12]

4.  Nokia 알림 서비스의 JAR 파일을 프로젝트에 추가합니다. 이 Nokia 알림 도우미 라이브러리 **push.jar**에서는 GCM 도우미 라이브러리와 비슷한 API를 제공합니다.
    Project Properties -\> Java Build Path -\> Libraries -\> Add External JARs로 이동하고 **\\extras\\nokia\\nokia\_x\_services\\libs\\nna\\push.jar**에 있는 **push.jar**을 추가합니다. 라이브러리에 대한 Javadoc은 Eclipse IDE가 설치된 **\\extras\\nokia\\nokia\_x\_services\\javadocs\\nna**에 있습니다.

5.  Package Explorer에서 이 push.jar 라이브러리를 프로젝트의 \\libs 디렉터리에 복사해야 합니다.

6.  알림 허브 Android SDK를 [여기][3]에서 다운로드합니다. .zip 파일의 압축을 풀고 Package Explorer에서 notificationhubs\\notification-hubs-0.3.jar 파일을 프로젝트의 \\libs 디렉터리로 복사합니다.

    <div class="dev-callout"><b>참고</b>
<p>후속 SDK 릴리스에서는 파일 이름 끝에 있는 숫자가 변경될 수도 있습니다.</p>
</div>

### 코드 추가

1.  **AndroidManifest.xml** 파일을 열어 응용 프로그램 요소를 다음 코드 줄로 바꾸고 `[YourPackageName]`을 Android 응용 프로그램을 만들 때 지정한 패키지 이름으로 바꿉니다.

        <!-- Push Notifications connects to Internet services. -->
        <uses-permission android:name="android.permission.INTERNET" />

        <!-- Keeps the processor from sleeping when a message is received. -->
        <uses-permission android:name="android.permission.WAKE_LOCK" />

         <!--
         Creates a custom permission so only this app can receive its messages.

         NOTE: the permission *must* be called PACKAGE.permission.C2D_MESSAGE,
               where PACKAGE is the application's package name.
        -->
        <permission 
            android:name="[YourPackageName].permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="[YourPackageName].permission.C2D_MESSAGE" />

        <uses-permission android:name="com.nokia.pushnotifications.permission.RECEIVE" />

        <application
            android:allowBackup="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >
            <activity
                android:name="[YourPackageName].MainActivity"
                android:label="@string/app_name" >
                <intent-filter>
                    <action android:name="android.intent.action.MAIN" />
                    <category android:name="android.intent.category.LAUNCHER" />
                </intent-filter>
            </activity>

            <receiver
                android:name="com.nokia.push.PushBroadcastReceiver"
                android:permission="com.nokia.pushnotifications.permission.SEND" >
                <intent-filter>

                    <!-- Receives the actual messages. -->
                    <action android:name="com.nokia.pushnotifications.intent.RECEIVE" />
                    <!-- Receives the registration id. -->
                    <action android:name="com.nokia.pushnotifications.intent.REGISTRATION" />
                    <category android:name="[YourPackageName]" />
                </intent-filter>
            </receiver>

            <service android:name="[YourPackageName].PushIntentService" />
        </application> 

2.  Package Explorer에서 `src` 노드 아래의 패키지를 마우스 오른쪽 단추로 클릭하고 **New**와 **Class**를 차례로 클릭하고 **ConfigurationSettings.java**라는 새 클래스를 만듭니다.
    ![][13]

    그리고 나서 샘플에서 사용되는 상수를 정의하는 다음 코드를 추가합니다.

        public class ConfigurationSettings {
            public static String NotificationHubName = "";
            public static String NotificationHubConnectionString = "";
            public static String SenderId = "";
        }

    위의 상수를 Nokia push console의 **SenderId** 구성 및 관리 포털의 **NotificationHubName**/**NotificationHubConnectionString**(DefaultListenSharedAccessSignature)으로 채웁니다.

3.  **MainActivity.java**에서 다음 import 문을 추가합니다.

        import com.nokia.push.PushRegistrar;

    그리고 나서 onCreate 메서드에 다음 코드를 추가합니다.

        // Make sure the device has the proper dependencies.
        PushRegistrar.checkDevice(this);

        // Make sure the manifest was properly set - comment out this line
        // while developing the app, then uncomment it when it's ready
        PushRegistrar.checkManifest(this);

        // Register the device with the Nokia Notification service
        PushRegistrar.register(this, ConfigurationSettings.SenderId);

4.  **PushIntentService.java**라는 다른 새 클래스를 추가하고, 알림 허브에 등록하고 이 알림 허브에서 받은 메시지를 처리할 다음 코드를 추가합니다.

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import android.util.Log;

        import com.microsoft.windowsazure.messaging.NotificationHub;
        import com.nokia.push.PushBaseIntentService;

        /**
         * IntentService responsible for handling push notification messages.
         */
        public class PushIntentService extends PushBaseIntentService {

            private NotificationManager mNotificationManager;
            private static NotificationHub hub;
            public static final int NOTIFICATION_ID = 1;
            private static final String TAG = "NokiaXPush/PushIntentService";

            /**
             * Constructor.
             */
            public PushIntentService() {
            }

            @Override
            protected String[] getSenderIds(Context context) {
                return new String[] { ConfigurationSettings.SenderId };
            }   

            @Override
            protected void onRegistered(Context context, String registrationId) {
                Log.i(TAG, "Nokia Registration ID \"" + registrationId + "\"");
                RegisterWithNotificationHub(context, registrationId);
            }

            public static void RegisterWithNotificationHub(Context context, String registrationId) {
                if (hub == null) {
                    hub = new NotificationHub(
                            ConfigurationSettings.NotificationHubName, 
                            ConfigurationSettings.NotificationHubConnectionString, 
                            context);
                }
                try {
                    hub.register(registrationId);
                    Log.i(TAG, "Registered with Notification Hub - '" 
                            + ConfigurationSettings.NotificationHubName + "'" 
                            + " with RegistrationID - '"
                            + registrationId + "'");
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }

            @Override
            protected void onMessage(Context context, Intent intent) {
                String message = intentExtrasToString(intent.getExtras());
                Log.i(TAG, "Received message. Extras: " + message);
                generateNotification(context, message);
            }

            /**
             * Extracts the key-value pairs from the given Intent extras and returns
             * them in a string.
             */
            private String intentExtrasToString(Bundle extras) {
                StringBuilder sb = new StringBuilder();
                sb.append("{ ");

                for (String key : extras.keySet()) {
                    sb.append(sb.length() <= 2 ? "" : ", ");
                    sb.append(key).append("=").append(extras.get(key));
                }

                sb.append(" }");
                return sb.toString();
            }

            /**
             * Issues a notification to inform the user that server has sent a message.
             */
            private void generateNotification(Context context, String message)
            {
                mNotificationManager = (NotificationManager)
                        context.getSystemService(Context.NOTIFICATION_SERVICE);

                PendingIntent contentIntent = PendingIntent.getActivity(context, 0,
                    new Intent(context, MainActivity.class), 0);

                NotificationCompat.Builder mBuilder =
                    new NotificationCompat.Builder(context)
                    .setSmallIcon(R.drawable.ic_launcher)
                    .setContentTitle("Notification Hub Demo")
                    .setStyle(new NotificationCompat.BigTextStyle()
                             .bigText(message))
                    .setContentText(message);

                mBuilder.setContentIntent(contentIntent);
                mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }

            @Override
            protected void onError(Context arg0, String errorId) {
                Log.i(TAG, "Received error:  " + errorId);
            }

            @Override
            protected void onUnregistered(Context arg0, String errorId) {
                Log.i(TAG, "Received recoverable error: " + errorId);
            }
        }

## <a name="send"></a>앱에 알림을 보내는 방법

[REST 인터페이스][REST 인터페이스]를 사용하여 모든 백 엔드에서 알림 허브를 사용하여 알림을 보낼 수 있습니다. 이 자습서에서는 .NET 콘솔 앱을 사용하여 이를 보여 줍니다.

1.  새 Visual C# 콘솔 응용 프로그램을 만듭니다.

    ![][14]

2.  [WindowsAzure.ServiceBus NuGet 패키지][WindowsAzure.ServiceBus NuGet 패키지]를 사용하여 Azure 서비스 버스 SDK에 대한 참조를 추가합니다. Visual Studio 주 메뉴에서 **도구**, **라이브러리 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다. 그런 다음 콘솔 창에서 다음을 입력하고 Enter 키를 누릅니다.

        Install-Package WindowsAzure.ServiceBus

3.  Program.cs 파일을 열고 다음 using 문을 추가합니다.

        using Microsoft.ServiceBus.Notifications;

4.  `Program` 클래스에 다음 메서드를 추가합니다.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<DefaultFullSharedAccessSignatureSASConnectionString>", "<hub name>");
            await hub.SendNokiaXNativeNotificationAsync("{\"data\" : {\"payload\":\"" + "Hello from Azure" + "\"}}");
        }

5.  그런 다음 Main 메서드에 다음 줄을 추가합니다.

         SendNotificationAsync();
         Console.ReadLine();

## <a name="test-app"></a>앱 테스트

실제 휴대폰에서 이 앱을 테스트하려면 USB 케이블로 컴퓨터에 연결하면 됩니다.

에뮬레이터로 이 앱을 테스트하려면:

1.  Eclipse 상단 도구 모음에서 Run을 클릭하고 앱을 선택합니다.

2.  앱이 연결된 휴대폰으로 로드되거나, 에뮬레이터가 시작된 후 앱이 로드되고 실행됩니다.

3.  앱이 Nokia 알림 서비스에서 registrationId를 검색하고 알림 허브에 등록됩니다.

    <div class="dev-callout"><b>참고</b>
<p>
Android 앱이 알림 허브에 등록될 수 없으면 'Eclipse Logcat' 로그에 다음 메시지가 표시됩니다.
'Registered with Notification Hub - '<yourNotificationHubName>' with RegistrationID - '<RegistrationIdReturnedByNokiaNotificationService'' </p>
</div>

4.  .Net 콘솔 응용 프로그램을 사용할 때 테스트 알림을 보내려면 Visual Studio에서 F5 키를 눌러 응용 프로그램을 실행합니다. 그러면 장치나 에뮬레이터의 위쪽 알림 영역에 나타날 알림이 전송됩니다.

 


  [Nokia 알림 서비스 구성]: #register
  [알림 허브 구성]: #configure-hub
  [알림 허브에 앱 연결]: #connect-hub
  [앱에 알림을 보내는 방법]: #send
  [앱 테스트]: #test-app
  [여기]: http://developer.nokia.com/resources/library/nokia-x/getting-started/environment-setup.html
  [1]: http://developer.nokia.com/resources/library/nokia-x/getting-started/device-setup.html
  [2]: http://go.microsoft.com/fwlink/?linkid=389797&clcid=0x409
  [3]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
  [Nokia Notifications API Developer Console]: https://console.push.nokia.com/ncm/Web/index.jsp
  [0]: ./media/notification-hubs-nokia-x-get-started/NokiaConsole.png
  [4]: ./media/notification-hubs-nokia-x-get-started/NokiaConsoleService.png
  [링크]: http://developer.nokia.com/resources/library/nokia-x/nokia-notifications/nokia-notifications-developer-guide.html
  [Azure 관리 포털]: https://manage.windowsazure.com/
  [5]: ./media/notification-hubs-nokia-x-get-started/AzureManagementPortal.png
  [6]: ./media/notification-hubs-nokia-x-get-started/AzureManagementCreateNH.png
  [7]: ./media/notification-hubs-nokia-x-get-started/NHConfigureTopItem.png
  [8]: ./media/notification-hubs-nokia-x-get-started/NHConfigure.png
  [9]: ./media/notification-hubs-nokia-x-get-started/NHConfigureNokiaX.png
  [10]: ./media/notification-hubs-nokia-x-get-started/NHDashboard.png
  [11]: ./media/notification-hubs-nokia-x-get-started/NewAndroidApp.png
  [12]: ./media/notification-hubs-nokia-x-get-started/AndroidBuildTarget.png
  [13]: ./media/notification-hubs-nokia-x-get-started/NewJavaClass.png
  [REST 인터페이스]: http://msdn.microsoft.com/ko--kr/library/windowsazure/dn223264.aspx
  [14]: ./media/notification-hubs-nokia-x-get-started/ConsoleProject.png
  [WindowsAzure.ServiceBus NuGet 패키지]: http://nuget.org/packages/WindowsAzure.ServiceBus/
