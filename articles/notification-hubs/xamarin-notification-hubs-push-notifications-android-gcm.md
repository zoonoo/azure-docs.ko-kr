---
title: Xamarin.Android 앱에 대한 Notification Hubs 시작 | Microsoft Docs
description: 이 자습서에서 Azure Notification Hubs를 사용하여 Xamarin.Android 응용 프로그램에 푸시 알림을 보내는 방법을 알아봅니다.
author: jwhitedev
manager: kpiteira
editor: ''
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 7fee7813bbdcf902d5f5ae2d0af7540c8899ad25
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-notification-hubs-for-xamarinandroid-apps"></a>Xamarin.Android 앱에 대한 Notification Hubs 시작
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>개요
이 자습서에서는 Azure Notification Hubs를 사용하여 Xamarin.Android 응용 프로그램에 푸시 알림을 보내는 방법을 보여 줍니다. FCM(Firebase Cloud Messaging)을 사용하여 푸시 알림을 받는 빈 Xamarin.Android 앱을 만듭니다. 완료하면 알림 허브를 사용하여 앱을 실행하는 모든 장치로 푸시 알림을 브로드캐스트할 수 있습니다. 완성된 코드는 [NotificationHubs 앱][GitHub] 샘플에서 사용할 수 있습니다.

이 자습서에서는 Notification Hubs를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

이 자습서에 대해 완료된 코드는 GitHub의 [여기][GitHub]서 찾을 수 있습니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 사용하려면 다음이 필요합니다.

* Windows의 경우 [Xamarin이 포함된 Visual Studio] 또는 OS X의 경우 [Mac용 Visual Studio]
* 활성 Google 계정

이 자습서를 완료해야 다른 모든 Xamarin.Android 앱용 Notification Hubs 자습서를 진행할 수 있습니다.

> [!IMPORTANT]
> 이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F)을 참조하세요.
> 
> 

## <a name="enable-firebase-cloud-messaging"></a>Firebase Cloud Messaging 사용
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-your-notification-hub"></a>알림 허브 구성
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li><p>맨 위에 있는 <b>구성</b> 탭을 선택하고 이전 섹션에서 받은 <b>API 키</b> 값을 입력한 후 <b>저장</b>을 선택합니다.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

알림 허브가 FCM과 작동하도록 구성되었으며, 알림을 받고 푸시 알림을 보내기 위해 앱을 등록하는 연결 문자열이 있습니다.

## <a name="connect-your-app-to-the-notification-hub"></a>알림 허브에 앱 연결
먼저 새 프로젝트를 만듭니다. 

1. Visual Studio에서 **새 솔루션** > **Android 앱**을 차례로 선택하고 **다음**을 선택합니다.
   
      ![Visual Studio - 새 Android 프로젝트 만들기][22]

2. **앱 이름**과 **식별자**를 입력합니다. 지원하려는 **대상 플랫폼**, **다음**, **만들기**를 차례로 선택합니다.
   
      ![Visual Studio - Android 앱 구성][23]

    그러면 새 Android 프로젝트가 만들어집니다.

3. 솔루션 보기에서 새 프로젝트를 마우스 오른쪽 단추로 클릭하고 **옵션**를 선택하여 프로젝트 속성을 엽니다. **Build** 섹션에서 **Android Application** 항목을 선택합니다.
   
    **Package name** 의 첫 문자는 소문자여야 합니다.
   
   > [!IMPORTANT]
   > 패키지 이름의 첫 문자는 소문자여야 합니다. 그렇지 않으면 아래와 같이 푸시 알림을 위해 앱을 등록할 때 응용 프로그램 매니페스트 오류가 발생합니다.
   > 
   > 
   
      ![Visual Studio - Android 프로젝트 옵션][24]
4. 또는 **최소 Android 버전** 을 다른 API 수준으로 설정합니다.
5. **대상 Android 버전**을 대상으로 지정하려는 다른 API 버전으로 설정합니다(API 수준 8 이상이어야 함).
6. **확인**을 선택하고 프로젝트 옵션 대화 상자를 닫습니다.

