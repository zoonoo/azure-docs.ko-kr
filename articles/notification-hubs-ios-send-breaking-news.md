<properties 
	pageTitle="알림 허브 속보 자습서 - iOS" 
	description="Azure 서비스 버스 알림 허브를 사용하여 iOS 장치에 최신 뉴스 알림을 보내는 방법에 대해 알아봅니다." 
	services="notification-hubs" 
	documentationCenter="ios" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="yuaxu"/>

# 알림 허브를 사용하여 속보 보내기
<div class="dev-center-tutorial-selector sublanding">
	<a href="/ko-kr/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/" title="Windows Universal" >Windows Universal</a><a href="/ko-kr/documentation/articles/notification-hubs-windows-phone-send-breaking-news/" title="Windows Phone">Windows Phone</a><a href="/ko-kr/documentation/articles/notification-hubs-ios-send-breaking-news/" title="iOS" class="current">iOS</a>
	<a href="/ko-kr/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/" title="Android">Android</a>
</div>

이 항목에서는 Azure 알림 허브를 사용하여 iOS 앱에 속보 알림을 브로드캐스트하는 방법을 보여 줍니다. 완료하면, 관심이 있는 속보 범주를 등록하고 해당 범주의 푸시 알림만 받을 수 있습니다. 이 시나리오는 RSS 수집기, 음악 애호가를 위한 앱 등 이전에 관심을 보인 사용자 그룹에 알림을 보내야 하는 많은 앱에 공통된 패턴입니다.

브로드캐스트 시나리오를 사용하려면 알림 허브에서 등록을 만들 때 하나 이상의 _태그_를 포함하면 됩니다. 태그에 알림이 전송되면 태그에 대해 등록된 모든 장치에서 알림을 받게 됩니다. 태그는 단순히 문자열이므로 사전에 프로비전해야 할 필요가 없습니다. 태그에 대한 자세한 내용은 [알림 허브 지침]을 참조하세요.

이 자습서에서는 이 시나리오를 사용하기 위한 다음 기본 단계를 차례로 안내합니다.

1. [앱에 범주 선택 추가]
2. [알림 등록]
3. [백 엔드에서 알림 보내기]
4. [앱 실행 및 알림 생성]

이 항목은 [알림 허브 시작][시작]에서 만든 앱을 기반으로 작성되었습니다. 이 자습서를 시작하기 전에 먼저 [알림 허브 시작][시작]을 완료해야 합니다.

## <a name="adding-categories"></a>앱에 범주 선택 추가

첫 번째 단계는 기존의 스토리보드에 사용자가 등록할 범주를 선택할 수 있도록 하는 UI 요소를 추가하는 것입니다. 사용자가 선택한 범주는 장치에 저장됩니다. 앱을 시작하면 장치 등록이 선택한 범주와 함께 태그로서 알림 허브에 생성됩니다.

2. MainStoryboard_iPhone.storyboard의 개체 라이브러리에서 다음 구성 요소를 추가합니다.
	+ "Breaking News" 텍스트가 포함된 레이블
	+ "World", "Politics", "Business", "Technology", "Science", "Sports" 범주 텍스트가 포함된 레이블
	+ 6개의 스위치(범주당 1개)
	+ "Subscribe" 단추

	스토리보드는 다음과 같이 표시됩니다.

	![][3]

3. 단말기 편집기에서 모든 스위치에 대한 콘센트를 만든 다음 "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"로 지정합니다.

	![][4]

4. "subscribe" 단추에 대한 동작을 만듭니다. BreakingNewsViewController.h는 다음을 포함해야 합니다.

		@property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

		- (IBAction)subscribe:(id)sender;

5. 새 클래스 `Notifications`를 만듭니다. 다음 코드를 Notifications.h 파일의 인터페이스 섹션에 복사합니다.

		@property NSData* deviceToken;

		- (void)storeCategoriesAndSubscribeWithCategories:(NSArray*) categories completion: (void (^)(NSError* error))completion;
		- (void)subscribeWithCategories:(NSSet*) categories completion:(void (^)(NSError *))completion;

6. 다음 import 지시문을 Notifications.m에 추가합니다.

		#import <WindowsAzureMessaging/WindowsAzureMessaging.h>

