<properties
	pageTitle="모바일 서비스 앱에 푸시 알림 추가(Xamarin.iOS) - 모바일 서비스"
	description="Azure 모바일 서비스와 함께 Xamarin.iOS 앱에서 푸시 알림을 사용하는 방법에 대해 알아봅니다."
	documentationCenter="xamarin"
	authors="ysxu"
	manager="dwrede"
	services="mobile-services"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.devlang="Java"
	ms.topic="article"
	ms.date="3/10/2015"
	ms.author="yuaxu"/>

# 모바일 서비스 앱에 푸시 알림 추가

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

이 항목에서는 Azure 모바일 서비스를 사용하여 Xamarin.iOS 8 앱에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 APNS(Apple 푸시 알림 서비스)를 사용하여 [모바일 서비스 시작] 프로젝트에 푸시 알림을 추가합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 푸시 알림을 전송합니다.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1. [인증서 서명 요청 생성]
2. [앱을 등록하고 푸시 알림을 사용하도록 설정]
3. [앱용 프로비저닝 프로필 만들기]
4. [모바일 서비스 구성]
5. [Xamarin.iOS 앱 구성]
6. [앱에 푸시 알림 추가]
7. [푸시 알림을 전송하도록 스크립트 업데이트]
8. [알림을 받기 위한 데이터 삽입]

이 자습서를 사용하려면 다음이 필요합니다.

+ iOS 8 장치(iOS 시뮬레이터에서에서 푸시 알림을 테스트할 수 없음)
+ iOS 개발자 프로그램 멤버 자격
+ [Xamarin.iOS Studio]
+ [Azure 모바일 서비스 구성 요소]

   >[AZURE.NOTE]푸시 알림 구성 요구 사항 때문에 에뮬레이터 대신 iOS 지원 장치(iPhone 또는 iPad)에서 푸시 알림을 배포 및 테스트해야 합니다.

APNS(Apple Push Notification Service)는 인증서를 사용하여 모바일 서비스를 인증합니다. 필요한 인증서를 만들어 모바일 서비스에 업로드하려면 해당 지침을 따르세요. 공식 APNS 기능 설명서는 [Apple Push Notification Service](영문)를 참조하세요.

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

모바일 서비스에서 iOS 앱으로 푸시 알림을 보내려면 Apple에 앱을 등록하고 푸시 알림을 등록해야 합니다.

1. 아직 앱을 등록하지 않은 경우 Apple 개발자 센터의 <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a>로 이동하여 Apple ID로 로그온하고 **Identifiers**와 **App IDs**를 클릭한 다음에 **+** 기호를 클릭하여 앱의 앱 ID를 만듭니다.

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

    참고: 이 자습서에서는 개발 인증서를 사용합니다. 프로덕션 인증서를 등록할 때에도 동일한 프로세스가 사용됩니다. 모바일 서비스로 인증서를 업로드할 때 동일한 인증서 유형을 설정해야 합니다.

5. **Choose File**을 클릭하고 이전에 CSR 파일을 저장한 위치로 이동하여 **Generate**를 클릭합니다.

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

나중에 이 인증서를 사용하여 .p12 파일을 생성하고 모바일 서비스에 업로드하여 APNS에서의 인증에 사용하게 됩니다.

## <a name="profile"></a>앱용 프로비저닝 프로필 만들기

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a>로 돌아가서 **Provisioning Profiles**와 **All**을 차례로 선택하고 **+** 단추를 클릭하여 새 프로필을 만듭니다. **Add iOS Provisioning Profile** 마법사가 실행됩니다.

    ![][112]

2. **Development**에서 프로비저닝 프로필 유형으로 **iOS App Development**를 선택하고 **Continue**를 클릭합니다.

3. **App ID** 드롭다운 목록에서 모바일 서비스 Quickstart 앱의 앱 ID를 선택하고 **Continue**를 클릭합니다.

    ![][113]

4. **Select certificates** 화면에서 앞서 만든 인증서를 선택하고 **Continue**를 클릭합니다.

    ![][114]

5. 테스트에 사용할 **Devices**를 선택하고 **Continue**를 클릭합니다.

    ![][115]

6. 끝으로, **Profile Name**에서 프로필의 이름을 선택하고 **Generate**와 **Done**을 차례로 클릭합니다.

    ![][116]

    새 프로비저닝 프로필이 생성됩니다.

    ![][117]

## <a name="configure-mobileServices"></a>푸시 요청을 전송하도록 모바일 서비스 구성

APNS에 앱을 등록하고 프로젝트를 구성했으면 APNS와 통합되도록 모바일 서비스를 구성해야 합니다.

1. Keychain Access에서 새 인증서를 마우스 오른쪽 단추로 클릭하여 **Export**를 클릭하고, 파일의 이름을 지정한 다음, **.p12** 형식을 선택하고, **Save**를 클릭합니다.

    ![][28]

    내보낸 인증서의 파일 이름과 위치를 적어둡니다.

2. [Azure 관리 포털]에 로그온하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

    ![][18]

3. **푸시** 탭을 클릭하고 **apple 푸시 알림 설정**에서 **업로드**를 클릭합니다.

    ![][19]

    인증서 업로드 대화 상자가 표시됩니다.

4. **파일**을 클릭하고, 내보낸 인증서 .p12 파일을 선택한 다음, **암호**를 입력하고, 올바른 **모드**가 선택되었는지 확인합니다. 그런 다음 확인 아이콘을 클릭한 후 **저장**을 클릭합니다.

    ![][20]