### <a name="add-the-required-packages-to-your-project"></a>프로젝트에 필요한 패키지 추가

1. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **NuGet 패키지 추가**를 차례로 선택합니다.
2. **Xamarin.Azure.NotificationHubs.Android**를 검색하여 프로젝트에 추가합니다.
3. **Xamarin.Firebase.Messaging**을 검색하여 프로젝트에 추가합니다.

### <a name="set-up-notification-hubs-in-your-project"></a>프로젝트에서 알림 허브 설정

#### <a name="registering-with-firebase-cloud-messaging"></a>Firebase Cloud Messaging 등록

**AndroidManifest.xml** 파일을 열고 다음 `<receiver>` 요소를 `<application>` 요소로 삽입합니다.

        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
          </intent-filter>
        </receiver>

1. Android 앱 및 알림 허브에 대해 다음 정보를 수집합니다.
   
   * **연결 문자열 수신 대기**: [Azure Portal]의 대시보드에서 **연결 문자열 보기**를 선택합니다. 이 값에 대해 *DefaultListenSharedAccessSignature* 연결 문자열을 복사합니다.
   * **허브 이름**: [Azure Portal]의 허브 이름입니다. 예를 들어 *mynotificationhub2*입니다.
     
2. Xamarin 프로젝트에 대해 **Constants.cs** 클래스를 만들고 클래스에 다음 상수 값을 정의합니다. 자리 표시자는 해당 값으로 바꿉니다.
    
    ```csharp
        public static class Constants
        {
           public const string ListenConnectionString = "<Listen connection string>";
           public const string NotificationHubName = "<hub name>";
        }
    ```

3. **MainActivity.cs**에 다음 using 문을 추가합니다.
   
    ```csharp
        using Android.Util;
    ```

4. 앱이 실행되고 있을 때 경고 대화 상자를 표시하는 데 사용할 인스턴스 변수를 **MainActivity.cs**에 추가합니다.
   
    ```csharp
        public const string TAG = "MainActivity";
    ```

5. **MainActivity.cs**의 `OnCreate`에서 `base.OnCreate(savedInstanceState)` 다음에 다음 코드를 추가합니다.

    ```csharp   
        if (Intent.Extras != null)
        {
            foreach (var key in Intent.Extras.KeySet())
            {
                if(key!=null)
                {
                    var value = Intent.Extras.GetString(key);
                    Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
                }
            }
        }
    ```

6. 프로젝트를 마우스 오른쪽 단추로 클릭하고, 이전에 Firebase 프로젝트에서 다운로드한 `google-services.json`을 추가합니다. 추가된 파일을 마우스 오른쪽 단추로 클릭하고, 빌드 작업을 `GoogleServicesJson`으로 설정합니다.

    ![Visual Studio - google-services.json 구성][25]

7. **MyFirebaseIIDService**라는 새 클래스를 만듭니다.

8. **MyFirebaseIIDService.cs**에 다음 using 문을 추가합니다.
   
    ```csharp
        using System;
        using Android.App;
        using Firebase.Iid;
        using Android.Util;
        using WindowsAzure.Messaging;
        using System.Collections.Generic;
    ```

9. **MyFirebaseIIDService.cs**에서 **class** 선언 위에 다음을 추가하고, **FirebaseInstanceIdService**에서 클래스를 상속받도록 설정합니다.
   
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
        public class MyFirebaseIIDService : FirebaseInstanceIdService
    ```

10. **MyFirebaseIIDService.cs**에 다음 코드를 추가합니다.
   
    ```csharp
        const string TAG = "MyFirebaseIIDService";
        NotificationHub hub;

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "FCM token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }

        void SendRegistrationToServer(string token)
        {
            // Register with Notification Hubs
            hub = new NotificationHub(Constants.NotificationHubName,
                                      Constants.ListenConnectionString, this);

            var tags = new List<string>() { };
            var regID = hub.Register(token, tags.ToArray()).RegistrationId;

            Log.Debug(TAG, $"Successful registration of ID {regID}");
        }
    ```

11. 프로젝트에 대한 또 다른 클래스를 새로 만들고, 이름을 **MyFirebaseMessagingService**로 지정합니다.

12. **MyFirebaseMessagingService.cs**에 다음 using 문을 추가합니다.
    
    ```csharp
        using System;
        using System.Linq;
        using Android;
        using Android.App;
        using Android.Content;
        using Android.Util;
        using Firebase.Messaging;
    ```

13. class 선언 위에 다음을 추가하고, **FirebaseMessagingService**에서 클래스를 상속받도록 설정합니다.
    
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class MyFirebaseMessagingService : FirebaseMessagingService
    ```
    
