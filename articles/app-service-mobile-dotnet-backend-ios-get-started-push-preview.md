<properties 
	pageTitle="Azure 앱 서비스를 사용하여 iOS 앱에 푸시 알림 추가" 
	description="Azure 앱 서비스를 사용하여 iOS 앱에 푸시 알림을 전송하는 방법을 알아봅니다." 
	services="app-service\mobile" 
	documentationCenter="ios" 
	manager="dwrede"
	editor="" 
	authors="ysxu"/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article"
	ms.date="02/19/2015" 
	ms.author="yuaxu"/>


# iOS 앱에 푸시 알림 추가

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../includes/app-service-mobile-selector-get-started-push-preview.md)]

이 항목에서는 Azure 앱 서비스를 사용하여 APNS(Apple Push Notification Service)를 통해 iOS 앱에 푸시 알림을 전송하는 방법을 보여 줍니다. 완료하면 레코드가 삽입될 때마다 .NET 백 엔드가 QuickStart ToDo 앱에 푸시 알림을 보냅니다. 앱은 iOS 8 및 이전 버전과 호환됩니다.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1. [iOS 인증서 서명 요청 생성]
2. [앱을 등록하고 푸시 알림을 사용하도록 설정]
3. [앱용 프로비저닝 프로필 만들기]
4. [푸시 요청을 전송하도록 모바일 백 엔드 구성]
5. [푸시 알림을 전송하도록 서버 업데이트](#update-server)
6. [Azure에 모바일 백 엔드 게시]
7. [앱에 푸시 알림 추가]
8. [앱 테스트]

이 자습서를 사용하려면 다음이 필요합니다.

+ [Azure 모바일 앱 iOS SDK]
+ [Azure 알림 허브 NuGet]
+ [XCode 6.0][Install Xcode]
+ iOS 6.0(이상) 지원 장치
+ iOS 개발자 프로그램 멤버 자격

   >[AZURE.NOTE]푸시 알림 구성 요구 사항 때문에 에뮬레이터 대신 iOS 지원 장치(iPhone 또는 iPad)에서 푸시 알림을 배포 및 테스트해야 합니다.

이 자습서는 앱 서비스 모바일 앱 퀵 스타트를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [앱 서비스 모바일 앱 시작]을 완료해야 합니다.

[AZURE.INCLUDE [Apple 푸시 알림 사용](../includes/enable-apple-push-notifications.md)]

## 푸시 요청을 전송하도록 모바일 앱 구성

[AZURE.INCLUDE [app-service-mobile-apns-configure-push-preview](../includes/app-service-mobile-apns-configure-push-preview.md)]

## <a id="update-server"></a>푸시 알림을 전송하도록 서버 업데이트

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

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## 앱에 푸시 알림 추가
1. 앱 서비스 모바일 앱 클라이언트 SDK를 다운로드하고 xcode에서 해당 참조를 추가합니다.

2. **QSAppDelegate.m**의 **application:didFinishLaunchingWithOptions**에서 Apple Push Notification Service에 클라이언트를 등록하는 다음 코드를 추가합니다.

        // register iOS8 or previous devices for notifications
        if ([[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && 	
        	[[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)]) {
            [[UIApplication sharedApplication] registerForRemoteNotifications];
        } else {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
        }

3. 동일한 파일의 **QSAppDelegate** 구현 내에서 다음 처리기 메서드를 추가합니다.

        // registration with APNs is successful
        - (void)application:(UIApplication *)application 
            didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {

            // make sure you have imported "QSTodoService.h"
            QSTodoService *todoService = [QSTodoService defaultService];
            MSClient *client = todoService.client;

            [client.push registerDeviceToken:deviceToken completion:^(NSError *error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

4. 그런 다음 구현 내에서 오류 처리기 메서드를 추가합니다.

        // handle any failure to register
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

5. QSAppDelegate.m의 구현 내에서 다음 처리기 메서드를 추가합니다.

        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application 
              didReceiveRemoteNotification:(NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            
            NSDictionary *apsPayload = userInfo[@"aps"];
            NSString *alertString = apsPayload[@"alert"];
    
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" 
                                                            message:alertString 
                                                           delegate:nil 
                                                  cancelButtonTitle:@"OK" 
                                                  otherButtonTitles:nil];
            [alert show];
        }

이제 푸시 알림을 지원하도록 앱이 업데이트됩니다.

## 앱에서 푸시 알림 테스트

1. **실행** 단추를 눌러 프로젝트를 빌드하고 iOS 지원 장치에서 앱을 시작한 다음, **확인**을 클릭하여 푸시 알림을 수락합니다.

  	![][23]

    > [AZURE.NOTE]앱에서 푸시 알림을 명시적으로 수락해야 합니다. This request only occurs the first time that the app runs.

2. 앱에서 _새 모바일 서비스 작업_과 같은 의미 있는 텍스트를 입력하고 (**+**) 아이콘을 클릭합니다.

  	![][24]

3. 알림이 수신되는지 확인하고, **확인**을 클릭하여 알림을 해제합니다.

  	![][25]

4. 2단계를 반복하여 앱을 즉시 닫은 후 다음 푸시가 표시되는지 확인합니다.

  	![][26]

이 자습서를 성공적으로 완료했습니다.

<!-- Anchors.  -->
[iOS 인증서 서명 요청 생성]: #certificates
[앱을 등록하고 푸시 알림을 사용하도록 설정]: #register
[앱용 프로비저닝 프로필 만들기]: #profile
[앱에 푸시 알림 추가]: #add-push
[푸시 요청을 전송하도록 모바일 백 엔드 구성]: #configure
[Update the server to send push notifications]: #update-server
[Azure에 모바일 백 엔드 게시]: #publish-mobile-service
[앱 테스트]: #test-the-service

<!-- Images. -->
[5]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png
[17]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Azure 모바일 앱 iOS SDK]: https://go.microsoft.com/fwLink/?LinkID=529823
[Azure 알림 허브 NuGet]: https://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: mobile-services-dotnet-backend-ios-get-started.md
[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!--HONumber=54-->