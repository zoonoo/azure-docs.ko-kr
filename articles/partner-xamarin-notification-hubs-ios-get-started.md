<properties
	pageTitle="Xamarin iOS 앱에 대한 알림 허브 시작"
	description="Azure 알림 허브를 사용하여 Xamarin iOS 응용 프로그램에 푸시 알림을 보내는 방법에 대해 알아봅니다."
	services="notification-hubs"
	documentationCenter="xamarin"
	authors="yuaxu"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="11/11/2014"
	ms.author="donnam"/>

# 알림 허브 시작

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

##개요

이 항목에서는 Azure 알림 허브를 사용하여 iOS 응용 프로그램에 푸시 알림을 보내는 방법을 보여 줍니다.
이 자습서에서는 APNS(Apple 푸시 알림 서비스)를 사용하여 푸시 알림을 받는 빈 Xamarin.iOS 앱을 만듭니다. 완료하면 알림 허브를 사용하여 앱을 실행하는 모든 장치로 푸시 알림을 브로드캐스트할 수 있습니다. 완성된 코드는 [NotificationHubs 앱][GitHub] 샘플에서 확인할 수 있습니다.

이 자습서에서는 알림 허브를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다. 

##필수 조건

이 자습서를 사용하려면 다음 필수 조건이 필요합니다.

+ [XCode 6.0][Xcode 설치]
+ iOS 7.0(이상) 지원 장치
+ iOS 개발자 프로그램 멤버 자격
+ [Xamarin.iOS]
+ [Azure 모바일 서비스 구성 요소]

   > [AZURE.NOTE] 푸시 알림 구성 요구 사항 때문에 시뮬레이터 대신 iOS 지원 장치(iPhone 또는 iPad)에서 푸시 알림을 배포 및 테스트해야 합니다.

이 자습서를 완료해야 다른 모든 Xamarin.iOS 앱용 알림 허브 자습서를 진행할 수 있습니다.

> [AZURE.IMPORTANT] 이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-kr%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started)을 참조하세요.

APNS(Apple 푸시 알림 서비스)는 인증서를 사용하여 모바일 서비스를 인증합니다. 필요한 인증서를 만들어 모바일 서비스에 업로드하려면 해당 지침을 따르세요. 공식 APNS 기능 설명서는 [Apple 푸시 알림 서비스]를 참조하세요.


<h2><a name="certificates"></a>인증서 서명 요청 파일 생성</h2>

먼저, 서명된 인증서를 생성하기 위해 Apple에서 사용하는 CSR(인증서 서명 요청) 파일을 생성해야 합니다.

1. Utilities 폴더에서 Keychain Access 도구를 실행합니다.

2. **Keychain Access**를 클릭하고 **Certificate Assistant**를 확장한 다음 **Request a Certificate from a Certificate Authority...**를 클릭합니다.

  	![][5]

3. **사용자 전자 메일 주소**를 선택하고 **일반 이름** 및 **CA 전자 메일 주소** 값을 입력한 후에 **디스크에 저장**이 선택되어 있는지 확인하고 **계속**을 클릭합니다.

  	![][6]

4. **다른 이름으로 저장**에 CSR(인증서 서명 요청) 파일의 이름을 입력하고 **위치**에서 위치를 선택한 후 **저장**을 클릭합니다.

  	![][7]

  	CSR 파일이 선택한 위치에 저장됩니다. 기본 위치는 바탕 화면입니다. 이 파일을 저장한 위치를 기억해두세요.

이제 Apple에 앱을 등록하고, 푸시 알림을 사용하도록 설정하고, 내보낸 CSR을 업로드하여 푸시 인증서를 만드세요.

<h2><a name="register"></a>푸시 알림에 대해 앱 등록</h2>

모바일 서비스에서 iOS 앱으로 푸시 알림을 보내려면 Apple에 앱을 등록하고 푸시 알림도 등록해야 합니다.  

1. 아직 앱을 등록하지 않은 경우 Apple Developer Center의 <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS 프로비전 포털</a>로 이동하여 Apple ID로 로그온하고 **식별자**와 **앱 ID**를 클릭한 다음 마지막으로 **+** 기호를 클릭하여 새 앱을 등록합니다.

	![][105]

