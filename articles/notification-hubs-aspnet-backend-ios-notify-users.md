<properties title="Azure Notification Hubs Notify Users" pageTitle="Azure Notification Hubs Notify Users" metaKeywords="Azure push notifications, Azure notification hubs" description="Learn how to send secure push notifications in Azure. Code samples written in Objective-C using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="sethm"></tags>

# Azure 알림 허브 사용자에게 알림

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/ko--kr/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows 범용">Windows 범용</a><a href="/ko--kr/documentation/articles/notification-hubs-/" title="iOS" class="current">iOS</a>
        <a href="/ko--kr/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

Azure의 푸시 알림 지원을 통해 사용하기 쉬운 다중 플랫폼 및 규모 확장 푸시 인프라에 액세스할 수 있어, 모바일 플랫폼용 소비자 응용 프로그램 및 엔터프라이즈 응용 프로그램 모두에 대한 푸시 알림을 매우 간단하게 구현할 수 있습니다. 이 자습서에서는 Azure 알림 허브를 사용하여 특정 장치에서 특정 앱 사용자에게 푸시 알림을 보내는 방법을 보여 줍니다. 지침 항목 [앱 백 엔드에서 등록][앱 백 엔드에서 등록]에 나와 있는 대로 ASP.NET WebAPI 백 엔드는 클라이언트를 인증하고 알림을 생성하는 데 사용됩니다. 이 자습서는 **알림 허브 시작** 자습서에서 만든 알림 허브를 기반으로 합니다.

이 자습서는 **보안 푸시** 자습서의 필수 조건이기도 합니다. 이 **사용자에게 알림** 자습서의 단계를 완료해야 푸시 알림을 안전하게 보내도록 **사용자에게 알림** 코드를 수정하는 방법을 보여 주는 **보안 푸시** 자습서를 진행할 수 있습니다.

> [AZURE.NOTE] 이 자습서에서는 [알림 허브 시작(iOS)][알림 허브 시작(iOS)]에 설명된 대로 알림 허브를 만들고 구성했다고 가정합니다.

## 알림 허브 만들기 및 구성

이 자습서를 시작하기 전에 iOS 프로비저닝 프로필과 개발 푸시 인증서를 만들고 나서 Azure 알림 허브를 만들고 해당 응용 프로그램에 연결해야 합니다. [알림 허브 시작(iOS)][알림 허브 시작(iOS)], 특히 섹션 1-5의 단계를 수행하세요.

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers][notification-hubs-aspnet-backend-notifyusers]]

## iOS 앱 수정

1.  [알림 허브 시작(iOS)][알림 허브 시작(iOS)], 섹션 1-5의 단계를 수행하여 알림 허브에서 푸시 알림을 받을 수 있는 단일 페이지 보기 앱을 만듭니다.

> [AZURE.NOTE] 이 섹션에서는 빈 조직 이름을 사용하여 프로젝트를 구성했다고 가정합니다. 이렇게 구성하지 않았으면 아래 코드의 모든 클래스 이름 앞에 조직 이름을 추가해야 합니다.

1.  Main.storyboard의 개체 라이브러리에서 다음 구성 요소를 추가합니다.

    -   자리 표시자 텍스트 **Username**이 포함된 UITextField
    -   자리 표시자 텍스트 **Password**가 포함된 UITextField, 오른쪽 창의 TextField 속성 그룹에서 **보안 텍스트 입력** 옵션 선택
    -   **1. 로그인** 레이블이 지정된 UIButton, 오른쪽 창의 접근성 속성 그룹에서 **사용** 옵션 선택 취소
    -   **2. 푸시 보내기** 레이블이 지정된 UIButton, 오른쪽 창의 접근성 속성 그룹에서 **사용** 옵션 선택 취소

    스토리보드는 다음과 같이 표시됩니다.

    ![][]

2.  ViewController.m의 인터페이스 부분에서 UITextField 및 UIButton에 대한 콘센트를 만듭니다.

        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

3.  ViewController.m 구현에서 두 단추에 대한 작업을 만듭니다.
     - (IBAction)LogInAction:(id)sender { }
     - (IBAction)SendPushAction:(id)sender { }

