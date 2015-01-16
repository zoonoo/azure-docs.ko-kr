<properties urlDisplayName="Get Started with Push (iOS)" pageTitle="푸시 알림 시작(iOS) | 모바일 개발자 센터" metaKeywords="" description="Azure 모바일 서비스를 사용하여 iOS 앱에 푸시 알림을 보내는 방법에 대해 알아봅니다." metaCanonical="http://www.windowsazure.com/ko-kr/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />


# 모바일 서비스 앱에 푸시 알림 추가

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

이 항목에서는 Azure 모바일 서비스를 사용하여 iOS 앱에 푸시 알림을 보내는 방법에 대해 설명합니다. 이 자습서에서는 APNS(Apple 푸시 알림 서비스)를 사용하여 quickstart 프로젝트에 푸시 알림을 추가합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 푸시 알림을 보냅니다.


이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1. [인증서 서명 요청 생성]
2. [앱을 등록하고 푸시 알림을 사용하도록 설정]
3. [앱용 프로비저닝 프로필 만들기]
4. [로컬로 서비스 다운로드]
5. [모바일 서비스 테스트]
6. [푸시 알림을 전송하도록 서버 업데이트](#update-server)
7. [Azure에 모바일 서비스 게시]
8. [앱에 푸시 알림 추가]
9. [푸시 알림을 전송하도록 스크립트 업데이트]
10. [로컬 테스트에 푸시 알림 사용](#local-testing)
11. [게시된 모바일 서비스에 대해 앱 테스트]

이 자습서를 사용하려면 다음이 필요합니다.

+ [모바일 서비스 iOS SDK]
+ [XCode 4.5][Xcode 설치]
+ iOS 6.0(이상) 지원 장치
+ iOS 개발자 프로그램 멤버 자격

   > [WACOM.NOTE] 푸시 알림 구성 요구 사항 때문에 에뮬레이터 대신 iOS 지원 장치(iPhone 또는 iPad)에서 푸시 알림을 배포 및 테스트해야 합니다.

이 자습서는 모바일 서비스 빠른 시작을 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작]을 완료해야 합니다.


[WACOM.INCLUDE [Apple 푸시 알림 사용](../includes/enable-apple-push-notifications.md)]


## 푸시 요청을 전송하도록 모바일 서비스 구성

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

<h2><a name="download-the-service"></a>로컬 컴퓨터에 서비스 다운로드</h2>

[WACOM.INCLUDE [mobile-services-ios-download-service-locally](../includes/mobile-services-ios-download-service-locally.md)]

<h2><a name="test-the-service"></a>모바일 서비스 테스트</h2>

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a id="update-server"></a>푸시 알림을 전송하도록 서버 업데이트

1. Visual Studio 솔루션 탐색기에서 모바일 서비스 프로젝트의 **컨트롤러** 폴더를 확장합니다. TodoItemController.cs를 엽니다. 파일 맨 위에 `using` 문을 추가합니다.


		using System;
		using System.Collections.Generic;

2. `PostTodoItem` 메서드를 다음 코드로 업데이트합니다.  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            ApplePushMessage message = new ApplePushMessage(item.Text, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    이 코드는 할 일 항목을 삽입한 후 삽입한 항목의 텍스트가 포함된 푸시 알림을 보냅니다. 오류 이벤트에서 코드는 관리 포털에서 모바일 서비스의 **로그** 탭에서 볼 수 있는 오류 로그 항목을 추가합니다.


<h2><a name="publish-the-service"></a>Azure에 모바일 서비스 게시</h2>

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## 앱에 푸시 알림 추가

1. QSAppDelegate.m에서 다음 코드 조각을 삽입하여 모바일 서비스 iOS SDK를 가져옵니다.

        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>

2. QSAppDelegate.m의 구현 내에서 다음 처리기 메서드를 교체합니다.

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3. QSAppDelegate.m의 구현 내에서 다음 처리기 메서드를 추가합니다. 모바일 서비스 URL 및 응용 프로그램 키 값을 복사하고 자리 표시자와 바꿉니다.

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {

            // TODO: update @"MobileServiceUrl" and @"AppKey" placeholders
            MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"]

            [client.push registerNativeWithDeviceToken:deviceToken tags:@[@"uniqueTag"] completion:^(NSError *error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

4. QSAppDelegate.m의 구현 내에서 다음 처리기 메서드를 추가합니다.

        // Handle any failure to register.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

5. QSAppDelegate.m의 구현 내에서 다음 처리기 메서드를 추가합니다.  

        // Because alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

   > [WACOM.NOTE] <strong>addItem</strong> 메서드를 호출하기 전에 이 코드를 추가해야 합니다.

이제 푸시 알림을 지원하도록 앱이 업데이트됩니다.

##<a id="local-testing"></a> 로컬 테스트에 푸시 알림 사용

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## 앱에서 푸시 알림 테스트

1. **실행** 단추를 눌러 프로젝트를 빌드하고 iOS 지원 장치에서 앱을 시작한 다음, **확인**을 클릭하여 푸시 알림을 수락합니다.

  	![][23]

    > [WACOM.NOTE] 앱의 푸시 알림을 명시적으로 수락해야 합니다. 이 요청은 앱을 처음 실행할 때만 발생합니다.

2. 앱에서 _새 모바일 서비스 작업_과 같은 의미 있는 텍스트를 입력하고 (**+**) 아이콘을 클릭합니다.

  	![][24]

3. 알림이 수신되는지 확인하고, **확인**을 클릭하여 알림을 해제합니다.

  	![][25]

4. 2단계를 반복하여 앱을 즉시 닫은 후 다음 푸시가 표시되는지 확인합니다.

  	![][26]

이 자습서를 성공적으로 완료했습니다.

## 다음 단계

이 자습서에서는 모바일 서비스 및 알림 허브를 사용하여 푸시 알림을 보낼 수 있도록 iOS 앱을 설정하는 작업에 대한 기본 사항을 설명했습니다. 다음으로는 태그를 사용하여 모바일 서비스에서 인증된 사용자에게만 푸시 알림을 보내는 방법을 보여 주는 다음 자습서인 [인증된 사용자에게 푸시 알림 보내기]를 완료할 수 있습니다.

<!--+ [Send push notifications to authenticated users]
	<br/>태그를 사용하는 모바일 서비스에서 인증된 사용자에게만 푸시 알림을 보내도록 하는 방법을 알아봅니다.

+ [구독자에게 브로드캐스트 알림 보내기]
	<br/>사용자가 관심 있는 범주에 대한 푸시 알림을 등록하고 수신하게 하는 방법을 알아봅니다.

+ [구독자에게 템플릿 기반 알림 보내기]
	<br/>템플릿을 사용하여 백 엔드에 플랫폼별 페이로드를 작성하지 않고도 모바일 서비스에서 푸시 알림을 보내도록 하는 방법을 알아봅니다.
-->
다음 항목에서 모바일 서비스 및 알림 허브에 대해 알아보세요.

* [데이터 작업 시작]
  <br/>모바일 서비스를 사용하여 데이터를 저장하고 쿼리하는 방법을 알아봅니다.

* [인증 시작]
  <br/>모바일 서비스를 사용하여 서로 다른 계정 유형의 앱 사용자를 인증하는 방법을 알아봅니다.

* [알림 허브 정의]
  <br/>모든 주요 클라이언트 플랫폼에 걸쳐 알림 허브가 앱에 알림을 전달하는 방법에 대해 알아봅니다.

* [알림 허브 응용 프로그램 디버깅](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>알림 허브 솔루션 문제를 해결하고 디버깅하기 위한 지침을 얻으세요. 

<!-- Anchors.  -->
[인증서 서명 요청 생성]: #certificates
[앱을 등록하고 푸시 알림을 사용하도록 설정]: #register
[앱용 프로비저닝 프로필 만들기]: #profile
[모바일 서비스 구성]: #configure
[푸시 알림을 전송하도록 스크립트 업데이트]: #update-scripts
[앱에 푸시 알림 추가]: #add-push
[알림을 받기 위한 데이터 삽입]: #test
[게시된 모바일 서비스에 대해 앱 테스트]: #test-app
[다음 단계]:#next-steps
[로컬로 서비스 다운로드]: #download-the-service-locally
[모바일 서비스 테스트]: #test-the-service
[Azure에 모바일 서비스 게시]: #publish-mobile-service

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
[Xcode 설치]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[모바일 서비스 iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple 푸시 알림 서비스]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started
[Azure 관리 포털]: https://manage.windowsazure.com/
[apns 개체]: http://go.microsoft.com/fwlink/p/?LinkId=272333

[데이터 작업 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[인증 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users

[인증된 사용자에게 푸시 알림 보내기]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/

[알림 허브 정의]: /ko-kr/documentation/articles/notification-hubs-overview/
[구독자에게 브로드캐스트 알림 보내기]: /ko-kr/documentation/articles/notification-hubs-ios-send-breaking-news/
[구독자에게 템플릿 기반 알림 보내기]: /ko-kr/documentation/articles/notification-hubs-ios-send-localized-breaking-news/
