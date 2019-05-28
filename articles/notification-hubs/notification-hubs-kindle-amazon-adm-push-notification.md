---
title: Azure Notification Hubs를 사용하여 Kindle 앱에 알림 푸시 | Microsoft Docs
description: 이 자습서에서 Azure Notification Hubs를 사용하여 Kindle 애플리케이션에 푸시 알림을 보내는 방법을 알아봅니다.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 346fc8e5-294b-4e4f-9f27-7a82d9626e93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-kindle
ms.devlang: Java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/29/2019
ms.author: jowargo
ms.openlocfilehash: edd0e12460e07cfd2990cc43a9056ed06b84fb1d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926700"
---
# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Kindle 앱에 대한 Notification Hubs 시작

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

이 자습서에서는 Azure Notification Hubs를 사용하여 Kindle 애플리케이션에 푸시 알림을 보내는 방법을 보여 줍니다. ADM(Amazon Device Messaging)을 사용하여 푸시 알림을 받는 빈 Kindle 앱을 만듭니다.

이 자습서에서는 다음 작업을 수행하는 코드를 생성/업데이트합니다.

> [!div class="checklist"]
> * 개발자 포털에 새 앱 추가
> * API 키 만들기
> * 알림 허브 만들기 및 구성
> * 애플리케이션 설정
> * ADM 메시지 처리기 만들기
> * 앱에 API 키 추가
> * 앱 실행
> * 테스트 알림 보내기

## <a name="prerequisites"></a>필수 조건