7. 다음 코드를 Notifications.m 파일의 구현 섹션에 복사합니다.

		- (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

		    [self subscribeWithCategories:categories completion:completion];
		}

		- (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion{
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string with listen access>" notificationHubPath:@"<hub name>"];

		    [hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];
		}

	이 클래스는 로컬 저장소를 사용하여, 이 장치에서 받아야 할 뉴스의 범주를 저장합니다. 이러한 범주를 등록하기 위한 메서드도 이 클래스에 포함됩니다.

4. 위 코드에서 `<hub name>`과 `<connection string with listen access>` 자리 표시자를 알림 허브 이름과 앞서 얻었던 *DefaultListenSharedAccessSignature*의 연결 문자열로 바꿉니다.

	> [AZURE.NOTE] 클라이언트 앱과 함께 배포되는 자격 증명은 일반적으로 안전하지 않기 때문에 클라이언트 앱과 함께 listen access용 키만 배포해야 합니다. Listen access를 통해 앱에서 알림을 등록할 수 있지만, 기존 등록을 수정할 수 없으며 알림을 전송할 수도 없습니다. 안전한 백 엔드 서비스에서 알림을 보내고 기존 등록을 변경하는 데에는 모든 액세스 키가 사용됩니다.

8. BreakingNewsAppDelegate.h 파일에서 다음 속성을 추가합니다.

		@property (nonatomic) Notifications* notifications;

	이 속성은 AppDelegate에서 Notification 클래스의 단일 항목 인스턴스를 만듭니다.

9. BreakingNewsAppDelegate.m의 **didFinishLaunchingWithOptions** 메서드에서 **registerForRemoteNotificationTypes** 앞에 다음 코드를 추가합니다.

		self.notifications = [[Notifications alloc] init];

	이 코드는 Notification 단일 항목을 초기화합니다.

10. BreakingNewsAppDelegate.m의 **didRegisterForRemoteNotificationsWithDeviceToken** 메서드에서 **registerNativeWithDeviceToken** 호출을 제거하고 다음 코드를 추가합니다.

		self.notifications.deviceToken = deviceToken;

	이때 **didRegisterForRemoteNotificationsWithDeviceToken** 메서드에 다른 코드가 없어야 합니다.

11.	BreakingNewsAppDelegate.m에 다음 메서드를 추가합니다.

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:
			(NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		    [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
		    [alert show];
	    }

	이 메서드는 단일 **UIAlert**를 표시하여 앱이 실행 중일 때 수신된 알림을 처리합니다.

9. BreakingNewsViewController.m에서 다음 코드를 XCode 생성 **subscribe** 메서드에 복사합니다.

		NSMutableArray* categories = [[NSMutableArray alloc] init];

	    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
	    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
	    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
	    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
	    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
	    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

	    Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
	        if (!error) {
	            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
	                                  @"Subscribed!" delegate:nil cancelButtonTitle:
	                                  @"OK" otherButtonTitles:nil, nil];
	            [alert show];
	        } else {
	            NSLog(@"Error subscribing: %@", error);
	        }
	    }];

	이 메서드는 범주의 **NSMutableArray**를 만들고 **Notifications** 클래스를 사용하여 로컬 저장소에 목록을 저장하고 알림 허브에 해당 태그를 등록합니다. 범주가 변경되면 새 범주로 등록이 다시 생성됩니다.

이제 사용자가 범주 선택을 변경할 때마다 앱은 범주 집합을 장치의 로컬 저장소에 저장하고 알림 허브에 등록할 수 있습니다.

## <a name="register"></a>알림 등록

다음 단계에서는 로컬 저장소에 저장된 범주를 사용하여 시작 시 알림 허브에 등록합니다.

> [AZURE.NOTE] APNS(Apple Push Notification Service)에서 할당하는 장치 토큰은 언제든지 변경될 수 있으므로 알림 실패를 피하려면 알림을 자주 등록해야 합니다. 이 예제에서는 앱이 시작될 때마다 알림을 등록합니다. 자주(하루 두 번 이상) 실행되는 앱에서는 이전 등록 이후 만 하루가 지나지 않은 경우 대역폭 유지를 위한 등록을 건너뛸 수 있습니다.

1. 다음 메서드를 Notifications.h 파일의 인터페이스 섹션에 추가합니다.

		- (NSSet*)retrieveCategories;

	이 코드는 Notifications 클래스에서 범주를 검색합니다.

