<properties 
	pageTitle="iOS용 알림 허브 지역화된 속보 자습서" 
	description="Azure 서비스 버스 알림 허브를 사용하여 지역화된 최신 뉴스 알림을 보내는 방법에 대해 알아봅니다(iOS)." 
	services="notification-hubs" 
	documentationCenter="ios" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="wesmc"/>
# 알림 허브를 사용하여 iOS 장치로 지역화된 속보 보내기

<div class="dev-center-tutorial-selector sublanding">
    	<a href="/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/" title="Windows Store C#">Windows 스토어 C#</a><a href="/documentation/articles/notification-hubs-ios-send-localized-breaking-news/" title="iOS" class="current">iOS</a>
</div>

##개요

이 항목에서는 Azure 알림 허브의 **템플릿** 기능을 사용하여 언어 및 장치별로 지역화된 속보 알림을 브로드캐스트하는 방법을 보여 줍니다. 이 자습서에서는 [알림 허브를 사용하여 속보 보내기]에서 만든 Windows 스토어 앱으로 시작합니다. 이 자습서를 완료하면 관심 있는 범주를 등록하고, 알림을 받을 언어를 지정하고, 선택한 범주에 대한 푸시 알림만 해당 언어로 받을 수 있습니다.


이 시나리오는 다음과 같은 두 부분으로 구성되어 있습니다.

- iOS 앱을 통해 클라이언트 장치는 언어를 지정하고 다른 속보 범주를 구독할 수 있습니다.

- 백 엔드는 Azure 알림 허브의 **태그** 및 **템플릿** 기능을 사용하여 알림을 브로드캐스트합니다.



##필수 조건

이미 [알림 허브를 사용하여 속보 보내기] 자습서를 완료한 상태여야 하며 사용 가능한 코드가 있어야 합니다. 이 자습서에서는 해당 코드를 기반으로 직접 빌드하기 때문입니다.

Visual Studio 2012도 필요합니다.



##템플릿 개념

[알림 허브를 사용하여 속보 보내기]에서 **태그**를 사용하여 다른 뉴스 범주에 대한 알림을 구독하는 앱을 빌드했습니다.
그러나 많은 앱은 여러 시장을 대상으로 하고 지역화를 필요로 합니다. 즉, 알림 자체의 내용을 지역화해서 올바른 장치 집합으로 전달해야 합니다.
이 항목에서는 알림 허브의 **템플릿** 기능을 사용하여 지역화된 속보 알림을 쉽게 전달하는 방법을 보여 줍니다.

참고: 지역화된 알림을 보내는 한 가지 방법은 각 태그의 여러 버전을 만드는 것입니다. 예를 들어 영어, 프랑스어 및 북경어를 지원하려면 세계 뉴스에 대한 3가지 태그 즉 "world_en", "world_fr" 및 "world_ch"가 필요합니다. 그런 다음 이러한 각 태그로 세계 뉴스의 지역화된 버전을 보내야 합니다. 이 항목에서는 템플릿을 사용하여 태그 급증과 여러 메시지 보내기 요구 사항을 방지합니다.

높은 수준에서 봤을 때 템플릿은 특정 장치가 알림을 받는 방법을 지정하는 한 가지 방법입니다. 템플릿은 앱 백 엔드에서 전송한 메시지의 일부인 속성을 참조하여 정확한 페이로드 형식을 지정합니다. 여기서는 지원되는 모든 언어가 포함된 로캘을 알 수 없는 메시지를 보냅니다.

	{
		"News_English": "...",
		"News_French": "...",
		"News_Mandarin": "..."
	}

그런 다음 올바른 속성을 참조하는 템플릿을 사용하여 장치가 등록되도록 합니다. 예를 들어 프랑스어 뉴스를 등록하려는 iOS 앱은 다음을 등록합니다.

	{
		aps:{
			alert: "$(News_French)"
		}
	}

템플릿은 [알림 허브 지침] 문서에서 자세한 내용을 알아볼 수 있는 매우 강력한 기능입니다. 템플릿 식 언어에 대한 참조는 [방법: 서비스 버스 알림 허브(iOS 앱)]에 있습니다.

##앱 사용자 인터페이스

이제 [알림 허브를 사용하여 속보 보내기] 항목에서 만든 속보 앱을 수정하고 템플릿을 사용하여 지역화된 속보를 보냅니다.


MainStoryboard_iPhone.storyboard에서 지원되는 3가지 언어 즉 영어, 프랑스어 및 북경어로 분할된 제어를 추가합니다.

![][13]

그런 다음 아래와 같이 ViewController.h에 IBOutlet을 추가해야 합니다.

![][14]

##iOS 앱 빌드

지역화된 메시지를 받도록 클라이언트 앱을 조정하려면  *네이티브* 등록(즉, 템플릿을 지정하는 등록)을 템플릿 등록으로 바꿔야 합니다.

