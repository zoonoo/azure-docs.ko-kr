<properties 
	pageTitle="Azure 앱 서비스를 사용하여 Xamarin iOS 앱에 푸시 알림 추가" 
	description="Azure 앱 서비스를 사용하여 Xamarin iOS 앱에 푸시 알림을 전송하는 방법을 알아봅니다." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="ysxu"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="06/18/2015" 
	ms.author="yuaxu"/>

# Xamarin iOS 앱에 푸시 알림 추가

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../../includes/app-service-mobile-selector-get-started-push-preview.md)]

이 항목에서는 Azure 앱 서비스를 사용하여 Xamarin iOS 8 앱에 푸시 알림을 전송하는 방법을 보여 줍니다. 이 자습서에서는 APNS(Apple Push Notification Service)를 사용하여 [앱 서비스 모바일 앱 시작] 프로젝트에 푸시 알림을 추가합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 백 엔드에서 푸시 알림을 전송합니다.

이 자습서를 사용하려면 다음이 필요합니다.

+ iOS 8 장치
+ iOS 개발자 프로그램 멤버 자격
+ [Xamarin.iOS Studio]
+ [Azure 모바일 서비스 구성 요소]

   >[AZURE.NOTE]푸시 알림 구성 요구 사항 때문에 에뮬레이터 대신 iOS 지원 장치(iPhone 또는 iPad)에서 푸시 알림을 배포 및 테스트해야 합니다.

APNS(Apple Push Notification Service)는 인증서를 사용하여 모바일 앱을 인증합니다. 필요한 인증서를 만들어 모바일 앱에 업로드하려면 해당 지침을 따르세요. 공식 APNS 기능 설명서는 [Apple Push Notification Service](영문)를 참조하세요.

##<a name="review"></a>서버 프로젝트 구성 검토(옵션)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-push-preview](../../includes/app-service-mobile-dotnet-backend-enable-push-preview.md)]

## <a name="certificates"></a>인증서 서명 요청 파일 생성

먼저, 서명된 인증서를 생성하기 위해 Apple에서 사용하는 CSR(인증서 서명 요청) 파일을 생성해야 합니다.

1. 유틸리티에서 **Keychain Access 도구**를 실행합니다.

2. **Keychain Access**를 클릭하고 **Certificate Assistant**를 확장한 다음 **Request a Certificate from a Certificate Authority...**를 클릭합니다.

    ![][5]

3. **User Email Address**를 입력하고 **Common Name** 값을 입력한 다음 **Saved to disk**가 선택되었는지 확인하고 **Continue**를 클릭합니다.

    ![][6]

4. **Save As**에 CSR(인증서 서명 요청) 파일의 이름을 입력하고, **Where**에서 위치를 선택한 후 **Save**를 클릭합니다.

    ![][7]
  
    선택한 위치를 기억해 두세요.

이제 Apple에 앱을 등록하고, 푸시 알림을 사용하도록 설정하고, 내보낸 CSR을 업로드하여 푸시 인증서를 만들 것입니다.

## <a name="register"></a>푸시 알림에 대해 앱 등록

모바일 앱에서 iOS 장치로 푸시 알림을 전송하려면 Apple에 응용 프로그램을 등록하고 푸시 알림을 등록해야 합니다.

1. 아직 앱을 등록하지 않은 경우 Apple Developer Center의 iOS Provisioning Portal</a>로 이동하여 Apple ID로 로그온하고 **Identifiers**와 **App IDs**를 클릭한 다음에 **+** 기호를 클릭하여 앱의 앱 ID를 만듭니다.
    
    ![][102]

2. **Description**에 앱의 이름을 입력하고 고유한 **Bundle Identifier**를 입력한 후 기억해 둔 다음 "App Services" 섹션에서 "Push Notifications" 옵션을 선택하고 **Continue**를 클릭합니다. 이 예제에서는 **MobileServices.Quickstart** ID를 사용하지만 실제로 이 ID를 사용해서는 안 됩니다. 모든 사용자는 고유한 앱 ID를 사용해야 하기 때문입니다. 사용자의 전체 이름 또는 이니셜을 앱 이름 뒤에 추가하는 것이 좋습니다.

    ![][103]
   
    앱 ID가 생성되고 정보를 **제출**하도록 요청됩니다. **Submit**를 클릭합니다.
   
    ![][104]
   
    **Submit**를 클릭하면 아래와 같은 **Registration complete** 화면이 표시됩니다. **Done**을 클릭합니다.
   
    ![][105]

