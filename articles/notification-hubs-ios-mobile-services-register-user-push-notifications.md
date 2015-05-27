<properties 
	pageTitle="모바일 서비스를 사용하여 푸시 알림에 현재 사용자 등록 - 알림 허브" 
	description="Azure 모바일 서비스에서 등록을 수행할 때 Azure 알림 허브를 사용하여 iOS 앱에서 푸시 알림 등록을 요청하는 방법에 대해 알아봅니다." 
	services="notification-hubs" 
	documentationCenter="ios" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="yuaxu"/>

# 모바일 서비스를 사용하여 푸시 알림에 현재 사용자 등록

<div class="dev-center-tutorial-selector sublanding">
    <a href="/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/" title="Windows 스토어 C#">Windows 스토어 C#</a><a href="/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/" title="iOS" class="current">iOS</a>
</div>

이 항목에서는 Azure 모바일 서비스에 의해 등록이 수행될 때 Azure 알림 허브를 사용하여 푸시 알림 등록을 요청하는 방법을 보여 줍니다. 이 항목은 [알림 허브를 통해 사용자에게 알림] 자습서를 확장합니다. 이미 해당 자습서의 필수 단계를 완료하여 인증된 모바일 서비스를 만든 상태여야 합니다. 사용자 알림 시나리오에 대한 자세한 내용은 [알림 허브를 통해 사용자에게 알림]을 참조하십시오.

1. Xcode에서, 필수 자습서 [인증 시작]을 완료할 때 만든 프로젝트의 QSTodoService.h 파일을 열고 다음 **deviceToken** 속성을 추가합니다.

		@property (nonatomic) NSData* deviceToken;

 	이 속성은 장치 토큰을 저장합니다.

2. QSTodoService.m 파일에서 다음 **getDeviceTokenInHex** 메서드를 추가합니다.

			- (NSString*)getDeviceTokenInHex {
			    const unsigned *tokenBytes = [[self deviceToken] bytes];
			    NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
			                          ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
			                          ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
			                          ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
			    return hexToken;
			}

	이 메서드는 장치 토큰을 16진수 문자열 값으로 변환합니다.

3. QSAppDelegate.m 파일에서 다음 코드 줄을 **didFinishLaunchingWithOptions** 메서드에 추가합니다.

			[[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

	그러면 앱에서 푸시 알림이 사용하도록 설정됩니다.

4. 	QSAppDelegate.m 파일에서 다음 메서드를 추가합니다.

			- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
			    [QSTodoService defaultService].deviceToken = deviceToken;
			}

	그러면 **deviceToken** 속성이 업데이트됩니다.

	> [AZURE.NOTE]이때 이 메서드에 다른 코드가 있어서는 안 됩니다. [알림 허브 시작](/manage/services/notification-hubs/get-started-notification-hubs-ios/"%20target="_blank") 자습서를 완료할 때 추가된 **registerNativeWithDeviceToken** 메서드에 대한 호출이 이미 있는 경우 해당 호출을 주석으로 처리하거나 제거해야 합니다.

5.  (옵션) QSAppDelegate.m 파일에서 다음 처리기 메서드를 추가합니다.

			- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
			    NSLog(@"%@", userInfo);
			    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
			                          [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
			                          @"OK" otherButtonTitles:nil, nil];
			    [alert show];
			}

 	이 메서드는 앱이 실행되고 있는 동안 알림을 받으면 UI에 경고를 표시합니다.

6. QSTodoListViewController.m 파일에서 **registerForNotificationsWithBackEnd** 메서드를 추가합니다.

			- (void)registerForNotificationsWithBackEnd {
			    NSString* json = [NSString  stringWithFormat:@"{"platform":"ios", "deviceToken":"%@"}", [self.todoService getDeviceTokenInHex] ];

			    [self.todoService.client invokeAPI:@"register_notifications" data:[json dataUsingEncoding:NSUTF8StringEncoding] HTTPMethod:@"POST" parameters:nil headers:nil completion:^(id result, NSHTTPURLResponse *response, NSError *error) {
			        if (error != nil) {
			            NSLog(@"Registration failed: %@", error);
			        } else {
			            // display UIAlert with successful login
			            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
			            [alert show];
			        }
			    }];
			}

	이 메서드는 장치 토큰을 포함한 json 페이로드를 생성합니다. 그런 다음 모바일 서비스에서 사용자 지정 API를 호출하여 알림을 등록합니다. 이 메서드는 푸시 알림에 대한 장치 토큰을 만들어 장치 유형과 함께, 알림 허브에서 등록을 만드는 사용자 지정 API 메서드로 보냅니다. 이 사용자 지정 API는 [알림 허브를 통해 사용자에게 알림]에서 정의되었습니다.

7.	마지막으로, **viewDidAppear** 메서드에서 다음 예제에서와 같이 사용자가 인증된 후 이 새 **registerForNotificationsWithBackEnd** 메서드에 대한 호출을 추가합니다.

			- (void)viewDidAppear:(BOOL)animated
			{
			    MSClient *client = self.todoService.client;

			    if (client.currentUser != nil) {
			        return;
			    }

			    [client loginWithProvider:@"microsoftaccount" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
			        [self refresh];
			        [self registerForNotificationsWithBackEnd];
			    }];
			}

	> [AZURE.NOTE]이 작업은 페이지가 로드될 때마다 등록이 요청되도록 합니다. 앱에서 등록이 최신 상태가 되도록 이 등록을 주기적으로만 수행하고자 할 수 있습니다.
	
클라이언트 앱이 업데이트되었으므로 [알림 허브를 통해 사용자에게 알림]으로 돌아가서 알림 허브를 사용하여 알림을 보내도록 모바일 서비스를 업데이트합니다.

<!-- Anchors. -->

<!-- Images. -->


<!-- URLs. -->
[알림 허브를 통해 사용자에게 알림]: /manage/services/notification-hubs/notify-users
[인증 시작]: /develop/mobile/tutorials/get-started-with-users-ios/

[Azure Management Portal]: https://manage.windowsazure.com/
[Get Started with Notification Hubs]: /manage/services/notification-hubs/get-started-notification-hubs-ios/

<!--HONumber=54-->