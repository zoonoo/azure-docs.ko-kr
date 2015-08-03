<properties
	pageTitle="Azure 알림 허브 시작"
	description="Azure 알림 허브를 사용하여 알림을 푸시하는 방법에 대해 알아봅니다."
	services="notification-hubs"
	documentationCenter="ios"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article" 
	ms.date="05/27/2015"
	ms.author="wesmc"/>

# 알림 허브 시작

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##개요

이 항목에서는 Azure 알림 허브를 사용하여 iOS 응용 프로그램에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 APNS(Apple Push Notification Service)를 사용하여 푸시 알림을 받는 빈 iOS 앱을 만듭니다. 완료하면 알림 허브를 사용하여 앱을 실행하는 모든 장치로 푸시 알림을 브로드캐스트할 수 있습니다.

이 자습서에서는 알림 허브를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다.

##필수 조건

이 자습서를 사용하려면 다음 필수 조건이 필요합니다.

+ [모바일 서비스 iOS SDK]
+ [XCode 6][Install Xcode]
+ iOS 8(이상) 지원 장치
+ iOS 개발자 프로그램 멤버 자격

   >[AZURE.NOTE]푸시 알림 구성 요구 사항 때문에 시뮬레이터 대신 iOS 지원 장치(iPhone 또는 iPad)에서 푸시 알림을 배포 및 테스트해야 합니다.

이 자습서를 완료해야 다른 모든 iOS 앱용 알림 허브 자습서를 진행할 수 있습니다.

> [AZURE.NOTE]이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-kr%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started)을 참조하십시오.

[AZURE.INCLUDE [알림 허브가 Apple 푸시 알림 사용](../../includes/notification-hubs-enable-apple-push-notifications.md)]

##알림 허브 구성

이 섹션에서는 사용자가 만든 푸시 인증서를 사용하여 새 알림 허브를 만들고 구성하는 과정을 설명합니다. 이미 만든 알림 허브를 사용하려는 경우 2-5단계를 건너뛸 수 있습니다.


1. Keychain Access에서는 **인증서** 범주에서 만든 새 푸시 인증서를 마우스 오른쪽 단추로 클릭합니다. **Export**를 클릭하고 파일의 이름을 지정한 후 **.p12** 형식을 선택하고 **Save**를 클릭합니다.

    ![][1]

	내보낸 인증서의 파일 이름과 위치를 적어둡니다.

	>[AZURE.NOTE]이 자습서에서는 QuickStart.p12 파일을 만듭니다. Your file name and location might be different.

2. [Azure 관리 포털]에 로그온하고 화면 맨 아래에 있는 **+새로 만들기**를 클릭합니다.

3. **앱 서비스**, **Service Bus**, **알림 허브**, **빠른 생성**을 차례로 클릭합니다.

   	![][2]

4. 알림 허브의 이름을 입력하고 원하는 지역을 선택한 후 **새 알림 허브 만들기**를 클릭합니다.

   	![][3]

5. 방금 만든 네임스페이스(일반적으로 ***알림 허브 이름*-ns**)를 클릭하여 대시보드를 엽니다.

   	![][4]

6. 맨 위에 있는 **알림 허브** 탭을 클릭한 후 방금 만든 알림 허브를 클릭합니다.

   	![][5]

7. 위쪽의 **구성** 탭을 클릭하고 인증서 손도장 (thumbprint)을 업로드하는 Apple 알림 설정에서 **업로드** 단추를 클릭합니다. 그런 다음 앞서 내보낸 **.p12** 인증서 및 인증서의 암호를 선택합니다. **프로덕션**(스토어에서 앱을 구매한 사용자에게 푸시 알림을 보내려는 경우) 또는 **샌드박스**(개발 중에) 중 어떤 푸시 서비스를 사용할지를 선택해야 합니다.

   	![][6]

8. 맨 위에 있는 **대시보드** 탭을 클릭한 후 **연결 문자열 보기**를 클릭합니다. 두 연결 문자열을 기록해 둡니다.

   	![][7]

이제 알림 허브가 APNS과 작동하도록 구성되었으며 앱을 등록하고 알림을 보내기 위한 연결 문자열이 있습니다.

