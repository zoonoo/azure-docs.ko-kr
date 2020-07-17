---
title: Azure Notification Hubs 및 iOS SDK 버전 3.0.0 미리 보기 1을 사용하여 iOS에 푸시 알림 보내기
description: 이 자습서에서는 Azure Notification Hubs 및 Apple Push Notification 서비스를 사용하여 iOS 디바이스에 푸시 알림을 보내는 방법을 알아봅니다.
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: 4f34b3e7567a65dca45fd29b4e8aa9b78a6ced76
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2020
ms.locfileid: "85127554"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs-version-300-preview1"></a>자습서: Azure Notification Hubs(버전 3.0.0-preview1)를 사용하여 iOS 앱에 푸시 알림 보내기

이 자습서에서는 Azure Notification Hubs SDK 버전 2.0.4를 사용하여 Azure Notification Hubs를 통해 iOS 애플리케이션에 푸시 알림을 보내는 방법을 보여줍니다.

이 자습서에서 다루는 단계는 다음과 같습니다.

- 샘플 iOS 앱을 만듭니다.
- iOS 앱을 Azure Notification Hubs에 연결합니다.
- 테스트 푸시 알림을 보냅니다.
- 앱이 알림을 수신하는지 확인합니다.

[GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/v3-preview2/Samples)에서 이 자습서의 전체 코드를 다운로드할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