- [Android Studio](https://developer.android.com/studio/?gclid=CjwKCAjwwZrmBRA7EiwA4iMzBPZ9YYmR0pbb5LtjnWhWCxe8PWrmjmeaR6ad5ksCr_j2mmkVj_-o6hoCAqwQAvD_BwE).
- [개발 환경 설정](https://developer.amazon.com/docs/fire-tablets/ft-set-up-your-development-environment.html)의 단계에 따라 Kindle에 대한 개발 환경을 설정합니다.

## <a name="add-a-new-app-to-the-developer-portal"></a>개발자 포털에 새 앱 추가

1. [Amazon 개발자 포털](https://developer.amazon.com/apps-and-games/console/apps/list.html)에 로그인합니다.
2. **Add New App**(새 앱 추가)을 선택한 다음, **Android**를 선택합니다.  

    ![새 앱 단추 추가](./media/notification-hubs-kindle-get-started/add-new-app-button.png)
1. **New App Submission**(새 앱 제출) 페이지에서 다음 단계에 따라 **애플리케이션 키**를 가져옵니다.
    1. **App title**(앱 제목)에 대한 이름을 입력합니다.
    2. **범주**(예: 교육)를 선택합니다.
    4. **Customer support email address**(고객 지원 이메일 주소) 필드에 대한 이메일 주소를 입력합니다. 
    5. **저장**을 선택합니다.

        ![New App Submission(새 앱 제출) 페이지](./media/notification-hubs-kindle-get-started/new-app-submission-page.png) 
2.  맨 위에서 **Mobile Ads**(모바일 광고) 탭으로 전환하고 다음 단계를 수행합니다. 
    1. 앱이 주로 13세 미만의 어린이를 대상으로 하는지 지정합니다. 이 자습서에서는 **No**(아니오)를 선택합니다.
    2. **제출**을 선택합니다. 

        ![Mobile Ads(모바일 광고) 페이지](./media/notification-hubs-kindle-get-started/mobile-ads-page.png)
    3. **Mobile Ads**(모바일 광고) 페이지에서 **애플리케이션 키**를 복사합니다. 

        ![애플리케이션 키](./media/notification-hubs-kindle-get-started/application-key.png)
3.  맨 위에서 **Apps & Services**(앱 및 서비스)를 선택하고 목록에서 애플리케이션을 선택합니다. 

    ![목록에서 앱을 선택합니다.](./media/notification-hubs-kindle-get-started/all-apps-select.png)
4. **Device Messaging** 탭으로 이동하여 다음 단계를 수행합니다. 
    1. **Create a New Security Profile**(새 보안 프로필 만들기)을 선택합니다.
    2. 보안 프로필에 대한 **이름**을 입력합니다. 
    3. 보안 프로필에 대한 **설명**을 입력합니다. 
    4. **저장**을 선택합니다. 
    5. 결과 페이지에서 **View Security Profile**(보안 프로필 보기)을 선택합니다. 
5. 이제 **Security Profile**(보안 프로필) 페이지에서 다음 단계를 수행합니다. 
    1. **Web Settings**(웹 설정) 탭으로 전환하여 나중에 사용할 수 있도록 **클라이언트 ID**와 **클라이언트 비밀** 값을 복사합니다. 

        ![클라이언트 ID 및 비밀 가져오기](./media/notification-hubs-kindle-get-started/client-id-secret.png) 
    2. **Android/Kindle Settings**(Android/Kindle 설정) 페이지로 전환하여 페이지를 열어둡니다. 이 값은 다음 섹션에서 입력합니다. 

## <a name="create-an-api-key"></a>API 키 만들기
1. 관리자 권한으로 명령 프롬프트를 엽니다.
2. Android SDK 폴더로 이동합니다.
3. 다음 명령을 입력합니다.

    ```shell
    keytool -list -v -alias androiddebugkey -keystore ./debug.keystore
    ```
4. **keystore** 암호에 **android**를 입력합니다.
5. **MD5** 및 **SHA256** 지문을 복사합니다. 
6. 개발자 포털로 돌아가서 **Android/Kindle Settings**(Android/Kindle 설정) 탭에서 다음 단계를 수행합니다. 
    1. **API 키에 대한 이름**을 입력합니다. 
    2. 앱에 대한 **패키지의 이름**(예: **com.fabrikam.mykindleapp**)과 **MD5** 값을 입력합니다.
        
        >[!IMPORTANT]
        > Android Studio에서 앱을 만들 때는 여기서 지정한 것과 동일한 패키지 이름을 사용합니다. 
    1. 앞에서 복사한 **MD5 서명**을 붙여넣습니다. 
    2. 앞에서 복사한 **SHA256 서명**을 붙여넣습니다.  
    3. **Generate New Key**(새 키 생성)를 선택합니다.

        ![Android/Kindle 설정 - 키 생성](./media/notification-hubs-kindle-get-started/android-kindle-settings.png)
    4. 이제 목록에서 **Show**(표시)를 선택하여 API 키를 봅니다. 

        ![Android/Kindle 설정 - API 키 표시](./media/notification-hubs-kindle-get-started/show-api-key-button.png) 
    5. **API Key Details**(API 키 세부 정보) 창에서 API 키를 복사하여 다른 곳에 저장합니다. 그런 다음, 오른쪽 위 모서리에 있는 **X**를 선택하여 창을 닫습니다. 


## <a name="create-and-configure-a-notification-hub"></a>알림 허브 만들기 및 구성

1. [Azure Portal에서 Azure 알림 허브 만들기](create-notification-hub-portal.md) 문서의 단계를 수행하여 알림 허브를 만듭니다. 
2. **설정** 메뉴 아래에서 **Amazon(ADM)** 을 선택합니다.
3. 앞에서 저장한 **클라이언트 ID**와 **클라이언트 비밀**을 붙여넣습니다. 
4. 도구 모음에서 **저장**을 선택합니다. 

    ![알림 허브에 대한 ADM 설정 구성](./media/notification-hubs-kindle-get-started/configure-notification-hub.png)
5. 왼쪽 메뉴에서 **액세스 정책**을 선택하고 **DefaultListenSharedAccessSignature** 정책에 대한 연결 문자열의 **복사** 단추를 선택합니다. 그런 후 아무 위치에나 저장합니다. 나중에 소스 코드에서 사용하게 됩니다. 

    ![알림 허브 - 연결 문자열 수신 대기](./media/notification-hubs-kindle-get-started/event-hub-listen-connection-string.png)    

## <a name="set-up-your-application"></a>애플리케이션 설정

1. Android Studio를 시작합니다. 
2. **File**(파일)을 선택하고, **New**(신규)를 가리킨 후, **New Project**(새 프로젝트)를 선택합니다. 
3. **Choose your project**(프로젝트 선택) 창의 **Phone and Tablet**(휴대폰 및 태블릿) 탭에서 **Empty Activity**(빈 활동)을 선택하고 **Next**(다음)를 선택합니다. 
4. **Configure your project**(프로젝트 구성) 창에서 다음 단계를 수행합니다.
    1. **애플리케이션의 이름**을 입력합니다. Amazon 개발자 포털에서 만든 애플리케이션 이름과 일치시킬 수 있습니다. 
    2. **패키지의 이름**을 입력합니다. 
        
        >[!IMPORTANT]
        >패키지 이름은 Amazon 개발자 포털에서 지정한 패키지 이름과 일치해야 합니다.
    3. 나머지 값을 검토하고 적절하게 업데이트합니다. 
    4. **마침**을 선택합니다. 

        ![Android 프로젝트 구성](./media/notification-hubs-kindle-get-started/new-android-studio-project.png)
5. [Amazon Developer SDK for Android](https://developer.amazon.com/sdk-download) 라이브러리를 하드 디스크에 다운로드합니다. SDK zip 파일의 압축을 풉니다.
6. Android Studio에서 폴더 구조를 **Android**에서 **Project**로 변경합니다(**Project**로 아직 설정되지 않은 경우 ). 

    ![Android Studio - 프로젝트 구조로 전환](./media/notification-hubs-kindle-get-started/android-studio-project-view.png)
7. **app**을 확장하여 트리 뷰에서 **libs** 폴더를 표시합니다.     
8. 파일 탐색기 창에서 Amazon Android SDK를 다운로드한 폴더로 이동합니다.
9. **CTRL**을 누르고 **amazon-device-messaging-1.0.1.jar** 파일을 트리 뷰의 **lib** 노드로 끌어서 놓습니다. 

    ![Android Studio - Amazon Device Messaging JAR 추가](./media/notification-hubs-kindle-get-started/drag-drop-amazon-device-messaging-jar.png)
9. **Copy** 창에서 **OK**를 선택합니다. **Copy** 창 대신 **Move** 창이 보이면 창을 닫고 **CTRL** 단추를 누른 상태에서 끌어서 놓기 작업을 시도합니다. 

    ![Android Studio - JAR 복사](./media/notification-hubs-kindle-get-started/copy-jar-window.png)
10. 다음 명령문을 **app의 build.gradle** 파일에서 **dependencies** 섹션에 추가합니다. `implementation files('libs/amazon-device-messaging-1.0.1.jar')`. 

    ![Android Studio - 앱의 build.gradle에 ADM 추가](./media/notification-hubs-kindle-get-started/adm-build-gradle.png)
11. **app**의 `Build.Gradle` 파일에서 **dependencies** 섹션에 다음 줄을 추가합니다. 

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```
12. **dependencies** 섹션 **뒤에** 다음 리포지토리를 추가합니다.

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```
13. **app**의 **build.gradle** 파일에 대한 편집기에서 도구 모음에 있는 **Sync now**(지금 동기화)를 선택합니다. 

    ![Android Studio - 앱의 build.gradle 동기화](./media/notification-hubs-kindle-get-started/gradle-sync-now.png)
14. 트리 뷰에서 Android 구조로 다시 전환합니다.  루트 매니페스트 요소에 Amazon 네임스페이스를 추가합니다.

    ```xml
    xmlns:amazon="http://schemas.amazon.com/apk/res/android"
    ```
   
    ![매니페스트의 Amazon 네임스페이스](./media/notification-hubs-kindle-get-started/amazon-namespace-manifest.png)
2. 사용 권한을 매니페스트 요소 아래에 첫 번째 요소로 추가합니다. **[YOUR PACKAGE NAME]** 을 앱을 만드는 데 사용한 패키지로 바꿉니다.

    ```xml
    <permission
        android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
        android:protectionLevel="signature" />

    <uses-permission android:name="android.permission.INTERNET"/>

    <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

    <!-- This permission allows your app access to receive push notifications
    from ADM. -->
    <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

    <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    ```
3. 애플리케이션 요소의 첫 번째 자식으로 다음 요소를 삽입합니다. **[YOUR PACKAGE NAME]** 을 앱을 만든 패키지 이름과 바꿉니다. 다음 단계에서 MyADMMessageHandler 클래스를 만듭니다. 

    ```xml
        <amazon:enable-feature
            android:name="com.amazon.device.messaging"
            android:required="true"/>
        <service
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler"
            android:exported="false" />
        <receiver
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler$Receiver"
            android:permission="com.amazon.device.messaging.permission.SEND" >

            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
                <action android:name="com.amazon.device.messaging.intent.RECEIVE" />
                <!-- Replace the name in the category tag with your app's package name. -->
                <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>
    ```

## <a name="create-your-adm-message-handler"></a>ADM 메시지 처리기 만들기

1. `com.amazon.device.messaging.ADMMessageHandlerBase`에서 상속된 프로젝트의 `com.fabrikam.mykindleapp` 패키지에 새 클래스를 추가하고 `MyADMMessageHandler`라고 이름을 지정합니다. 다음 이미지를 참조하세요.

    ![MyADMMessageHandler 클래스 만들기](./media/notification-hubs-kindle-get-started/create-adm-message-handler.png)

2. 다음 `import` 문을 파일에 `MyADMMessageHandler` 클래스에 추가합니다.

    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.support.v4.app.NotificationCompat;
    import android.util.Log;
    import com.amazon.device.messaging.ADMMessageReceiver;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    ```

3. 만든 클래스에 다음 코드를 추가합니다. `[HUB NAME]` 및 `[LISTEN CONNECTION STRING]`을 알림 허브의 이름과 수신 대기 문자열로 기억합니다. 

    ```java
    public static final int NOTIFICATION_ID = 1;
    private NotificationManager mNotificationManager;
    NotificationCompat.Builder builder;
    private static NotificationHub hub;

    public static NotificationHub getNotificationHub(Context context) {
        Log.v("com.wa.hellokindlefire", "getNotificationHub");
        if (hub == null) {
            hub = new NotificationHub("[HUB NAME]", "[HUB NAMESPACE CONNECTION STRING]", context);
        }
        return hub;
    }

    public MyADMMessageHandler() {
        super("MyADMMessageHandler");
    }

    @Override
    protected void onMessage(Intent intent) {
        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);
    }

    @Override
    protected void onRegistrationError(String s) {

    }

    @Override
    protected void onRegistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).register(s);
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
        }
    }

    @Override
    protected void onUnregistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).unregister();
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
        }
    }

    public static class Receiver extends ADMMessageReceiver
    {
        public Receiver()
        {
            super(MyADMMessageHandler.class);
        }
    }

    private void sendNotification(String msg) {
        Context ctx = getApplicationContext();

        mNotificationManager = (NotificationManager)
                ctx.getSystemService(Context.NOTIFICATION_SERVICE);

        PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);


        NotificationCompat.Builder mBuilder =
                new NotificationCompat.Builder(ctx)
                        .setSmallIcon(R.mipmap.ic_launcher)
                        .setContentTitle("Notification Hub Demo")
                        .setStyle(new NotificationCompat.BigTextStyle()
                                .bigText(msg))
                        .setContentText(msg);

        mBuilder.setContentIntent(contentIntent);
        mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
    }
    ```

## <a name="add-your-api-key-to-your-app"></a>앱에 API 키 추가
1. 다음 단계에 따라 프로젝트에 assets folder를 추가합니다. 
    1. **Project** 보기로 전환합니다. 
    2. **app**을 마우스 오른쪽 단추로 클릭합니다.
    3. **새로 만들기**를 선택합니다.
    4. **Folder**를 선택합니다. 
    5. 그런 다음, **Assets Folder**를 선택합니다. 

        ![assets folder 추가 메뉴](./media/notification-hubs-kindle-get-started/add-assets-folder-menu.png)    
    6. **Configure Component**(구성 요소 구성) 페이지에서 다음 단계를 수행합니다.
        1. **Change folder location**(폴더 위치 변경)을 선택합니다.
        2. 폴더가 `src/main/assets`으로 설정되었는지 확인합니다.
        3. **마침**을 선택합니다. 
        
            ![assets 폴더 구성](./media/notification-hubs-kindle-get-started/configure-asset-folder.png)
2. **assets** 폴더에 **api_key.txt**라는 파일을 추가합니다. 트리 뷰에서 **app**을 확장하고, **src**를 확장하고, **main**을 확장하고, **assets**을 마우스 오른쪽 단추로 클릭한 다음, **New**를 가리킨 후 **File**을 선택합니다. 파일 이름에 **api_key.txt**를 입력합니다. 3. 
5. Amazon 개발자 포털에서 생성한 API 키를 api_key.txt 파일에 복사합니다. 
6. 프로젝트를 빌드합니다. 

## <a name="run-the-app"></a>앱 실행
1. Kindle 디바이스에서 위에서 아래로 살짝 밀고 **설정**, **내 계정**을 차례로 클릭한 다음, 유효한 Amazon 계정을 등록합니다.
2. Android Studio의 Kindle 디바이스에서 앱을 실행합니다. 

> [!NOTE]
> 문제가 발생하면 에뮬레이터 또는 디바이스의 시간을 확인합니다. 시간 값이 정확해야 합니다. Kindle 에뮬레이터의 시간을 변경하려면 Android SDK 플랫폼 도구 디렉터리에서 다음 명령을 실행할 수 있습니다.

```shell
adb shell  date -s "yyyymmdd.hhmmss"
```

## <a name="send-a-notification-message"></a>알림 메시지 보내기

.NET을 사용하여 메시지를 보내려면

```csharp
static void Main(string[] args)
{
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

    hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
}
```

샘플 코드는 [GitHub에서 이 예제](https://github.com/Azure/azure-notificationhubs-dotnet/blob/master/Samples/SendPushSample/SendPushSample/Program.cs)를 참조하세요.

![][7]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 백 엔드에 등록된 모든 Kindle 디바이스로 브로드캐스트 알림을 보냈습니다. 특정 Kindle 디바이스로 알림을 푸시하는 방법을 알아보려면 다음 자습서를 계속 진행합니다.  다음 자습서에는 특정 Android 디바이스로 알림을 푸시하는 방법이 나와 있지만, 동일한 논리를 사용하여 특정 Kindle 디바이스로 알림을 푸시할 수 있습니다.

> [!div class="nextstepaction"]
>[특정 디바이스에 알림 푸시](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- URLs. -->
[Amazon developer portal]: https://developer.amazon.com/home.html
[download the SDK]: https://developer.amazon.com/public/resources/development-tools/sdk
[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