2. **설명**에 앱 이름을 입력하고 **번들 식별자** 값을 입력한 후 "앱 서비스" 섹션에서 "푸시 알림" 옵션을 선택하고 **계속**을 클릭합니다.

	![][106]

	![][107]

	![][108]


	앱 ID가 생성되고 정보를 제출하도록 요청됩니다. **제출**을 클릭합니다.

	![][109]

	**Submit**를 클릭하면 아래와 같은 **Registration complete** 화면이 표시됩니다. **완료**를 클릭합니다.

	![][110]

	>[AZURE.NOTE] **MobileServices.Quickstart** 외의 **번들 식별자** 값을 입력하는 경우 Xcode 프로젝트에서도 번들 식별자 값을 업데이트해야 합니다.

3. 방금 만든 앱 ID를 찾아 해당 행을 클릭합니다.

	![][111]

	앱 ID를 클릭하면 앱에 대한 세부 정보 및 앱 ID가 표시됩니다.

	![][112]

	![][113]

4. **편집**을 클릭하고 화면 맨 아래로 스크롤한 후 **개발 푸시 SSL 인증서** 섹션 아래의 **인증서 만들기...**를 클릭합니다.

	![][114]

	그러면 "iOS 인증서 추가" 도우미가 표시됩니다.

	![][115]

	>[AZURE.NOTE] 이 자습서에서는 개발 인증서를 사용합니다. 프로덕션 인증서를 등록할 때에도 동일한 프로세스가 사용됩니다. 모바일 서비스로 인증서를 업로드할 때 동일한 인증서 유형을 설정해야 합니다.

5. **파일 선택**을 클릭하고 첫 번째 작업에서 만든 CSR 파일이 저장된 위치로 이동하여 **생성**을 클릭합니다.

  	![][116]

6. 포털에서 인증서가 생성되면 **다운로드**를 클릭한 후 **완료**를 클릭합니다.

  	![][118]

  	![][119]  

   	그러면 서명 인증서가 다운로드되어 컴퓨터의 **Downloads** 폴더에 저장됩니다.

  	![][9]

    > [AZURE.NOTE] 기본적으로 다운로드된 개발 증명서 파일은 이름이 **aps_development.cer**로 지정됩니다.

7. 다운로드한 푸시 인증서 **aps_development.cer**을 두 번 클릭합니다.

	아래와 같이 새 인증서가 Keychain에 설치됩니다.

   	![][10]

	> [AZURE.NOTE]
	> 인증서의 이름은 다를 수 있지만 <strong>Apple Development iOS Push Notification Services:</strong>가 앞에 옵니다.

	나중에 이 인증서를 사용하여 .p12 파일을 생성하고 알림 허브에 업로드하여 APNS를 통해 푸시 알림을 사용할 수 있게 합니다.

<h2><a name="profile"></a>앱용 프로비저닝 프로필 만들기</h2>

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS 프로비전닝 포털</a>로 돌아가서 **프로비저닝 프로필**, **모두**를 차례로 선택한 후에 **+** 단추를 클릭하여 새 프로필을 만듭니다. 그러면 **iOS 프로비저닝 프로필 추가** 마법사가 표시됩니다.

   	![][120]

2. **개발**에서 프로비저닝 프로필 유형으로 **iOS 앱 개발**을 선택하고 **계속**을 클릭합니다.

   	![][121]

3. **앱 ID** 드롭다운 목록에서 모바일 서비스 Quickstart 앱의 앱 ID를 선택하고 **계속**을 클릭합니다.

   	![][122]

4. **인증서 선택** 화면에서 앞에서 만든 인증서를 선택하고 **계속**을 클릭합니다.

   	![][123]

5. 테스트에 사용할 **Devices**를 선택하고 **Continue**를 클릭합니다.

   	![][124]

6. 마지막으로 **프로필 이름**에서 프로필의 이름을 선택하고 **생성**, **완료**를 차례로 클릭합니다.

   	![][125]

   	![][126]

  	새 프로비저닝 프로필이 생성됩니다.

7. Xcode에서 구성 도우미를 열고 왼쪽 창의 **라이브러리** 섹션에서 **프로비저닝 프로필**을 선택한 다음 방금 만든 프로비저닝 프로필을 가져옵니다.

8. 왼쪽에서 장치를 선택하고 프로비저닝 프로필을 다시 가져옵니다.

9. 키 집합 액세스에서 새 인증서를 마우스 오른쪽 단추로 클릭하고 **내보내기**를 클릭한 다음 인증서의 이름을 입력하고 **.p12** 형식을 선택한 후에 **저장**을 클릭합니다.

   	![][18]

  	내보낸 인증서의 파일 이름과 위치를 적어둡니다.