4.  먼저 백 엔드와 상호 연결되는 RegisterClient 클래스를 만듭니다. NSObject에서 RegisterClient inheriting이라는 Objective-C 클래스를 만듭니다. 그리고 나서 RegisterClient.h 인터페이스 섹션에 다음 코드를 추가합니다.

        @property (strong, nonatomic) NSString* authenticationHeader;
        -(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;

5.  RegisterClient.m에서 다음 인터페이스 섹션을 추가합니다.

        @interface RegisterClient ()

        @property (strong, nonatomic) NSURLSession* session;
        -(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
        -(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
        -(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSString*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;

        @end

6.  그리고 나서 구현 섹션에서 다음 코드를 추가하고 *{backend endpoint}* 자리 표시자를 이전 섹션에서 앱 백 엔드를 배포하는 데 사용한 끝점으로 대체합니다.

        NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";
        NSString *const BackEndEndpoint = @"{backend endpoint}/api/register";

        - (instancetype)init
        {
            self = [super init];
            if (self) {
                NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
                _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
            }
            return self;
        }

        -(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;
        {
            [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
        }

        -(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
        {
            NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

            NSString *deviceTokenString = [[token description] stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
            deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""] uppercaseString];

            [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString completion:^(NSString* registrationId, NSError *error) {
                NSLog(@"regId: %@", registrationId);
                if (error) {
                    completion(error);
                    return;
                }

                [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
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
                        NSLog(@"Registration error with response status: %ld", (long) httpResponse.statusCode);

                        completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode userInfo:nil]);
                    }

                }];
            }];
        }

        -(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSData*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;
        {
            NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token, @"Tags": [tags allObjects]};
            NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration options:NSJSONWritingPrettyPrinted error:nil];

            NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData encoding:NSUTF8StringEncoding]);

            NSString* endpoint = [NSString stringWithFormat:@"%@/%@", BackEndEndpoint, registrationId];
            NSURL* requestURL = [NSURL URLWithString:endpoint];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"PUT"];
            [request setHTTPBody:jsonData];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
            [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
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

        -(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
        {
            NSString* registrationId = [[NSUserDefaults standardUserDefaults] objectForKey:RegistrationIdLocalStorageKey];

            if (registrationId)
            {
                completion(registrationId, nil);
                return;
            }

            // request new one & save
            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@?handle=%@", BackEndEndpoint, token]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"POST"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSString* registrationId = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];

                    // remove quotes
                    registrationId = [registrationId substringWithRange: NSMakeRange(1, [registrationId length]-2)];

                    [[NSUserDefaults standardUserDefaults] setObject:registrationId forKey:RegistrationIdLocalStorageKey];
                    [[NSUserDefaults standardUserDefaults] synchronize];

                    completion(registrationId, nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

    위의 코드는 NSURLSession를 사용하여 앱 백 엔드에 대한 REST 호출을 수행하고 NSUserDefaults를 사용하여 알림 허브에서 반환된 registrationId를 로컬로 저장하는, 지침 문서 [앱 백 엔드에서 등록][앱 백 엔드에서 등록]에서 설명된 논리를 구현합니다.

    이 클래스가 제대로 작동하려면 **authorizationHeader** 속성을 설정해야 합니다. 이 속성은 로그인 후에 **ViewController** 클래스에 의해 설정됩니다.

7.  ViewController.h에서 장치 토큰을 포함할 다음 속성을 추가합니다.

        @property (strong, nonatomic) NSData* deviceToken;

8.  ViewController.m에서 ViewController 클래스를 UITextFieldDelegate로 설정하고, RegisterClient 인스턴스를 참조하는 속성을 추가하고, Private 메서드 선언을 추가합니다. 인터페이스 섹션은 다음과 같아야 합니다.

        @interface ViewController () <UITextFieldDelegate>
        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

        @property (strong, nonatomic) RegisterClient* registerClient;

        -(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;
        @end

    Private 메서드는 앱 백 엔드를 사용하여 기본 인증을 수행하기 위한 권한 부여 헤더를 만드는 데 사용됩니다.

> [AZURE.NOTE] 이는 보안 인증 체계가 아닙니다. **createAndSetAuthenticationHeaderWithUsername:AndPassword:** 구현을 레지스터 클라이언트 클래스(예: OAuth, Active Directory)에서 사용할 인증 토큰을 생성하는 특정 인증 메커니즘으로 대체해야 합니다.

1.  그리고 나서 ViewController.m의 구현 섹션에서 다음 코드를 추가합니다.

        -(void) setDeviceToken: (NSData*) deviceToken
        {
            _deviceToken = deviceToken;
            self.LogInButton.enabled = YES;
        }

        -(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;
        {
            NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

            NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

            self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData encoding:NSUTF8StringEncoding];
        }

        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }

    장치 토큰 설정이 어떻게 로그인 단추를 활성화하는지에 주목하세요. 이는 로그인 작업 중에 보기 컨트롤러가 푸시 알림을 앱 백 엔드에 등록하기 때문입니다. 따라서 장치 토큰이 제대로 설정되기 전에 사용자가 로그인 단추를 누르지 않게 해야 합니다. 앱에서는 토큰 설정 전에 단추 누름이 발생하면 푸시 등록에서 로그인을 분리할 수 있습니다.

2.  ViewController.m에서 백 엔드 끝점에 대한 상수를 추가하고 UIButton에 대한 작업 메서드 구현을 입력합니다. 자리 표시자를 이전 섹션에서 사용한 백 엔드 끝점으로 바꿔야 합니다.

        - (IBAction)LogInAction:(id)sender {
            // create authentication header and set it in register client
            NSString* username = self.UsernameField.text;
            NSString* password = self.PasswordField.text;

            [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

            __weak ViewController* selfie = self;
            [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil andCompletion: ^(NSError* error) {
                if (!error) {
                    dispatch_async(dispatch_get_main_queue(), ^{
                        selfie.SendPushButton.enabled = YES;

                        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Success" message:
                                              @"Registered successfully!" delegate:nil cancelButtonTitle:
                                              @"OK" otherButtonTitles:nil, nil];
                        [alert show];
                    });
                }
            }];
        }

        NSString *const SendNotificationEndpoint = @"{backend endpoint}/api/notifications";

        - (IBAction)SendPushAction:(id)sender {
            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];


            NSURL* requestURL = [NSURL URLWithString:SendNotificationEndpoint];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"POST"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.registerClient.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || httpResponse.statusCode != 200)
                {
                    NSLog(@"Error status: %d, request: %@", httpResponse.statusCode, error);
                }
            }];
            [dataTask resume];
        }