14. **MyFirebaseMessagingService.cs**에 다음 코드를 추가합니다.
    
    ```csharp
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            if(message.GetNotification()!= null)
            {
                //These is how most messages will be received
                Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
                SendNotification(message.GetNotification().Body);
            }
            else 
            {
                //Only used for debugging payloads sent from the Azure portal
                SendNotification(message.Data.Values.First());

            }

        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new Notification.Builder(this)
                        .SetContentTitle("FCM Message")
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(messageBody)
                        .SetAutoCancel(true)
                        .SetContentIntent(pendingIntent);

            var notificationManager = NotificationManager.FromContext(this);

            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```

15. 장치 또는 로드된 에뮬레이터에서 앱을 실행합니다.

## <a name="send-notifications-from-the-portal"></a>포털에서 알림 보내기
[Azure Portal]에서 *테스트 보내기* 옵션을 사용하여 앱에서 알림 수신을 테스트할 수 있습니다. 이렇게 하면 테스트 푸시 알림을 장치에 보냅니다.

![Azure Portal - 보내기 테스트][30]

푸시 알림은 일반적으로 Mobile Services 또는 ASP.NET과 같은 백 엔드 서비스에서 호환되는 라이브러리를 통해 보내집니다. 백 엔드에서 라이브러리를 사용할 수 없는 경우 REST API를 직접 사용하여 알림 메시지를 보낼 수도 있습니다.

알림을 보내기 위해 검토할 수 있는 다른 자습서 목록은 다음과 같습니다.

* ASP.NET: [Notification Hubs를 사용하여 사용자에게 푸시 알림 보내기]를 참조하세요.
* Azure Notification Hubs Java SDK: Java에서 알림을 보내는 방법은 [Java에서 Notification Hubs를 사용하는 방법](notification-hubs-java-push-notification-tutorial.md) 을 참조하세요. 이는 Eclipse for Android Development에서 테스트되었습니다.
* PHP: [PHP에서 Notification Hubs를 사용하는 방법](notification-hubs-php-push-notification-tutorial.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
이 간단한 예제에서는 모든 Android 장치로 알림을 브로드캐스트합니다. 특정 사용자를 대상으로 하려면 [Notification Hubs를 사용하여 사용자에게 푸시 알림 보내기](영문) 자습서를 참조하세요. 사용자를 관심 그룹별로 분할하려면 [Notification Hubs를 사용하여 뉴스 속보 보내기](영문)를 참조하십시오. Notification Hubs 사용 방법에 대해 자세히 알아보려면 [Notification Hubs 지침] 및 [Android용 Notification Hubs 방법]을 참조하세요.

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Xamarin이 포함된 Visual Studio]: https://docs.microsoft.com/visualstudio/install/install-visual-studio
[Mac용 Visual Studio]: https://www.visualstudio.com/vs/visual-studio-mac/

[Azure Portal]: https://portal.azure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs 지침]: http://msdn.microsoft.com/library/jj927170.aspx
[Android용 Notification Hubs 방법]: http://msdn.microsoft.com/library/dn282661.aspx

[Notification Hubs를 사용하여 사용자에게 푸시 알림 보내기]: /manage/services/notification-hubs/notify-users-aspnet
[Notification Hubs를 사용하여 뉴스 속보 보내기]: /manage/services/notification-hubs/breaking-news-dotnet
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