- 키 집합에 설치된 유효한 개발자 인증서와 함께 [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)를 실행하는 Mac.
- iOS 버전 10 이상을 실행하는 iPhone 또는 iPad.
- [Apple Portal](https://developer.apple.com/)에 등록되고 인증서와 연결된 물리적 디바이스입니다.

계속 진행하기 전에 알림 허브에서 푸시 자격 증명을 설정 및 구성하기 위한 [iOS 앱용 Azure Notification Hubs](ios-sdk-get-started.md) 시작에 대한 이전 자습서를 참조하세요. iOS 개발에 대한 사전 경험이 없더라도 이러한 단계를 수행할 수 있습니다.

> [!NOTE]
> 푸시 알림에 대한 구성 요구 사항 때문에 iOS 에뮬레이터 대신 실제 iOS 디바이스(iPhone 또는 iPad)에서 푸시 알림을 배포 및 테스트해야 합니다.

## <a name="connect-your-ios-app-to-notification-hubs"></a>Notification Hubs에 iOS 앱 연결

1. Xcode에서 새 iOS 프로젝트를 만들고 **애플리케이션 단일 보기** 템플릿을 선택합니다.

   :::image type="content" source="media/ios-sdk/image1.png" alt-text="템플릿 선택":::

2. 새 프로젝트에 대한 옵션을 설정하는 경우 Apple Developer 포털에서 번들 식별자를 설정할 때 사용한 것과 동일한  **제품 이름** 및 **조직 식별자**를 사용해야 합니다.

3. 프로젝트 탐색기의 **대상**에서 프로젝트 이름을 선택한 다음, **서명 및 기능** 탭을 선택합니다. Apple Developer 계정에 적합한 **팀**을 선택합니다. XCode는 번들 식별자에 따라 이전에 만든 프로비전 프로필을 자동으로 끌어옵니다.

   Xcode에서 만든 새 프로비전 프로필이 보이지 않으면 서명 ID에 대한 프로필을 새로 고칩니다. 메뉴 모음에서 **Xcode**,  **기본 설정**, **계정** 탭, **세부 정보 보기** 단추, 서명 ID를 차례로 클릭한 다음, 오른쪽 아래 모서리에 있는 새로 고침 단추를 클릭합니다.

   :::image type="content" source="media/ios-sdk/image2.png" alt-text="세부 정보 보기":::

4.  **서명 및 기능** 탭에서 **+ 기능**을 선택합니다.  **푸시 알림**을 두 번 클릭하여 활성화합니다.

   :::image type="content" source="media/ios-sdk/image3.png" alt-text="기능":::

5. Azure Notification Hubs SDK 모듈을 추가합니다.

    [Cocoapods](https://cocoapods.org/)를 사용하거나 프로젝트에 이진 파일을 수동으로 추가하여 앱에 Azure Notification Hubs SDK를 통합할 수 있습니다.

   - Cocoapods를 통한 통합: podfile에 다음 종속성을 추가하여 Azure Notification Hubs SDK를 앱에 포함합니다.

      ```ruby
      pod 'AzureNotificationHubs-iOS'
      ```

      - pod install을 실행하여 새로 정의된 Pod를 설치하고 .xcworkspace를 엽니다.

         pod install을 실행하는 동안 **AzureNotificationHubs-iOS에 대한 사양을 찾을 수 없음**과 같은 오류가 표시되면 `pod repo update`를 실행하여 Cocoapods 리포지토리에서 최신 Pod를 가져온 다음, pod install을 실행하세요.

   - 프로젝트에 이진 파일을 복사하여 통합:

      다음과 같이 프로젝트에 이진 파일을 복사하여 통합할 수 있습니다.

        - Zip 파일로 제공된 [Azure Notification Hubs SDK](https://github.com/Azure/azure-notificationhubs-iOS/releases/) 프레임워크를 다운로드하고 압축을 풉니다.

        - Xcode에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **파일 추가** 옵션을 클릭하여 **WindowsAzureMessaging.framework** 폴더를 Xcode 프로젝트에 추가합니다.  **옵션**을 선택하고 **필요한 경우 항목 복사**가 선택되었는지 확인한 다음, **추가**를 클릭합니다.

          :::image type="content" source="media/ios-sdk/image4.png" alt-text="프레임워크 추가":::

6.  **Constants.h**라는 프로젝트에 새 헤더 파일을 추가합니다. 이렇게 하려면 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **새 파일...** 을 선택합니다. 그런 다음, **헤더 파일**을 선택합니다. 이 파일에는 알림 허브에 대한 상수가 들어 있습니다. 그리고 **다음**을 선택합니다. 파일 이름을 **Constants.h**로 지정합니다.

7. Constants.h 파일에 다음 코드를 추가합니다.

   ```objc
   #ifndef Constants_h
   #define Constants_h
   extern NSString* const NHInfoConnectionString;
   extern NSString* const NHInfoHubName;
   extern NSString* const NHUserDefaultTags;
   #endif /* Constants_h */
   ```

8. Constants.h에 대한 구현 파일을 추가합니다. 이렇게 하려면 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **새 파일...** 을 선택합니다.  **Objective-C 파일**을 선택하고  **다음**을 선택합니다. 파일 이름을 **Constants.m**으로 지정합니다.

   :::image type="content" source="media/ios-sdk/image5.png" alt-text="구현 파일 추가":::

9.  **Constants.m** 파일을 열고 콘텐츠를 다음 코드로 바꿉니다. 문자열 리터럴 자리 표시자  `NotificationHubConnectionString` 및 `NotificationHubConnectionString`을 각각 이전에 포털에서 가져온 허브 이름과 **DefaultListenSharedAccessSignature**로 바꿉니다.

   ```objc
   #import <Foundation/Foundation.h>
   #import "Constants.h"

   NSString* const NHInfoConnectionString = @"NotificationHubConnectionString";
   NSString* const NHInfoHubName = @"NotificationHubName";NSString* const NHUserDefaultTags = @"notification_tags";
   ```

10. 프로젝트 파일 **AppDelegate**에서 다음 `import` 문을 추가합니다.

    ```objc
    #import "Constants.h"
    ```

11. 동일한 **AppDelegate.m** 파일에서 `didFinishLaunchingWithOptions` 뒤의 모든 코드를 다음 코드로 바꿉니다.

    ```objc
    // Tells the delegate that the app successfully registered with Apple Push Notification service (APNs).


    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    NSString *hubName = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoHubName];
    NSString *connectionString = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoConnectionString];
    [MSNotificationHub initWithConnectionString:connectionString withHubName:hubName];


    NSMutableSet *tags = [[NSMutableSet alloc] init];

    // Load and parse stored tags
    NSString *unparsedTags = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
    if (unparsedTags.length > 0) {
        NSArray *tagsArray = [unparsedTags componentsSeparatedByString: @","];

        [MSNotificationHub addTags:tagsArray];
    }

    }
    - (void)showAlert:(NSString *)message withTitle:(NSString *)title {
    if (title == nil) {
        title = @"Alert";
    }
    UIAlertController *alert = [UIAlertController alertControllerWithTitle:title message:message preferredStyle:UIAlertControllerStyleAlert];
    [alert addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:nil]];
    [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:alert animated:YES completion:nil];
    }

    - (void)showNotification:(NSDictionary *)userInfo {
    [self logNotificationDetails:userInfo];

    NotificationDetailViewController *notificationDetail = [[NotificationDetailViewController alloc] initWithUserInfo:userInfo];
    [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:notificationDetail animated:YES completion:nil];
    }

    @end
    ```

    이 코드는 **Constants.h**에서 지정한 연결 정보를 사용하여 알림 허브에 연결합니다. 그런 다음, 허브에서 알림을 보낼 수 있도록 해당 알림 허브에 디바이스 토큰을 제공합니다.

### <a name="create-notificationdetailviewcontroller-header-file"></a>NotificationDetailViewController 헤더 파일 만들기

1. 이전 지침과 마찬가지로, **NotificationDetailViewController.h**라는 또 다른 헤더 파일을 추가합니다. 새 헤더 파일의 콘텐츠를 다음 코드로 바꿉니다.

   ```objc
   #import <UIKit/UIKit.h>

   NS_ASSUME_NONNULL_BEGIN

   @interface NotificationDetailViewController : UIViewController

   @property (strong, nonatomic) IBOutlet UILabel *titleLabel;
   @property (strong, nonatomic) IBOutlet UILabel *bodyLabel;
   @property (strong, nonatomic) IBOutlet UIButton *dismissButton;

   @property (strong, nonatomic) NSDictionary *userInfo;

   - (id)initWithUserInfo:(NSDictionary *)userInfo;

   @end

   NS_ASSUME_NONNULL_END
   ```

2.  **NotificationDetailViewController.m** 구현 파일을 추가합니다. 파일의 콘텐츠를 UIViewController 메서드를 구현하는 다음 코드로 바꿉니다.

   ```objc
   #import "NotificationDetailViewController.h"

   @interface NotificationDetailViewController ()

   @end

   @implementation NotificationDetailViewController

   - (id)initWithUserInfo:(NSDictionary *)userInfo {
    self = [super initWithNibName:@"NotificationDetail" bundle:nil];
    if (self) {
        _userInfo = userInfo;
    }
    return self;
   }

   - (void)viewDidLayoutSubviews {
    [self.titleLabel sizeToFit];
    [self.bodyLabel sizeToFit];
   }

   - (void)viewDidLoad {
    [super viewDidLoad];

    NSString *title = nil;
    NSString *body = nil;

    NSDictionary *aps = [_userInfo valueForKey:@"aps"];
    NSObject *alertObject = [aps valueForKey:@"alert"];
    if (alertObject != nil) {
        if ([alertObject isKindOfClass:[NSDictionary class]]) {
            NSDictionary *alertDict = (NSDictionary *)alertObject;
            title = [alertDict valueForKey:@"title"];
            body = [alertObject valueForKey:@"body"];
        } else if ([alertObject isKindOfClass:[NSString class]]) {
            body = (NSString *)alertObject;
        } else {
            NSLog(@"Unable to parse notification content. Unexpected format: %@", alertObject);
        }
    }

    if (title == nil) {
        title = @"<unset>";
    }

    if (body == nil) {
        body = @"<unset>";
    }

    self.titleLabel.text = title;
    self.bodyLabel.text = body;
   }

   - (IBAction)handleDismiss:(id)sender {
    [self dismissViewControllerAnimated:YES completion:nil];
   }

   @end
   ```

### <a name="viewcontroller"></a>ViewController

1. 프로젝트 파일 **ViewController.h**에서 다음 `import` 문을 추가합니다.

   ```objc
   #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
   #import <UserNotifications/UserNotifications.h>
   ```

2. 또한 **ViewController.h** 파일에서 `@interface` 선언 뒤에 다음 속성 선언을 추가합니다.

   ```objc
   @property (strong, nonatomic) IBOutlet UITextField *tagsTextField;
   ```

3. 프로젝트의 **ViewController.m** 구현 파일에서 파일의 콘텐츠를 다음 코드로 바꿉니다.

   ```objc
   #import "ViewController.h"
   #import "Constants.h"
   #import "AppDelegate.h"

   @interface ViewController ()

   @end

   @implementation ViewController

   // UIViewController methods

   - (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
    // Simple method to dismiss keyboard when user taps outside of the UITextField.
    [self.view endEditing:YES];
   }

   - (void)viewDidLoad {
    [super viewDidLoad];

    // Load raw tags text from storage and initialize the text field
    self.tagsTextField.text = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
   }

   - (IBAction)handleRegister:(id)sender {
    // Save raw tags text in storage
    [[NSUserDefaults standardUserDefaults] setValue:self.tagsTextField.text forKey:NHUserDefaultTags];

   // Delegate processing the register action to the app delegate.
   [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleRegister)];
   }

   - (IBAction)handleUnregister:(id)sender {
    [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleUnregister)];
   }

   @end
   ```

4. 오류가 없는지 확인하려면 디바이스에서 앱을 빌드하고 실행합니다.

## <a name="send-test-push-notifications"></a>테스트 푸시 알림 보내기

 [Azure Portal](https://portal.azure.com/)에서  **테스트 보내기**  옵션을 사용하여 앱에서 알림 수신을 테스트할 수 있습니다. 이렇게 하면 테스트 푸시 알림이 디바이스로 전송됩니다.

:::image type="content" source="media/ios-sdk/image6.png" alt-text="테스트 보내기":::

푸시 알림은 일반적으로 호환 라이브러리를 사용하는 Mobile Apps 또는 ASP.NET과 같은 백 엔드 서비스에서 전송됩니다. 백 엔드에서 라이브러리를 사용할 수 없는 경우 REST API를 직접 사용하여 알림 메시지를 보낼 수도 있습니다.

알림을 보내기 위해 검토할 수 있는 다른 자습서 목록은 다음과 같습니다.

- Azure Mobile Apps: Notification Hubs와 통합된 Mobile Apps 백 엔드에서 알림을 보내는 방법에 대한 예제는 [iOS 앱에 푸시 알림 추가](/azure/app-service-mobile/app-service-mobile-ios-get-started-push)를 참조하세요.
- ASP.NET: [Notification Hubs를 사용하여 사용자에게 푸시 알림 보내기](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)를 참조하세요.
- Azure Notification Hubs Java SDK: Java에서 알림을 보내는 방법은 [Java에서 Notification Hubs를 사용하는 방법](notification-hubs-java-push-notification-tutorial.md)을 참조하세요. 이는 Eclipse for Android Development에서 테스트되었습니다.
- PHP: [PHP에서 Notification Hubs를 사용하는 방법](notification-hubs-php-push-notification-tutorial.md)을 참조하세요.

## <a name="verify-that-your-app-receives-push-notifications"></a>앱에 푸시 알림이 수신되는지 확인

iOS에서 푸시 알림을 테스트하려면 실제 iOS 디바이스에 앱을 배포해야 합니다. iOS 시뮬레이터를 사용하여 Apple 푸시 알림을 보낼 수 없습니다.

1. 앱을 실행하고 등록이 성공했는지 확인한 다음, **확인**을 누릅니다.

   :::image type="content" source="media/ios-sdk/image7.png" alt-text="등록":::

2. 다음으로, 이전 섹션에 설명한 대로 [Azure Portal](https://portal.azure.com/)에서 테스트 푸시 알림을 보냅니다.

3. 지정된 알림 허브에서 보내는 알림을 수신하도록 등록된 모든 디바이스에 푸시 알림이 전송됩니다.

   :::image type="content" source="media/ios-sdk/image8.png" alt-text="테스트 보내기":::

## <a name="next-steps"></a>다음 단계

이 간단한 예제에서는 등록된 모든 iOS 디바이스에 푸시 알림을 브로드캐스트합니다. 특정 iOS 디바이스로 푸시 알림을 보내는 방법을 알아보려면 다음 자습서를 계속 진행합니다.

[자습서: 특정 디바이스에 푸시 알림](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

자세한 내용은 다음 문서를 참조하세요.

- [Azure Notification Hubs 개요](notification-hubs-push-notification-overview.md)
- [Notification Hubs REST API](/rest/api/notificationhubs/)
- [백 엔드 작업에 대한 Notification Hubs SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [GitHub의 Notification Hubs SDK](https://github.com/Azure/azure-notificationhubs)
- [애플리케이션 백 엔드에 등록](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [등록 관리](notification-hubs-push-notification-registration-management.md)
- [태그 사용](notification-hubs-tags-segment-push-message.md)
- [사용자 지정 템플릿 사용](notification-hubs-templates-cross-platform-push-messages.md)
- [공유 액세스 서명을 사용한 Service Bus 액세스 제어](/azure/service-bus-messaging/service-bus-sas)
- [프로그래밍 방식으로 SAS 토큰 생성](/rest/api/eventhub/generate-sas-token)
- [Apple 보안: 일반적인 암호화](https://developer.apple.com/security/)
- [UNIX Epoch 시간](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