3.  마지막으로 **ViewDidLoad**에서 다음 코드를 추가하여 RegisterClient 인스턴스를 인스턴스화 하고 텍스트 필드에 대한 대리자를 설정합니다.

        self.UsernameField.delegate = self;
        self.PasswordField.delegate = self;
        self.registerClient = [[RegisterClient alloc] init];

4.  이제 **AppDelegate.m**에서 **application:didRegisterForPushNotificationWithDeviceToken:** 메서드의 모든 콘텐츠를 제거하고 다음으로 바꿉니다.

        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;

    이렇게 하면 보기 컨트롤러에 APN에서 검색된 최신 장치 토큰이 포함됩니다.

5.  계속 **AppDelegate.m**에서 다음 메서드가 있는지 확인합니다.

        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                  [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
                                  @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

## 응용 프로그램 실행

응용 프로그램을 실행하려면 다음을 수행합니다.

1.  **AppBackend**가 Azure에 배포되었는지 확인합니다. Visual Studio를 사용할 경우 **AppBackend** Web API 응용 프로그램을 실행합니다. ASP.NET 웹 페이지가 표시됩니다.

2.  XCode에서는 실제 iOS 장치에서 앱을 실행합니다(푸시 알림은 시뮬레이터에서 작동하지 않음).

3.  iOS 앱 UI에서 사용자 이름과 암호를 입력합니다. 이는 임의 문자열일 수 있지만 같은 값이어야 합니다.

4.  iOS 앱 UI에서 **로그인**을 클릭합니다. 그리고 나서 **푸시 보내기**를 클릭합니다.

  [Windows 범용]: /ko--kr/documentation/articles/notification-hubs-windows-dotnet-notify-users/ "Windows 범용"
  [iOS]: /ko--kr/documentation/articles/notification-hubs-/ "iOS"
  [Android]: /ko--kr/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/ "Android"
  [앱 백 엔드에서 등록]: http://msdn.microsoft.com/ko--kr/library/dn743807.aspx
  [알림 허브 시작(iOS)]: http://azure.microsoft.com/ko--kr/documentation/articles/notification-hubs-ios-get-started/
  [notification-hubs-aspnet-backend-notifyusers]: ../includes/notification-hubs-aspnet-backend-notifyusers.md
  []: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users1.png