##알림 허브에 앱 연결

1. XCode에서 새 iOS 프로젝트를 만들고 **Single View Application** 템플릿을 선택합니다.

   	![][8]

2. 새 프로젝트에 대한 옵션을 설정할 때 Apple 개발 포털에서 이전에 번들 ID를 설정한 경우에 사용한 동일한 **제품 이름** 및 **조직 식별자**를 사용해야 합니다.

	![][11]

3. **대상** 아래에서 프로젝트 이름을 클릭하고 **빌드 설정** 탭을 클릭하며 **코드 서명 ID**를 확장한 다음 **디버그**에서 **코드 서명 ID**를 설정합니다. **수준**을 **기본**에서 **모든**으로 설정/해제하고 이전에 만든 프로비저닝 프로필에 **프로필 프로비전**합니다.

	XCode에서 만든 새 프로비전 프로필이 보이지 않으면 메뉴 모음에서 **XCode**를 클릭하여 서명 idenity용 프로필을 새로 고친 다음 **기본 설정**, **계정** 탭, **자세히 보기** 단추를 클릭하고 서명 idenity 단추를 클릭하며 오른쪽 아래 모서리에서 새로 고침 단추를 클릭합니다.

   	![][9]

4. [모바일 서비스 iOS SDK]의 **버전 1.2.4**를 다운로드하고 파일의 압축을 풉니다. XCode에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **파일 추가** 옵션을 클릭하여 **WindowsAzureMessaging.framework** 폴더를 XCode 프로젝트에 추가합니다. **필요한 경우 항목 복사**를 선택한 다음 **추가**를 클릭합니다.

   	![][10]

5. AppDelegate.h 파일을 열고 import 지시문을 추가합니다.

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6. AppDelegate.m 파일은 iOS 버전에 기반하여 `didFinishLaunchingWithOptions` 메서드에 다음 코드를 추가합니다. 이 코드는 APNS로 장치 핸들을 등록합니다.

	iOS 8의 경우

	 	UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
												UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

    	[[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    	[[UIApplication sharedApplication] registerForRemoteNotifications];

	iOS 8 이전 버전의 경우

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];


7. 동일한 파일에서 다음 메서드를 추가하고 문자열 리터럴 자리 표시자를 *허브 이름* 및 앞에서 설명한 *DefaultListenSharedAccessSignature*으로 바꿉니다. 이 코드가 알림 허브에 장치 토큰을 제공하므로 알림 허브가 알림을 보낼 수 있습니다.

	    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<Enter your listen connection string>"
										notificationHubPath:@"<Enter your hub name>"];

		    [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
		        if (error != nil) {
		            NSLog(@"Error registering for notifications: %@", error);
		        }
				else {
				    [self MessageBox:@"Registration Status" message:@"Registered"];
				}
	    	}];
		}

		-(void)MessageBox:(NSString *)title message:(NSString *)messageText
		{
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
				cancelButtonTitle:@"OK" otherButtonTitles: nil];
			[alert show];
		}


8. 같은 파일에서 앱이 활성 상태일 때 알림이 수신되는 경우 **UIAlert**를 표시하려면 다음 메서드를 추가합니다.


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
		}

8. 실패하지 않았음을 확인하기 위해 장치에서 앱을 빌드 및 실행합니다.

## 알림을 보내는 방법


아래 화면과 같이 알림 허브의 디버그 탭을 사용하여 Azure 포털에서 알림을 보내 앱의 알림 수신을 테스트할 수 있습니다.