2. Notifications.m 파일에 해당 구현을 추가합니다.

		- (NSSet*)retrieveCategories {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

		    NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

		    if (!categories) return [[NSSet alloc] init];
		    return [[NSSet alloc] initWithArray:categories];
		}

2. **didRegisterForRemoteNotificationsWithDeviceToken** 메서드에 다음 코드를 추가합니다:

		Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    NSSet* categories = [notifications retrieveCategories];
	    [notifications subscribeWithCategories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];

	이제 앱이 시작될 때마다 로컬 저장소에서 범주를 검색하고, 이러한 범주에 대한 등록을 요청하게 됩니다.

3. BreakingNewsViewController.h에서 다음 코드를 **viewDidLoad** 메서드에 추가합니다.

		Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    NSSet* categories = [notifications retrieveCategories];

	    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
	    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
	    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
	    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
	    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
	    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;

	전에 저장한 범주의 상태를 기반으로 시작 시 UI가 업데이트됩니다.

이제 앱이 완료되며, 사용자가 범주 선택을 변경할 때마다 알림 허브 등록에 사용된 장치의 로컬 저장소에 범주 집합을 저장할 수 있습니다. 다음에는 범주 알림을 이 앱에 보낼 수 있는 백 엔드를 정의합니다.

<h2><a name="send"></a>백 엔드에서 알림 보내기</h2>

[AZURE.INCLUDE [알림 허브-백 엔드](../includes/notification-hubs-back-end.md)]

## <a name="test-app"></a>앱 실행 및 알림 생성

1. 실행 단추를 눌러 프로젝트를 빌드하고 앱을 시작합니다.

	![][2]

	앱 UI는 구독할 범주를 선택하도록 하는 토글 집합을 제공합니다.

2. 하나 이상의 범주 토글을 사용하도록 설정한 후 **구독**을 클릭합니다.

	**구독**을 선택하면 앱은 선택한 범주를 태그로 변환하고 알림 허브에서 선택한 태그에 대한 새로운 장치 등록을 요청합니다.

4. 다음 방법 중 하나로 백 엔드에서 새 알림을 보냅니다.

	+ **콘솔 앱:** 콘솔 앱을 시작합니다.

	+ **Java/PHP:** 앱/스크립트를 실행합니다.

5. 선택한 범주에 대한 알림이 알림 메시지로 나타납니다.

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 범주별로 속보를 브로드캐스트하는 방법에 대해 알아보았습니다. 이제 기타 고급 알림 허브 시나리오를 다루는 다음 자습서 중 하나를 완료해 보세요.

+ **[알림 허브를 사용하여 지역화된 속보 브로드캐스트]**

	지역화된 알림을 보낼 수 있도록 속보 앱을 확장하는 방법에 대해 알아보세요.

+ **[알림 허브를 통해 사용자에게 알림]**

	인증된 특정 사용자에게 알림을 푸시하는 방법에 대해 알아보세요. 이 방법은 특정 사용자에게만 알림을 보내기 위한 훌륭한 솔루션입니다.

<!-- Anchors. -->
[앱에 범주 선택 추가]: #adding-categories
[알림 등록]: #register
[백 엔드에서 알림 보내기]: #send
[앱 실행 및 알림 생성]: #test-app
[다음 단계]: #next-steps

<!-- Images. -->
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png
[4]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios3.png







<!-- URLs. -->
[방법: 서비스 버스 알림 허브(iOS 앱)]: http://msdn.microsoft.com/library/jj927168.aspx
[알림 허브를 사용하여 지역화된 속보 브로드캐스트]: /ko-kr/manage/services/notification-hubs/breaking-news-localized-dotnet/
[모바일 서비스]: /ko-kr/develop/mobile/tutorials/get-started
[알림 허브를 통해 사용자에게 알림]: /ko-kr/manage/services/notification-hubs/notify-users/

[Azure 관리 포털]: https://manage.windowsazure.com/
[알림 허브 지침]: http://msdn.microsoft.com/library/jj927170.aspx
[iOS용 알림 허브 사용 방법]: http://msdn.microsoft.com/library/jj927168.aspx
[시작]: /ko-kr/manage/services/notification-hubs/get-started-notification-hubs-ios/

<!--HONumber=45--> 
