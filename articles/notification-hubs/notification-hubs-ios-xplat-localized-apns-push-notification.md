<properties
	pageTitle="iOS용 알림 허브 지역화된 속보 자습서"
	description="Azure 서비스 버스 알림 허브를 사용하여 지역화된 최신 뉴스 알림을 보내는 방법에 대해 알아봅니다(iOS)."
	services="notification-hubs"
	documentationCenter="ios"
	authors="wesmc7777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/29/2016"
	ms.author="wesmc"/>

# 알림 허브를 사용하여 iOS 장치로 지역화된 속보 보내기

> [AZURE.SELECTOR]
- [Windows 스토어 C#](notification-hubs-windows-store-dotnet-send-localized-breaking-news)
- [iOS](notification-hubs-ios-send-localized-breaking-news)


##개요

이 항목에서는 Azure 알림 허브의 [템플릿](notification-hubs-templates-cross-platform-push-messages.md) 기능을 사용하여 언어 및 장치별로 지역화된 속보 알림을 브로드캐스트하는 방법을 보여 줍니다. 이 자습서에서는 [알림 허브를 사용하여 속보 보내기]에서 만든 iOS 앱을 시작합니다. 이 자습서를 완료하면 관심 있는 범주를 등록하고, 알림을 받을 언어를 지정하고, 선택한 범주에 대한 푸시 알림만 해당 언어로 받을 수 있습니다.


이 시나리오는 다음과 같은 두 부분으로 구성되어 있습니다.

- iOS 앱을 통해 클라이언트 장치는 언어를 지정하고 다른 속보 범주를 구독할 수 있습니다.

- 백 엔드는 Azure 알림 허브의 **태그** 및 **템플릿** 기능을 사용하여 알림을 브로드캐스트합니다.



##필수 조건

[알림 허브를 사용하여 속보 보내기] 자습서를 이미 완료하고 사용 가능한 코드가 있어야 합니다. 이 자습서는 해당 코드를 기반으로 직접 빌드됩니다.

Visual Studio 2012 이상은 선택 사항입니다.



##템플릿 개념

[알림 허브를 사용하여 속보 보내기]에서는 **태그**를 사용하여 다른 뉴스 범주에 대한 알림을 구독하는 앱을 빌드했습니다. 하지만 대부분의 앱은 여러 시장을 대상으로 하므로 지역화해야 합니다. 즉, 알림 자체의 내용을 지역화해서 올바른 장치 집합으로 전달해야 합니다. 이 항목에서는 알림 허브의 **템플릿** 기능을 사용하여 지역화된 속보 알림을 쉽게 제공하는 방법을 보여 줍니다.

참고: 지역화된 알림을 보내는 한 가지 방법은 각 태그의 여러 버전을 만드는 것입니다. 예를 들어 영어, 프랑스어 및 북경어를 지원하려면 세계 뉴스에 대한 3가지 태그 즉, "world\_en", "world\_fr" 및 "world\_ch"가 필요합니다. 그런 다음 이러한 각 태그로 세계 뉴스의 지역화된 버전을 보내야 합니다. 이 항목에서는 템플릿을 사용하여 태그의 확산을 방지하고 여러 메시지를 보낼 필요가 없도록 합니다.

높은 수준의 템플릿을 사용하면 특정 장치에서 알림을 받는 방법을 지정할 수 있습니다. 템플릿은 앱에서 백 엔드로 보낸 메시지에 포함된 속성을 참조하여 정확한 페이로드 형식을 지정합니다. 여기서는 지원되는 모든 언어가 포함된 로캘을 알 수 없는 메시지를 보냅니다.

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

템플릿은 매우 강력한 기능입니다. 자세한 내용은 [템플릿](notification-hubs-templates-cross-platform-push-messages.md) 문서를 참조하세요.

##앱 사용자 인터페이스

이제 템플릿을 사용하여 지역화된 속보를 보내도록, [알림 허브를 사용하여 속보 보내기] 항목에서 만든 속보 앱을 수정합니다.


MainStoryboard\_iPhone.storyboard에서 지원되는 3가지 언어, 즉 영어, 프랑스어 및 북경어로 분할된 제어를 추가합니다.

![][13]

그런 다음 아래와 같이 ViewController.h에 IBOutlet을 추가해야 합니다.

![][14]

##iOS 앱 빌드


1. Notification.h에서 아래와 같이 *retrieveLocale* 메서드를 추가하고 저장 및 구독 메서드를 수정합니다.

		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

		- (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

		- (NSSet*) retrieveCategories;

		- (int) retrieveLocale;

	Notification.m에서 로캘 매개 변수를 추가하고 사용자 기본값에 저장하여 *storeCategoriesAndSubscribe* 메서드를 수정합니다.

		- (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
		    [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
		    [defaults synchronize];

		    [self subscribeWithLocale: locale categories:categories completion:completion];
		}

	그런 다음 로캘을 포함하도록 *subscribe* 메서드를 수정합니다.

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

		    [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
		}

	이제 *registerNativeWithDeviceToken* 대신 *registerTemplateWithDeviceToken* 메서드를 어떻게 사용하고 있는지 살펴봅니다. 템플릿을 등록할 때는 json 템플릿과 템플릿의 이름도 제공해야 합니다(이 앱이 다른 템플릿을 등록할 수 있으므로). 해당 뉴스에 대한 알림을 받도록 할 것이므로 범주를 태그로 등록해야 합니다.

	사용자 기본 설정에서 로캘을 검색하기 위한 메서드를 추가합니다.

		- (int) retrieveLocale {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

		    int locale = [defaults integerForKey:@"BreakingNewsLocale"];

		    return locale < 0?0:locale;
		}

2. 이제 Notifications 클래스를 수정했으므로 ViewController가 새 UISegmentControl을 사용하는지 확인해야 합니다. *viewDidLoad* 메서드에 다음 줄을 추가하여 현재 선택된 로캘을 표시해야 합니다.

		self.Locale.selectedSegmentIndex = [notifications retrieveLocale];

	그런 다음 *subscribe* 메서드에서 *storeCategoriesAndSubscribe*에 대한 호출을 다음으로 변경합니다.

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

3. 마지막으로, 앱이 시작될 때 등록을 올바르게 새로 고칠 수 있도록 AppDelegate.m에서 *didRegisterForRemoteNotificationsWithDeviceToken* 메서드를 업데이트해야 합니다. 알림의 *subscribe* 메서드에 대한 호출을 다음으로 변경합니다.

		NSSet* categories = [self.notifications retrieveCategories];
	    int locale = [self.notifications retrieveLocale];
	    [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];

##(선택 사항) .NET 콘솔 앱에서 지역화된 템플릿 알림 보내기

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]



##(선택 사항) 장치에서 지역화된 템플릿 알림 보내기

Visual Studio에 액세스할 수 없거나 장치의 앱에서 직접 지역화된 템플릿 알림을 보내는 테스트만 하기를 원하는 경우입니다. 이전 자습서에 정의한 `SendNotificationRESTAPI` 메서드에 지역화된 템플릿 매개 변수를 간단히 추가할 수 있습니다.

		- (void)SendNotificationRESTAPI:(NSString*)categoryTag
		{
		    NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
									 defaultSessionConfiguration] delegate:nil delegateQueue:nil];

		    NSString *json;

		    // Construct the messages REST endpoint
		    NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
		                                       HUBNAME, API_VERSION]];

		    // Generated the token to be used in the authorization header.
		    NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

		    //Create the request to add the template notification message to the hub
		    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
		    [request setHTTPMethod:@"POST"];

		    // Add the category as a tag
		    [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

			// Template notification
	        json = [NSString stringWithFormat:@"{"messageParam":"Breaking %@ News : %@","
					"News_English":"Breaking %@ News in English : %@","
					"News_French":"Breaking %@ News in French : %@","
					"News_Mandarin":"Breaking %@ News in Mandarin : %@","
	                categoryTag, self.notificationMessage.text,
	                categoryTag, self.notificationMessage.text,  // insert English localized news here
	                categoryTag, self.notificationMessage.text,  // insert French localized news here
	                categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

	        // Signify template notification format
	        [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

			// JSON Content-Type
			[request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

		    //Authenticate the notification message POST request with the SaS token
		    [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

		    //Add the notification message body
		    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

		    // Send the REST request
		    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
		               completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
	           {
	           NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
	               if (error || httpResponse.statusCode != 200)
	               {
	                   NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
	               }
	               if (data != NULL)
	               {
	                   //xmlParser = [[NSXMLParser alloc] initWithData:data];
	                   //[xmlParser setDelegate:self];
	                   //[xmlParser parse];
	               }
	           }];

		    [dataTask resume];
		}




## 다음 단계

템플릿 사용에 대한 자세한 내용은 다음을 참조하세요.

- [알림 허브를 통해 사용자에게 알림: ASP.NET]
- [알림 허브를 통해 사용자에게 알림: 모바일 서비스]






<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[알림 허브를 사용하여 속보 보내기]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[알림 허브를 통해 사용자에게 알림: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[알림 허브를 통해 사용자에게 알림: 모바일 서비스]: /manage/services/notification-hubs/notify-users
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md

[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx

<!---HONumber=AcomDC_0706_2016-->