3. 방금 만든 앱 ID를 찾아 해당 행을 클릭합니다.

    ![][106]
   
    앱 ID를 클릭하면 앱 및 앱 ID에 대한 세부 정보가 표시됩니다. **Settings** 단추를 클릭합니다.
   
    ![][107]
   
4. 화면 아래로 스크롤하여 **Development Push SSL Certificate** 섹션에서 **Create Certificate...** 단추를 클릭합니다.

    ![][108]

    "Add iOS Certificate" assistant가 표시됩니다.
   
    참고: 이 자습서에서는 개발 인증서를 사용합니다. 프로덕션 인증서를 등록할 때에도 동일한 프로세스가 사용됩니다. 모바일 앱에 인증서를 업로드할 때 동일한 인증서 유형을 설정해야 합니다.

5. **Choose File**을 클릭하고, 이전에 CSR 파일을 저장한 위치로 이동하고, **Generate**를 클릭합니다.

    ![][110]
  
6. 포털에서 인증서가 생성되면 **Download** 단추를 클릭하고 **Done**을 클릭합니다.
 
    ![][111]

    서명 인증서가 다운로드되어 컴퓨터의 다운로드 폴더에 저장됩니다.

    ![][9]

    참고: 기본적으로 다운로드된 개발 증명서 파일은 이름이 <strong>aps_development.cer</strong>로 지정됩니다.

7. 다운로드한 푸시 인증서 **aps_development.cer**을 두 번 클릭합니다.

    아래와 같이 새 인증서가 Keychain에 설치됩니다.

    ![][10]

    인증서의 이름은 다를 수 있지만 <strong>Apple Development iOS Push Notification Services:</strong>가 앞에 옵니다.

나중에 이 인증서를 사용하여 .p12 파일을 생성하고 모바일 앱에 업로드하여 APNS에서 인증할 수 있게 합니다.

## <a name="profile"></a>앱용 프로비저닝 프로필 만들기
 
1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a>로 돌아가서 **Provisioning Profiles**와 **All**을 차례로 선택하고 **+** 단추를 클릭하여 새 프로필을 만듭니다. **Add iOS Provisioning Profile** 마법사가 실행됩니다.

    ![][112]

2. **Development**에서 프로비저닝 프로필 유형으로 **iOS App Development**를 선택하고 **Continue**를 클릭합니다.

3. **App ID** 드롭다운 목록에서 모바일 앱 퀵 스타트 앱의 앱 ID를 선택하고 **Continue**를 클릭합니다.

    ![][113]

4. **Select certificates** 화면에서 앞서 만든 인증서를 선택하고 **Continue**를 클릭합니다.

    ![][114]

5. 테스트에 사용할 **Devices**를 선택하고 **Continue**를 클릭합니다.
  
    ![][115]

6. 끝으로, **Profile Name**에서 프로필의 이름을 선택하고 **Generate**와 **Done**을 차례로 클릭합니다.

    ![][116]

    새 프로비저닝 프로필이 생성됩니다.

    ![][117]

## <a name="configure-appServiceMobile"></a>푸시 요청을 전송하도록 앱 서비스 모바일 백 엔드 구성

[AZURE.INCLUDE [app-service-mobile-apns-configure-push-preview](../../includes/app-service-mobile-apns-configure-push-preview.md)]

##<a id="update-server"></a>푸시 알림을 전송하도록 서버 업데이트

1. Visual Studio에서 솔루션을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.

2. **Microsoft.Azure.NotificationHubs**를 검색하고 솔루션에 포함된 모든 프로젝트에 대해 **설치**를 클릭합니다.

3. Visual Studio 솔루션 탐색기에서 모바일 백 엔드 프로젝트의 **컨트롤러** 폴더를 확장합니다. TodoItemController.cs를 엽니다. 파일 맨 위에 다음 `using` 문을 추가합니다.

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;