모바일 서비스가 이제 APNS와 작동하도록 구성됩니다.

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

1. Xamarin.Studio에서 AppDelegate.cs 파일을 열고 다음 속성을 추가합니다.

        public string DeviceToken { get; set; }

2. **TodoItem** 클래스를 열고 다음 속성을 추가합니다.

        [JsonProperty(PropertyName = "deviceToken")]
        public string DeviceToken { get; set; }

3. **QSTodoService**에서 기존 클라이언트 선언을 다음으로 재정의합니다.

        public MobileServiceClient client { get; private set; }

4. 그런 다음 **AppDelegate**가 나중에 클라이언트를 얻어 푸시 알림을 등록할 수 있도록 다음 메서드를 추가합니다.

        public MobileServiceClient GetClient {
            get{
                return client;
            }
        }

5. **AppDelegate**에서 **FinishedLaunching** 이벤트를 재정의합니다.

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

6. **AppDelegate**에서 **RegisteredForRemoteNotifications** 이벤트를 재정의합니다.

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            // Modify device token
            DeviceToken = deviceToken.Description;
            DeviceToken = DeviceToken.Trim ('<', '>').Replace (" ", "");

            // Get Mobile Services client
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            // Register for push with Mobile Services
            IEnumerable<string> tag = new List<string>() { "uniqueTag" };
            var push = client.GetPush ();
            push.RegisterNativeAsync (DeviceToken, tag);
        }

7. **AppDelegate**에서 **ReceivedRemoteNotification** 이벤트를 재정의합니다.

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            Debug.WriteLine(userInfo.ToString());
            NSObject inAppMessage;

            bool success = userInfo.TryGetValue(new NSString("inAppMessage"), out inAppMessage);

            if (success)
            {
                var alert = new UIAlertView("Got push notification", inAppMessage.ToString(), null, "OK", null);
                alert.Show();
            }
        }

8. **AppDelegeate**에 저장된 장치 토큰을 가져와서 추가될 **TodoItem**에 이 토큰을 저장하도록 **TodoListViewController**에서 **OnAdd** 동작을 수정합니다.

        string deviceToken = ((AppDelegate)UIApplication.SharedApplication.Delegate).DeviceToken;

        var newItem = new TodoItem()
        {
            Text = itemText.Text,
            Complete = false,
            DeviceToken = deviceToken
        };

이제 푸시 알림을 지원하도록 앱이 업데이트됩니다.

## <a name="update-scripts"></a>관리 포털에서 등록된 삽입 스크립트 업데이트

1. 관리 포털에서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

    ![][21]

2. **TodoItem**에서 **스크립트** 탭을 클릭하고 **삽입**을 선택합니다.

    ![][22]

    **TodoItem** 테이블에 삽입 시 호출되는 함수가 표시됩니다.

3. 삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the
            // app to be closed on the device to demonstrate toast notifications
            setTimeout(function() {
                push.apns.send("uniqueTag", {
                    alert: "Toast: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

    새 삽입 스크립트가 등록되며, 이 스크립트는 [apns 개체]를 사용하여 삽입 요청에 제공된 장치에 푸시 알림(삽입된 텍스트)을 보냅니다.

    >[AZURE.NOTE]이 스크립트는 앱을 닫고 알림 메시지를 수신할 수 있는 시간을 주기 위해 알림 전송을 지연시킵니다.

## <a name="test"></a>앱에서 푸시 알림 테스트

1. **실행** 단추를 눌러 프로젝트를 빌드하고 iOS 지원 장치에서 앱을 시작한 다음, **확인**을 클릭하여 푸시 알림을 수락합니다.

    ![][23]

    >[AZURE.NOTE]앱에서 푸시 알림을 명시적으로 수락해야 합니다. 이 요청은 앱을 처음 실행할 때만 수행됩니다.

2. 앱에서 _새 모바일 서비스 작업_과 같은 의미 있는 텍스트를 입력하고 더하기(**+**) 아이콘을 클릭합니다.

    ![][24]

3. 알림이 수신되는지 확인하고, **확인**을 클릭하여 알림을 해제합니다.

    ![][25]

4. 2단계를 반복하여 앱을 즉시 닫은 후 다음 알림이 표시되는지 확인합니다.

    ![][26]

이 자습서를 성공적으로 완료했습니다.

<!-- Anchors. -->
[인증서 서명 요청 생성]: #certificates
[앱을 등록하고 푸시 알림을 사용하도록 설정]: #register
[앱용 프로비저닝 프로필 만들기]: #profile
[모바일 서비스 구성]: #configure-mobileServices
[Xamarin.iOS 앱 구성]: #configure-app
[푸시 알림을 전송하도록 스크립트 업데이트]: #update-scripts
[앱에 푸시 알림 추가]: #add-push
[알림을 받기 위한 데이터 삽입]: #test

<!-- Images. -->

[5]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

[120]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-20.png
[121]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-21.png
[122]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-22.png

[Xamarin.iOS Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[모바일 서비스 시작]: /develop/mobile/tutorials/get-started-xamarin-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-xamarin-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Xamarin 장치 프로비저닝]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[Azure 관리 포털]: https://manage.windowsazure.com/
[apns 개체]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Azure 모바일 서비스 구성 요소]: http://components.xamarin.com/view/azure-mobile-services/
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331303
[Xamarin.iOS]: http://xamarin.com/download

<!--HONumber=54-->