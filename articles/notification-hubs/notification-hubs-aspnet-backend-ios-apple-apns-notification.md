<properties
	pageTitle=".NET 백 엔드를 통한 Azure 알림 허브의 iOS 사용자 알림"
	description="Azure에서 사용자에게 푸시 알림을 보내는 방법에 대해 알아봅니다. 코드 샘플은 백 엔드용 .NET API 및 Objective-C로 작성되었습니다."
	documentationCenter="ios"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="notification-hubs"/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="wesmc"/>

#.NET 백 엔드를 통한 Azure 알림 허브의 iOS 사용자 알림

[AZURE.INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

##개요

Azure의 푸시 알림 지원을 통해 사용하기 쉬운 다중 플랫폼 및 규모 확장 푸시 인프라에 액세스할 수 있어, 모바일 플랫폼용 소비자 응용 프로그램 및 엔터프라이즈 응용 프로그램 모두에 대한 푸시 알림을 매우 간단하게 구현할 수 있습니다. 이 자습서에서는 Azure 알림 허브를 사용하여 특정 장치에서 특정 앱 사용자에게 푸시 알림을 보내는 방법을 보여 줍니다. 지침 항목 [앱 백 엔드에서 등록](notification-hubs-registration-management.md#registration-management-from-a-backend)에 나와 있는 대로 ASP.NET WebAPI 백 엔드는 클라이언트를 인증하고 알림을 생성하는 데 사용됩니다.

> [AZURE.NOTE] 이 자습서에서는 [알림 허브 시작(iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md)에 설명된 대로 알림 허브를 만들고 구성했다고 가정합니다 이 자습서는 [보안 푸시(iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) 자습서의 필수 조건이기도 합니다. 모바일 앱을 백 엔드 서비스로 사용하려는 경우 [모바일 앱 푸시 시작](../app-service-mobile/app-service-mobile-ios-get-started-push.md)을 참조하세요.



[AZURE.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## iOS 앱 수정

1. [알림 허브 시작(iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) 자습서에서 만든 단일 페이지 보기 앱을 엽니다

	> [AZURE.NOTE] 이 섹션에서는 빈 조직 이름을 사용하여 프로젝트를 구성했다고 가정합니다. 이렇게 구성하지 않았으면 모든 클래스 이름 앞에 조직 이름을 추가해야 합니다.

2. Main.storyboard에서 개체 라이브러리에서 스크린샷에 표시된 다음 구성 요소를 추가합니다.

    ![][1]

	+ **Username**: 자리 표시자 텍스트가 있는 UITextField, *사용자 이름 입력*, 바로 아래 결과 레이블 보내기를 지정하고 왼쪽 및 오른쪽 여백 및 보내기 결과 레이블 아래로 제한됩니다.
	+ **암호**: 자리 표시자 텍스트가 있는 UITextField, *사용자 이름 입력*, 바로 아래 사용자 이름 텍스트 필드 및 왼쪽 및 오른쪽 여백 및 사용자 이름 텍스트 필드 아래로 제한됩니다. *반환 키* 아래 특성 검사기에서 **텍스트 항목 보안** 옵션을 선택합니다.
	+ **로그인**: 암호 텍스트 필드 바로 아래 레이블이 지정된 UIButton, *Control-Content* 아래 특성 검사기에서 **사용** 옵션을 선택 취소합니다.
	+ **WNS**: 허브에 설치된 경우 알림을 Windows 알림 서비스로 보낼 수 있는 레이블 및 스위치입니다. [Windows 시작](notification-hubs-windows-store-dotnet-get-started.md) 자습서를 참조하세요.
	+ **GCM**: 허브에 설치된 경우 알림을 Google 클라우드 메시징으로 보낼 수 있는 레이블 및 스위치입니다. [Android 시작](notification-hubs-android-get-started.md) 자습서를 참조하세요.
	+ **APNS**: Apple 플랫폼 알림 서비스로 알림을 보낼 수 있는 레이블 및 스위치입니다.
	+ **받는 사람 사용자 이름 **: 자리 표시자 텍스트가 있는 UITextField, *받는 사람 사용자 이름 태그*, GCM 레이블 바로 아래이며 왼쪽 및 오른쪽 여백으로 제한되고 GCM 레이블 아래입니다.


	일부 구성 요소는 [알림 허브(iOS) 시작](notification-hubs-ios-apple-push-notification-apns-get-started.md) 자습서에 추가됩니다.

3. **Ctrl** 보기의 구성 요소에서 ViewController.h로 끌어서 이 새 콘센트를 추가합니다.

	    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
		@property (weak, nonatomic) IBOutlet UITextField *PasswordField;
		@property (weak, nonatomic) IBOutlet UITextField *RecipientField;
		@property (weak, nonatomic) IBOutlet UITextField *NotificationField;

		// Used to enable the buttons on the UI
		@property (weak, nonatomic) IBOutlet UIButton *LogInButton;
		@property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;

		// Used to enabled sending notifications across platforms
		@property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;

		- (IBAction)LogInAction:(id)sender;

4. ViewController.h에서 다음 `#define`를 import 문을 바로 아래 추가합니다. *<Enter Your Backend Endpoint>* 자리 표시자를 이전 섹션에서 앱 백 엔드를 배포하는 데 사용한 대상 URL로 대체합니다. 예:**http://you_backend.azurewebsites.net*

		#define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"

4. 프로젝트에서 사용자가 만든 ASP.NET 백 엔드를 사용하여 **RegisterClient**라는 새 **Cocoa Touch 클래스**를 인터페이스에 만듭니다. `NSObject`에서 상속하는 클래스를 만듭니다. 그런 후 RegisterClient.h에 다음 코드를 추가합니다.

		@interface RegisterClient : NSObject

		@property (strong, nonatomic) NSString* authenticationHeader;

		-(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
			andCompletion:(void(^)(NSError*))completion;

		-(instancetype) initWithEndpoint:(NSString*)Endpoint;

		@end

5. RegisterClient.m에서 `@interface` 섹션을 업데이트합니다.

		@interface RegisterClient ()

		@property (strong, nonatomic) NSURLSession* session;
		@property (strong, nonatomic) NSURLSession* endpoint;

		-(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
					andCompletion:(void(^)(NSError*))completion;
		-(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
					completion:(void(^)(NSString*, NSError*))completion;
		-(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
					tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;

		@end

6. RegisterClient.m의 `@implementation`를 코드로 다음 코드로 바꿉니다.


		@implementation RegisterClient

		// Globals used by RegisterClient
		NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

		-(instancetype) initWithEndpoint:(NSString*)Endpoint
		{
		    self = [super init];
		    if (self) {
		        NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
		        _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
				_endpoint = Endpoint;
		    }
		    return self;
		}

		-(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
					andCompletion:(void(^)(NSError*))completion
		{
		    [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
		}

		-(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
					andCompletion:(void(^)(NSError*))completion
		{
		    NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

		    NSString *deviceTokenString = [[token description]
				stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
		    deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
									uppercaseString];

		    [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
				completion:^(NSString* registrationId, NSError *error) {
		        NSLog(@"regId: %@", registrationId);
		        if (error) {
		            completion(error);
		            return;
		        }

		        [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
					tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
		            if (error) {
		                completion(error);
		                return;
		            }

		            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
		            if (httpResponse.statusCode == 200) {
		                completion(nil);
		            } else if (httpResponse.statusCode == 410 && retry) {
		                [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
		            } else {
		                NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

		                completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
									userInfo:nil]);
		            }

		        }];
		    }];
		}

		-(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
					tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
		{
		    NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
													@"Tags": [tags allObjects]};
		    NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
								options:NSJSONWritingPrettyPrinted error:nil];

		    NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
												encoding:NSUTF8StringEncoding]);

		    NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
									registrationId];
		    NSURL* requestURL = [NSURL URLWithString:endpoint];
		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"PUT"];
		    [request setHTTPBody:jsonData];
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
													self.authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
		    [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

		    NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
				completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
			{
		        if (!error)
		        {
		            completion(response, error);
		        }
		        else
		        {
		            NSLog(@"Error request: %@", error);
		            completion(nil, error);
		        }
		    }];
		    [dataTask resume];
		}

		-(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
					completion:(void(^)(NSString*, NSError*))completion
		{
		    NSString* registrationId = [[NSUserDefaults standardUserDefaults]
										objectForKey:RegistrationIdLocalStorageKey];

		    if (registrationId)
		    {
		        completion(registrationId, nil);
		        return;
		    }

		    // request new one & save
		    NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
									_endpoint, token]];
		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"POST"];
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
													self.authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

		    NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
				completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
			{
		        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		        if (!error && httpResponse.statusCode == 200)
		        {
		            NSString* registrationId = [[NSString alloc] initWithData:data
						encoding:NSUTF8StringEncoding];

		            // remove quotes
		            registrationId = [registrationId substringWithRange:NSMakeRange(1,
										[registrationId length]-2)];

		            [[NSUserDefaults standardUserDefaults] setObject:registrationId
						forKey:RegistrationIdLocalStorageKey];
		            [[NSUserDefaults standardUserDefaults] synchronize];

		            completion(registrationId, nil);
		        }
		        else
		        {
		            NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
		            if (error)
		                completion(nil, error);
		            else {
		                completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
									userInfo:nil]);
		            }
		        }
		    }];
		    [dataTask resume];
		}

		@end

	위의 코드는 NSURLSession를 사용하여 앱 백 엔드에 대한 REST 호출을 수행하고 NSUserDefaults를 사용하여 알림 허브에서 반환된 registrationId를 로컬로 저장하는, 지침 문서 [앱 백 엔드에서 등록](notification-hubs-registration-management.md#registration-management-from-a-backend)에서 설명된 논리를 구현합니다.

	이 클래스가 제대로 작동하려면 **authorizationHeader** 속성을 설정해야 합니다. 이 속성은 로그인 후에 **ViewController** 클래스에 의해 설정됩니다.

7. ViewController.h에서 RegisterClient.h에 대한 `#import` 문을 추가합니다. 그런 다음 장치 토큰에 대한 선언 및 `@interface` 섹션에서 `RegisterClient` 인스턴스에 대한 참조를 추가합니다.

		#import "RegisterClient.h"

		@property (strong, nonatomic) NSData* deviceToken;
		@property (strong, nonatomic) RegisterClient* registerClient;

8. ViewController.m에서, 개인 메서드 선언을 `@interface` 섹션에 추가합니다.

		@interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

		// create the Authorization header to perform Basic authentication with your app back-end
		-(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
						AndPassword:(NSString*)password;

		@end

> [AZURE.NOTE] 다음 코드 조각은 보안 인증 체계가 아닙니다. **createAndSetAuthenticationHeaderWithUsername:AndPassword:** 구현을 레지스터 클라이언트 클래스(예: OAuth, Active Directory)에서 사용할 인증 토큰을 생성하는 특정 인증 메커니즘으로 대체해야 합니다.

9. 그런 다음 ViewController.m의 `@implementation`에 장치 토큰 및 인증 헤더 설정을 위한 구현을 추가하는 다음 코드를 추가합니다.

		-(void) setDeviceToken: (NSData*) deviceToken
		{
		    _deviceToken = deviceToken;
		    self.LogInButton.enabled = YES;
		}

		-(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
						AndPassword:(NSString*)password;
		{
		    NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

		    NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

		    self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
														encoding:NSUTF8StringEncoding];
		}

		-(BOOL)textFieldShouldReturn:(UITextField *)textField
		{
		    [textField resignFirstResponder];
		    return YES;
		}

	장치 토큰 설정이 어떻게 로그인 단추를 활성화하는지에 주목하세요. 이는 로그인 작업 중에 보기 컨트롤러가 푸시 알림을 앱 백 엔드에 등록하기 때문입니다. 따라서 장치 토큰이 제대로 설정되기 전에는 로그인 작업에 액세스하지 못하게 해야 합니다. 로그인 작업이 토큰 설정 전에 발생하는 경우 푸시 등록에서 로그인을 분리할 수 있습니다.

10. ViewController.m에서 다음 스니펫을 사용하여 **로그인** 단추에 대한 작업 메소드 및 ASP.NET 백엔드를 사용하여 알림 메시지를 보내는 메서드를 구현합니다.

		- (IBAction)LogInAction:(id)sender {
		    // create authentication header and set it in register client
		    NSString* username = self.UsernameField.text;
		    NSString* password = self.PasswordField.text;

		    [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

		    __weak ViewController* selfie = self;
		    [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil
				andCompletion:^(NSError* error) {
		        if (!error) {
		            dispatch_async(dispatch_get_main_queue(),
					^{
		                selfie.SendNotificationButton.enabled = YES;
		                [self MessageBox:@"Success" message:@"Registered successfully!"];
		            });
		        }
		    }];
		}


		- (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag
					Message:(NSString*)message
		{
		    NSURLSession* session = [NSURLSession
		    	sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil
		        delegateQueue:nil];

			// Pass the pns and username tag as parameters with the REST URL to the ASP.NET backend
		    NSURL* requestURL = [NSURL URLWithString:[NSString
				stringWithFormat:@"%@/api/notifications?pns=%@&to_tag=%@", BACKEND_ENDPOINT, pns,
				usernameTag]];

		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"POST"];

			// Get the mock authenticationheader from the register client
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
				self.registerClient.authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

		    //Add the notification message body
		    [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
		    [request setHTTPBody:[message dataUsingEncoding:NSUTF8StringEncoding]];

			// Execute the send notification REST API on the ASP.NET Backend
		    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
				completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
			{
		        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		        if (error || httpResponse.statusCode != 200)
		        {
		            NSString* status = [NSString stringWithFormat:@"Error Status for %@: %d\nError: %@\n",
										pns, httpResponse.statusCode, error];
		            dispatch_async(dispatch_get_main_queue(),
		            ^{
						// Append text because all 3 PNS calls may also have information to view
		                [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];
		            });
		            NSLog(status);
		        }

		        if (data != NULL)
		        {
		            xmlParser = [[NSXMLParser alloc] initWithData:data];
		            [xmlParser setDelegate:self];
		            [xmlParser parse];
		        }
			}];
		    [dataTask resume];
		}


11. **알림 보내기** 단추의 작업을 업데이트하여 ASP.NET 백엔드를 사용하고 스위치에서 사용하도록 설정된 모든 PNS를 보냅니다.


		- (IBAction)SendNotificationMessage:(id)sender
		{
		    //[self SendNotificationRESTAPI];
		    [self SendToEnabledPlatforms];
		}


		-(void)SendToEnabledPlatforms
		{
		    NSString* json = [NSString stringWithFormat:@""%@"",self.notificationMessage.text];

			[self.sendResults setText:@""];

		    if ([self.WNSSwitch isOn])
		        [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

		    if ([self.GCMSwitch isOn])
		        [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

		    if ([self.APNSSwitch isOn])
		        [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
		}



11. **ViewDidLoad** 함수에서 다음을 추가하여 RegisterClient 인스턴스를 인스턴스화하고 텍스트 필드에 대한 대리자를 설정합니다.

		self.UsernameField.delegate = self;
		self.PasswordField.delegate = self;
		self.RecipientField.delegate = self;
		self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];

12. 이제 **AppDelegate.m**에서 **application:didRegisterForPushNotificationWithDeviceToken:** 메서드의 모든 콘텐츠를 제거하고 다음으로 바꾸어 보기 컨트롤러에 APN에서 검색된 최신 장치 토큰이 포함되게 합니다.

		// Add import to the top of the file
		#import "ViewController.h"

	    - (void)application:(UIApplication *)application
	    			didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
	    {
		    ViewController* rvc = (ViewController*) self.window.rootViewController;
		    rvc.deviceToken = deviceToken;
		}

13. 마지막으로 **AppDelegate.m**에서 다음 메서드가 있는지 확인합니다.

		- (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
		}

## 응용 프로그램 테스트

1. XCode에서는 실제 iOS 장치에서 앱을 실행합니다(푸시 알림은 시뮬레이터에서 작동하지 않음).

2. iOS 앱 UI에서 사용자 이름과 암호를 입력합니다. 임의 문자열일 수 있지만 같은 문자열 값이어야 합니다. 그런 다음 **Log In**을 클릭합니다.

	![][2]


3. 등록 성공을 알리는 팝업이 표시됩니다. **확인**을 클릭합니다.

	![][3]

4. **받는사람 사용자 태그* 텍스트 필드에 다른 장치에서 등록에 사용되는 사용자 이름 태그를 입력합니다.
5. 알림 메시지를 입력하고 **알림 보내기**를 클릭합니다. 받는 사람 사용자 이름 태그로 등록된 장치만이 알림 메시지를 받습니다. 이러한 사용자에만 전송됩니다.

	![][4]


[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png

<!---HONumber=AcomDC_0622_2016-->