![][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

![][31]

1. XCode에서 Main.storyboard를 열고 개체 라이브러리에서 다음 UI 구성 요소를 추가하여 사용자가 앱에서 푸시 알림을 보낼 수 있습니다.

	- 레이블 텍스트가 없는 레이블 알림을 보내는 오류 보고에 사용됩니다. **줄** 속성은 **0**으로 설정해야 하므로 오른쪽 및 왼쪽 여백과 보기의 위쪽에 크기가 자동으로 제한됩니다.
	- **자리 표시자** 텍스트를 사용한 텍스트 필드가 **알림 메시지 입력**으로 설정됩니다. 아래와 같이 레이블 바로 아래에 있는 필드를 제한합니다. 뷰 컨트롤러를 콘센트 대리자로 설정합니다.
	- **알림 보내기**라는 단추는 텍스트 필드의 바로 아래 및 가로 가운데로 제한됩니다.

	뷰는 다음과 같이 표시되어야 합니다.

	![][32]


2. ViewController.h 파일을 열고 다음의 `#import` 및 `#define` 문을 추가합니다. 자리 표시자 문자열 리터럴을 실제 *DefaultFullSharedAccessSignature* 연결 문자열과 *허브 이름*으로 대체합니다.


		#import <CommonCrypto/CommonHMAC.h>

		#define API_VERSION @"?api-version=2015-01"
		#define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"
		#define HUBNAME @"<Enter the name of your hub>"


3. 보기에 연결된 레이블 및 텍스트 필드용 출선을 추가하고 `interface` 정의를 업데이트하여 `UITextFieldDelegate` 및 `NSXMLParserDelegate`를 지원합니다. REST API 호출 및 응답 구문 분석을 지원할 수 있도록 아래에 표시된 3개의 속성 선언을 추가합니다.

	ViewController.h 파일은 다음과 같이 표시 되어야 합니다.

		#import <UIKit/UIKit.h>
		#import <CommonCrypto/CommonHMAC.h>

		#define API_VERSION @"?api-version=2015-01"
		#define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"
		#define HUBNAME @"<Enter the name of your hub>"

		@interface ViewController : UIViewController <UITextFieldDelegate, NSXMLParserDelegate>
		{
			NSXMLParser *xmlParser;
		}

		// Make sure these outlets are connected to your UI by ctrl+dragging.
		@property (weak, nonatomic) IBOutlet UITextField *notificationMessage;
		@property (weak, nonatomic) IBOutlet UILabel *sendResults;

		@property (copy, nonatomic) NSString *statusResult;
		@property (copy, nonatomic) NSString *currentElement;

		@end


4. ViewController.m를 열고 다음 코드를 *DefaultFullSharedAccessSignature* 연결 문자열을 구문 분석하도록 추가합니다. [REST API 참조](http://msdn.microsoft.com/library/azure/dn495627.aspx)에서 설명한 것처럼 이 구문 분석된 정보는 *권한 부여* 요청 헤더를 위한 SAS 토큰을 생성하는데 사용됩니다.

		NSString *HubEndpoint;
		NSString *HubSasKeyName;
		NSString *HubSasKeyValue;

		-(void)ParseConnectionString
		{
			NSArray *parts = [HUBFULLACCESS componentsSeparatedByString:@";"];
			NSString *part;

			if ([parts count] != 3)
			{
				NSException* parseException = [NSException exceptionWithName:@"ConnectionStringParseException"
					reason:@"Invalid full shared access connection string" userInfo:nil];

				@throw parseException;
			}

			for (part in parts)
			{
				if ([part hasPrefix:@"Endpoint"])
				{
					HubEndpoint = [NSString stringWithFormat:@"https%@",[part substringFromIndex:11]];
				}
				else if ([part hasPrefix:@"SharedAccessKeyName"])
				{
					HubSasKeyName = [part substringFromIndex:20];
				}
				else if ([part hasPrefix:@"SharedAccessKey"])
				{
					HubSasKeyValue = [part substringFromIndex:16];
				}
			}
		}

5. ViewController.m에서 `viewDidLoad` 메서드를 업데이트하여 뷰가 로드할 때 연결 문자열을 구문 분석합니다. 또한 아래에 표시된 유틸리티 메서드를 추가합니다.


		- (void)viewDidLoad
		{
			[super viewDidLoad];
			[self ParseConnectionString];
		}

		-(NSString *)CF_URLEncodedString:(NSString *)inputString
		{
		   return (__bridge NSString *)CFURLCreateStringByAddingPercentEscapes(NULL, (CFStringRef)inputString,
				NULL, (CFStringRef)@"!*'();:@&=+$,/?%#[]", kCFStringEncodingUTF8);
		}

		-(void)MessageBox:(NSString *)title message:(NSString *)messageText
		{
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
				cancelButtonTitle:@"OK" otherButtonTitles: nil];
			[alert show];
		}





6. ViewController.m에서는 [REST API 참조](http://msdn.microsoft.com/library/azure/dn495627.aspx)에서 설명한 것처럼 *권한 부여* 헤더에서 제공될 SAS 권한 부여 토큰을 생성하는 다음 코드를 추가합니다.

		-(NSString*) generateSasToken:(NSString*)uri
		{
			NSString *targetUri;
			NSString* utf8LowercasedUri = NULL;
			NSString *signature = NULL;
			NSString *token = NULL;

			@try
			{
				// Add expiration
				uri = [uri lowercaseString];
				utf8LowercasedUri = [self CF_URLEncodedString:uri];
				targetUri = [utf8LowercasedUri lowercaseString];
				NSTimeInterval expiresOnDate = [[NSDate date] timeIntervalSince1970];
				int expiresInMins = 60; // 1 hour
				expiresOnDate += expiresInMins * 60;
				UInt64 expires = trunc(expiresOnDate);
				NSString* toSign = [NSString stringWithFormat:@"%@\n%qu", targetUri, expires];

				// Get an hmac_sha1 Mac instance and initialize with the signing key
				const char *cKey  = [HubSasKeyValue cStringUsingEncoding:NSUTF8StringEncoding];
				const char *cData = [toSign cStringUsingEncoding:NSUTF8StringEncoding];
				unsigned char cHMAC[CC_SHA256_DIGEST_LENGTH];
				CCHmac(kCCHmacAlgSHA256, cKey, strlen(cKey), cData, strlen(cData), cHMAC);
				NSData *rawHmac = [[NSData alloc] initWithBytes:cHMAC length:sizeof(cHMAC)];
				signature = [self CF_URLEncodedString:[rawHmac base64EncodedStringWithOptions:0]];

				// construct authorization token string
				token = [NSString stringWithFormat:@"SharedAccessSignature sr=%@&sig=%@&se=%qu&skn=%@",
					targetUri, signature, expires, HubSasKeyName];
			}
			@catch (NSException *exception)
			{
				[self MessageBox:@"Exception Generating SaS Token" message:[exception reason]];
			}
			@finally
			{
				if (utf8LowercasedUri != NULL)
					CFRelease((CFStringRef)utf8LowercasedUri);
				if (signature != NULL)
				CFRelease((CFStringRef)signature);
			}

			return token;
		}


7. **알림 보내기** 단추에서 ViewController.m으로 **Ctrl + 드래그**하여 다음의 코드를 사용하는 REST API 호출을 실행하는 **Touch 축소** 이벤트에 대한 작업을 추가합니다.

		- (IBAction)SendNotificationMessage:(id)sender
		{
			self.sendResults.text = @"";
			[self SendNotificationRESTAPI];
		}

		- (void)SendNotificationRESTAPI
		{
		    NSURLSession* session = [NSURLSession
                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                             delegate:nil delegateQueue:nil];

			// Apple Notification format of the notification message
		    NSString *json = [NSString stringWithFormat:@"{"aps":{"alert":"%@"}}",
								self.notificationMessage.text];

			// Construct the messages REST endpoint
			NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
												HUBNAME, API_VERSION]];

			// Generated the token to be used in the authorization header.
			NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

			//Create the request to add the APNS notification message to the hub
			NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
			[request setHTTPMethod:@"POST"];
			[request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

			// Signify apple notification format
			[request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];

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
		        	xmlParser = [[NSXMLParser alloc] initWithData:data];
		        	[xmlParser setDelegate:self];
		       		[xmlParser parse];
		    	}
		    }];
		    [dataTask resume];
		}