이제 Xcode 프로젝트에서 코드 서명에 새 프로필을 사용하게 됩니다. 다음에는 인증서를 알림 허브에 업로드해야 합니다.

<h2><a name="configure-hub"></a>알림 허브 구성</h2>

1. [Azure 관리 포털]에 로그온하여 화면 아래쪽의 **+새로 만들기**를 클릭합니다.

2. **앱 서비스**, **서비스 버스**, **알림 허브**, **빨리 만들기**를 차례로 클릭합니다.

   	![][27]

3. 알림 허브의 이름을 입력하고 원하는 지역을 선택한 후 **새 알림 허브 만들기**를 클릭합니다.

   	![][28]

4. 방금 만든 네임스페이스(일반적으로 ***알림 허브 이름*-ns**)를 클릭한 후 맨 위에 있는 **구성** 탭을 클릭합니다.

   	![][29]

5. 위쪽의 **알림 허브** 탭을 클릭한 후 방금 만든 알림 허브를 클릭합니다.

   	![][210]

6. 위쪽의 **구성** 탭을 선택하고 Apple 알림 설정에 대해 **업로드**를 클릭합니다. 그런 다음 앞에서 내보낸 **.p12** 인증서 및 인증서의 암호를 선택합니다. **프로덕션**(스토어에서 앱을 구매한 사용자에게 푸시 알림을 보내려는 경우에 사용) 또는 **샌드박스**(개발 시에 사용) 중 사용하려는 푸시 서비스를 선택합니다.

   	![][211]

7. 위쪽의 **대시보드** 탭을 클릭하고 **연결 정보**를 클릭합니다. 두 연결 문자열을 기록해 둡니다.

   	![][212]

이제 알림 허브가 APN과 작동하도록 구성되었으며 앱을 등록하고 알림을 보내기 위한 연결 문자열이 있습니다.

<h2><a name="connecting-app"></a>알림 허브에 앱 연결</h2>

### 새 프로젝트 만들기

1. Xamarin Studio에서 새 iOS 프로젝트를 만들고 **통합 API >단일 보기 응용 프로그램** 템플릿을 선택합니다.

   	![][31]

2. 먼저 Azure 메시징 구성 요소에 대한 참조를 추가합니다. 솔루션 보기에서 프로젝트에 대한 **구성 요소** 폴더를 마우스 오른쪽 단추로 클릭하고 **다른 구성 요소 가져오기**를 선택합니다. **Azure 메시징** 구성 요소를 검색하여 프로젝트에 추가합니다.

3. **AppDelegate.cs**에서 다음 using 문을 추가합니다.

    using WindowsAzure.Messaging;

4. **SBNotificationHub** 인스턴스를 선언합니다.

		private SBNotificationHub Hub { get; set; }

5. 다음 변수를 사용하여 **Constants.cs** 클래스를 만듭니다.

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";


6. **AppDelegate.cs**에서 **FinishedLaunching()**을 다음과 일치하도록 업데이트합니다.

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert |
                UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes);

            return true;
        }

7. **AppDelegate.cs**의 **RegisteredForRemoteNotifications()** 메서드를 재정의합니다.

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);

            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }

                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }

8. **AppDelegate.cs**의 **ReceivedRemoteNotification()** 메서드를 재정의합니다.

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

9. **AppDelegate.cs**에 다음 **ProcessNotification()** 메서드를 만듭니다.

        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

                string alert = string.Empty;

                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  " this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the json gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();

                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }

    > [AZURE.NOTE] 네트워크에 연결되지 않는 경우와 같은 상황을 처리하도록 **FailedToRegisterForRemoteNotifications()**를 재정의할 수 있습니다.


10. 장치에서 앱을 실행합니다.

<h2><a name="send"></a>백 엔드에서 알림 보내기</h2>

<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST 인터페이스</a>를 사용하는 모든 백 엔드에서 알림 허브를 사용하여 알림을 보낼 수 있습니다. 이 자습서에서는 .NET 콘솔 앱 및 노드 스크립트를 사용하는 모바일 서비스를 통해 알림을 보냅니다.

.NET 앱을 사용하여 알림을 보내려면

1. 새 Visual C# 콘솔 응용 프로그램을 만듭니다.

   	![][213]

2. <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet 패키지</a>를 사용하여 Azure 서비스 버스 SDK에 대한 참조를 추가합니다. Visual Studio 주 메뉴에서 **도구**, **라이브러리 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다. 그런 다음 콘솔 창에서 다음을 입력하고

        Install-Package WindowsAzure.ServiceBus and press Enter.