4. `PostTodoItem` 메서드의 **InsertAsync** 호출 뒤에 다음 조각을 추가합니다.

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // iOS payload
        var appleNotificationPayload = "{"aps":{"alert":"" + item.Text + ""}}";

        await Hub.Push.SendAppleNativeNotificationAsync(appleNotificationPayload);

    이 코드는 이 모바일 앱과 연결된 알림 허브에 todo 항목 삽입 후 푸시 알림을 전송하도록 지시합니다.


## <a name="publish-the-service"></a>Azure에 모바일 백 엔드 게시

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## <a name="configure-app"></a>Xamarin.iOS 응용 프로그램 구성

1. Xamarin.Studio에서 **Info.plist**를 열고 앞에서 만든 ID를 사용하여 **Bundle Identifier**를 업데이트합니다.

    ![][121]

2. 아래의 **Background Modes**로 스크롤하여 **Enable Background Modes** 상자와 **Remote notifications** 상자를 선택합니다.

    ![][122]

3. Solution Panel에서 프로젝트를 두 번 클릭하여 **Project Options**를 엽니다.

4.  **Build**에서 **iOS Bundle Signing**을 선택하고 방금 이 프로젝트에 대해 설정한 해당 **ID** 및 **프로비저닝 프로필**을 선택합니다.

    ![][120]

    이제 Xamarin 프로젝트에서 코드 서명에 새 프로필을 사용하게 됩니다. 공식 Xamarin 장치 프로비저닝 설명서를 보려면 [Xamarin 장치 프로비저닝]을 참조하세요.

## <a name="add-push"></a>앱에 푸시 알림 추가

1. **QSTodoService**에서 **AppDelegate**가 모바일 클라이언트를 얻을 수 있도록 기존 클라이언트 선언을 재정의합니다.
        
        public MobileServiceClient client { get; private set; }

2. **AppDelegate**에서 **FinishedLaunching** 이벤트를 재정의합니다.

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert 
                | UIUserNotificationType.Badge 
                | UIUserNotificationType.Sound, 
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

3. 동일한 파일에서 **RegisteredForRemoteNotifications** 이벤트를 재정의합니다.

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken);
        }

4. 그런 다음 **DidReceivedRemoteNotification** 이벤트를 재정의합니다.

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

이제 푸시 알림을 지원하도록 앱이 업데이트됩니다.


## <a name="publish-the-service"></a>Azure에 모바일 백 엔드 게시

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## <a name="test"></a>앱에서 푸시 알림 테스트

1. **실행** 단추를 눌러 프로젝트를 빌드하고 iOS 지원 장치에서 앱을 시작한 다음, **확인**을 클릭하여 푸시 알림을 수락합니다.
	
	> [AZURE.NOTE]앱에서 푸시 알림을 명시적으로 수락해야 합니다. 이 요청은 앱이 처음 실행될 때만 발생합니다.

2. 앱에서 작업을 입력하고 더하기(**+**) 아이콘을 클릭합니다.

3. 알림이 수신되는지 확인하고, **확인**을 클릭하여 알림을 해제합니다.

4. 2단계를 반복하여 앱을 즉시 닫은 후 알림이 표시되는지 확인합니다.

이 자습서를 성공적으로 완료했습니다.

<!-- Images. -->

[24]: ./media/mobile-services-ios-get-started-push/mobile-services-quickstart-push2-ios.png
[앱 서비스 모바일 앱 시작]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md

[5]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step5.png
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step6.png
[7]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step7.png

[9]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step9.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[25]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-02.png
[103]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-03.png
[104]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-04.png
[105]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-05.png
[106]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-06.png
[107]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-07.png
[108]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-08.png

[110]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-10.png
[111]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-11.png
[112]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-12.png
[113]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-13.png
[114]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-14.png
[115]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-15.png
[116]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-16.png
[117]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-17.png

[120]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-20.png
[121]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-21.png
[122]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-22.png

[Xamarin.iOS Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: /ko-kr/develop/mobile/tutorials/get-started-xamarin-ios
[Get started with data]: /ko-kr/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with authentication]: /ko-kr/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Get started with push notifications]: /ko-kr/develop/mobile/tutorials/get-started-with-push-xamarin-ios
[Push notifications to app users]: /ko-kr/develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /ko-kr/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Xamarin 장치 프로비저닝]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Azure 모바일 서비스 구성 요소]: http://components.xamarin.com/view/azure-mobile-services/
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331303

 

<!-----HONumber=July15_HO5-->