1. Notification.h에서 아래와 같이  *retrieveLocale* 메서드를 추가하고 저장 및 구독 메서드를 수정합니다.

		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

		- (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

		- (NSSet*) retrieveCategories;

		- (int) retrieveLocale;

	Notification.m에서 로캘 매개 변수를 추가하고 사용자 기본값에 저장하여  *storeCategoriesAndSubscribe* 메서드를 수정합니다.

		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
		    [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
		    [defaults synchronize];

		    [self subscribeWithLocale: locale categories:categories completion:completion];
		}

	그런 다음 로캘을 포함하도록  *subscribe* 메서드를 수정합니다.

		- (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

		    NSString* localeString;
		    switch (locale) {
		        case 0:
		            localeString = @"English";
		            break;
		        case 1:
		            localeString = @"French";
		            break;
		        case 2:
		            localeString = @"Mandarin";
		            break;
		    }

		    NSString* template = [NSString stringWithFormat:@"{"aps":{"alert":"$(News_%@)"},"inAppMessage":"$(News_%@)"}", localeString, localeString];

		    [hub registerTemplateWithDeviceToken:self.deviceToken name:@"newsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
		}

	이제  *registerNativeWithDeviceToken* 대신 *registerTemplateWithDeviceToken* 메서드를 어떻게 사용하고 있는지 살펴봅니다. 템플릿을 등록할 때는 json 템플릿과 템플릿의 이름도 제공해야 합니다(이 앱이 다른 템플릿을 등록할 수 있으므로). 해당 뉴스에 대한 알림을 받도록 할 것이므로 범주를 태그로 등록해야 합니다.

	마지막으로, 사용자 기본 설정에서 로캘을 검색하기 위한 메서드를 추가합니다.

		- (int) retrieveLocale {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

		    int locale = [defaults integerForKey:@"BreakingNewsLocale"];

		    return locale < 0?0:locale;
		}

3. 이제 Notifications 클래스를 수정했으므로 ViewController가 새 UISegmentControl을 사용하는지 확인해야 합니다.  *viewDidLoad* 메서드에 다음 줄을 추가하여 현재 선택된 로캘을 표시해야 합니다.

		self.Locale.selectedSegmentIndex = [notifications retrieveLocale];

	그런 다음  *subscribe* 메서드에서  *storeCategoriesAndSubscribe*에 대한 호출을 다음으로 변경합니다.

		[notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
	        if (!error) {
	            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
	                                  @"Subscribed!" delegate:nil cancelButtonTitle:
	                                  @"OK" otherButtonTitles:nil, nil];
	            [alert show];
	        } else {
	            NSLog(@"Error subscribing: %@", error);
	        }
	    }];

4. 마지막으로, 앱이 시작될 때 등록을 올바르게 새로 고칠 수 있도록 AppDelegate.m에서 *didRegisterForRemoteNotificationsWithDeviceToken* 메서드를 업데이트해야 합니다. 알림의  *subscribe* 메서드에 대한 호출을 다음으로 변경합니다.

		NSSet* categories = [notifications retrieveCategories];
	    int locale = [notifications retrieveLocale];
	    [notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];

##백 엔드에서 지역화된 알림 보내기

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]


## 다음 단계

템플릿 사용에 대한 자세한 내용은 다음을 참조하세요.

- [알림 허브를 통해 사용자에게 알림: ASP.NET]
- [알림 허브를 통해 사용자에게 알림: 모바일 서비스]
- [알림 허브 지침]

템플릿 식 언어에 대한 참조는 [iOS용 알림 허브 사용 방법]에 있습니다.






<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[방법: 서비스 버스 알림 허브(iOS 앱)]: http://msdn.microsoft.com/library/jj927168.aspx
[알림 허브를 사용하여 속보 보내기]: /manage/services/notification-hubs/breaking-news-ios
[모바일 서비스]: /develop/mobile/tutorials/get-started
[알림 허브를 통해 사용자에게 알림: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[알림 허브를 통해 사용자에게 알림: 모바일 서비스]: /manage/services/notification-hubs/notify-users
[앱 제출 페이지]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[내 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows용 Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[모바일 서비스 시작]: /develop/mobile/tutorials/get-started/#create-new-service
[데이터 시작]: /develop/mobile/tutorials/get-started-with-data-ios
[인증 시작]: /develop/mobile/tutorials/get-started-with-users-ios
[푸시 알림 시작]: /develop/mobile/tutorials/get-started-with-push-ios
[앱 사용자에 대한 푸시 알림]: /develop/mobile/tutorials/push-notifications-to-users-ios
[스크립트를 통해 사용자 권한 부여]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript 및 HTML]: ../get-started-with-push-js.md

[Azure 관리 포털]: https://manage.windowsazure.com/
[모바일 서비스에 대한 Windows 개발자 미리 보기 등록 단계]: ../mobile-services-windows-developer-preview-registration.md
[wns 개체]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[알림 허브 지침]: http://msdn.microsoft.com/library/jj927170.aspx
[iOS용 알림 허브 사용 방법]: http://msdn.microsoft.com/library/jj927168.aspx

<!--HONumber=49--> 