2. Program.cs 파일을 열고 다음 using 문을 추가합니다.

        using Microsoft.ServiceBus.Notifications;

3. 다음 메서드를  `Program`  클래스에 추가합니다.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. 그런 다음  `Main` 메서드에 다음 줄을 추가합니다.

         SendNotificationAsync();
		 Console.ReadLine();

5. F5 키를 눌러 앱을 실행합니다. 장치에 대한 경고를 받게 됩니다. Wi-Fi를 사용하는 경우 연결에 문제가 없는지 확인하십시오.

Apple [로컬 및 푸시 알림 프로그래밍 가이드]에서 가능한 모든 페이로드를 찾아볼 수 있습니다.

모바일 서비스를 사용하여 알림을 보내려면 [모바일 서비스 시작]의 지침을 따른 후 다음 단계를 수행합니다.

1. [Azure 관리 포털]에 로그온한 다음 모바일 서비스를 선택합니다.

2. 위쪽의 **스케줄러** 탭을 선택합니다.

   	![][215]

3. 새 예약된 작업을 만들고 이름을 입력한 후 **요청 시**를 선택합니다.

   	![][216]

4. 작업이 만들어졌으면 작업 이름을 클릭합니다. 그런 다음 위쪽 막대에서 **스크립트** 탭을 클릭합니다.

5. 스케줄러 함수 내에 다음 스크립트를 삽입합니다. 자리 표시자를 알림 허브 이름과 앞에서 얻은  *DefaultFullSharedAccessSignature*의 연결 문자열로 바꿔야 합니다. **저장**을 클릭합니다.

		var azure = require('azure');
		var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
		notificationHubService.apns.send(
	    	null,
    		{"aps":
        		{
          		"alert": "Hello from Mobile Services!"
        		}
    		},
    		function (error)
    		{
	        	if (!error) {
    	        	console.warn("Notification successful");
        		}
    		}
		);


6. 아래쪽 막대에서 **한 번 실행**을 클릭합니다. 장치에 대한 경고를 받게 됩니다.

## <a name="next-steps"> </a>다음 단계

이 간단한 예제에서는 모든 iOS 장치로 알림을 브로드캐스트합니다. 특정 사용자에게만 알림을 보내려면 [알림 허브를 사용하여 사용자에게 푸시 알림 보내기] 자습서를 참조하고, 사용자를 관심 그룹별로 분할하려면 [알림 허브를 통해 속보 보내기]를 참조하세요. 알림 허브 사용 방법에 대한 자세한 내용은 [알림 허브 지침] 및 [iOS용 알림 허브 사용 방법]을 참조하세요.

<!-- Anchors. -->
[인증서 서명 요청 생성]: #certificates
[앱을 등록하고 푸시 알림을 사용하도록 설정]: #register
[앱용 프로비저닝 프로필 만들기]: #profile
[알림 허브 구성]: #configure-hub
[알림 허브에 앱 연결]: #connecting-app
[백 엔드에서 알림 보내기]: #send
[다음 단계]:#next-steps

<!-- Images. -->
[5]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step10.png
[18]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
[105]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-08.png
[109]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-09.png
[110]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-16.png

[118]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-18.png
[119]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-19.png

[120]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-20.png
[121]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-21.png
[122]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-22.png
[123]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-23.png
[124]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-24.png
[125]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-25.png
[126]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-26.png

[27]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal.png
[28]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
[29]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal.png
[210]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
[211]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-configure-ios.png
[212]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-connection-strings.png


[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png



[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[모바일 서비스 iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[앱 제출 페이지]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[내 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows용 Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[모바일 서비스 시작]: /develop/mobile/tutorials/get-started-xamarin-ios
[Azure 관리 포털]: https://manage.windowsazure.com/
[알림 허브 지침]: http://msdn.microsoft.com/library/jj927170.aspx
[iOS용 알림 허브 사용 방법]: http://msdn.microsoft.com/library/jj927168.aspx
[Xcode 설치]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS 프로비전 포털]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[알림 허브를 사용하여 사용자에게 푸시 알림 보내기]: /manage/services/notification-hubs/notify-users-aspnet
[알림 허브를 사용하여 속보 보내기]: /manage/services/notification-hubs/breaking-news-dotnet

[로컬 및 푸시 알림 프로그래밍 가이드]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Apple 푸시 알림 서비스]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure 모바일 서비스 구성 요소]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.iOS]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS

<!--HONumber=49-->