8. ViewController.m에서 텍스트 필드에 대한 키보드 닫기를 지원하려면 다음 대리자 메서드를 추가합니다. 인터페이스 디자이너에서 텍스트 필드에서 뷰 컨트롤러 아이콘으로 **Ctrl + 드래그**하여 뷰 컨트롤러를 콘센트 대리자로 설정합니다.

		//===[ Implement UITextFieldDelegate methods ]===

		-(BOOL)textFieldShouldReturn:(UITextField *)textField
		{
			[textField resignFirstResponder];
			return YES;
		}


9. ViewController.m에서 `NSXMLParser`을 사용하여 응답 구문 분석을 지원하려면 다음 대리자 메서드를 추가합니다.

		//===[ Implement NSXMLParserDelegate methods ]===

		-(void)parserDidStartDocument:(NSXMLParser *)parser
		{
		    self.statusResult = @"";
		}

		-(void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName
			namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
			attributes:(NSDictionary *)attributeDict
		{
		    NSString * element = [elementName lowercaseString];
		    NSLog(@"*** New element parsed : %@ ***",element);

		    if ([element isEqualToString:@"code"] | [element isEqualToString:@"detail"])
		    {
		        self.currentElement = element;
		    }
		}

		-(void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)parsedString
		{
		    self.statusResult = [self.statusResult stringByAppendingString:
		        [NSString stringWithFormat:@"%@ : %@\n", self.currentElement, parsedString]];
		}

		-(void)parserDidEndDocument:(NSXMLParser *)parser
		{
			// Set the status label text on the UI thread
			dispatch_async(dispatch_get_main_queue(),
			^{
				[self.sendResults setText:self.statusResult];
			});
		}



10. 프로젝트를 빌드하고 오류가 없는지 확인 합니다.



Apple [로컬 및 푸시 알림 프로그래밍 가이드]에서 가능한 모든 알림 페이로드를 찾아볼 수 있습니다.



##앱 테스트

iOS에서 푸시 알림을 테스트하려면 장치에 앱을 배포해야 합니다. iOS 시뮬레이터를 사용하여 Apple 푸시 알림을 보낼 수 없습니다.

1. 앱을 실행하고 등록이 성공했는지 확인한 다음 **확인**을 누릅니다.

	![][33]

2. 알림 메시지를 입력하려면 텍스트 필드에 터치합니다. 그런 다음 키보드에서 **보내기** 단추 또는 보기에서 **알림 보내기** 단추를 눌러서 알림 메시지를 보냅니다.

	![][34]

3. 알림을 수신하도록 등록된 모든 장치에 알림이 전송됩니다.

	![][35]

이 자습서를 개선하기 위해 모든 문제 또는 판독기에 대한 의견 및 권장 사항이 있다면 아래 Disqus 섹션에 의견을 남겨주십시오.


##다음 단계

이 간단한 예제에서는 모든 iOS 장치로 알림을 브로드캐스트합니다. 특정 사용자에게만 알림을 보내려면 [알림 허브를 사용하여 사용자에게 알림 푸시] 자습서를 참조하세요. 사용자를 관심 그룹별로 분할하려면 [알림 허브를 사용하여 뉴스 속보 보내기]를 참조하세요. [알림 허브 지침]에서 알림 허브 사용 방법을 자세히 알아보세요.



<!-- Images. -->

[1]: ./media/notification-hubs-ios-get-started/notification-hubs-export-cert-p12.png
[2]: ./media/notification-hubs-ios-get-started/notification-hubs-create-from-portal.png
[3]: ./media/notification-hubs-ios-get-started/notification-hubs-create-from-portal2.png
[4]: ./media/notification-hubs-ios-get-started/notification-hubs-select-from-portal.png
[5]: ./media/notification-hubs-ios-get-started/notification-hubs-select-from-portal2.png
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-connection-strings.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-debug-hub-ios.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[34]: ./media/notification-hubs-ios-get-started/notification-hubs-test2.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[모바일 서비스 iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Azure 관리 포털]: https://manage.windowsazure.com/
[알림 허브 지침]: http://msdn.microsoft.com/library/jj927170.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[알림 허브를 사용하여 사용자에게 알림 푸시]: notification-hubs-aspnet-backend-ios-notify-users.md
[알림 허브를 사용하여 뉴스 속보 보내기]: notification-hubs-ios-send-breaking-news.md

[로컬 및 푸시 알림 프로그래밍 가이드]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
 

<!---HONumber=July15_HO4-->