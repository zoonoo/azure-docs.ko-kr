---
title: Azure 알림 허브를 사용하는 Swift iOS 앱에 푸시 알림 보내기 | 마이크로 소프트 문서
description: Azure 알림 허브를 사용하는 Swift iOS 앱에 알림을 푸시하는 방법을 알아봅니다.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: femila
editor: jwargo
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: a721c519c7a836e20455c6f1887bcfa7b52951f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336630"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-api"></a>튜토리얼: 알림 허브 REST API를 사용하여 Swift iOS 앱에 푸시 알림 보내기

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

이 자습서에서는 Azure 알림 허브를 사용하여 [REST API를](/rest/api/notificationhubs/)사용하여 Swift 기반 iOS 응용 프로그램에 알림을 푸시합니다. 또한 [Apple 푸시 알림 서비스(APN)를](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)사용하여 푸시 알림을 받는 빈 iOS 앱을 만듭니다.

이 자습서에서는 다음 단계를 안내합니다.

> [!div class="checklist"]
> * 인증서 서명 요청 파일을 생성합니다.
> * 푸시 알림을 앱에 요청합니다.
> * 앱에 대한 프로비저닝 프로필을 만듭니다.
> * 알림 허브를 만듭니다.
> * 알림 허브를 APN 정보로 구성합니다.
> * iOS 앱을 알림 허브에 연결합니다.
> * 솔루션을 테스트합니다.

이 자습서에 대해 완료된 코드는 [GitHub](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_rest)에서 찾을 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

필수 조건을 충족하려면 다음이 필요합니다.

- 서비스에 익숙하지 않은 경우 [Azure 알림 허브 개요를](notification-hubs-push-notification-overview.md) 살펴보는 것입니다.
- [등록 및 설치에](notification-hubs-push-notification-registration-management.md)대해 자세히 알아보십시오.
- 활성 [애플 개발자 계정](https://developer.apple.com).
- 키체인에 설치된 유효한 개발자 인증서와 함께 Xcode를 실행하는 Mac입니다.
- 시뮬레이터로 푸시 알림을 테스트할 수 없기 때문에 실제 iPhone 장치를 실행하고 디버깅할 수 있습니다.
- [Apple 포털에](https://developer.apple.com) 등록되어 인증서와 연결된 실제 iPhone 장치입니다.
- 리소스를 만들고 관리할 수 있는 [Azure 구독입니다.](https://portal.azure.com)

iOS 개발에 대한 사전 경험이 없더라도 이 첫 번째 원칙 예제를 만드는 단계를 따를 수 있어야 합니다. 그러나 다음과 같은 개념에 익숙해지면 이점을 얻을 수 있습니다.

- Xcode 및 Swift로 iOS 앱 빌드.
- iOS용 [Azure 알림 허브](notification-hubs-ios-apple-push-notification-apns-get-started.md) 구성
- [Apple 개발자 포털](https://developer.apple.com) 및 Azure [포털](https://portal.azure.com).

> [!NOTE]
> 알림 허브는 **샌드박스** 인증 모드만 사용하도록 구성됩니다. 프로덕션 워크로드에 이 인증 모드를 사용해서는 안 됩니다.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>iOS 앱을 알림 허브에 연결

이 섹션에서는 알림 허브에 연결되는 iOS 앱을 빌드합니다.  

### <a name="create-an-ios-project"></a>iOS 프로젝트 만들기

1. Xcode에서 새 iOS 프로젝트를 만들고 **애플리케이션 단일 보기** 템플릿을 선택합니다.

1. 새 프로젝트에 대한 옵션을 설정할 때:

   1. Apple 개발자 포털에서 번들 **식별자를** `com.<organization>`설정할 때 사용한 제품 이름(PushDemo) 및 조직 **식별자()를** 지정합니다. **Product Name**

   1. **앱 ID가** 설정된 **팀을** 선택합니다.

   1. **언어를** **Swift로**설정합니다.

   1. **다음**을 선택합니다.

1. **지원 파일이라는**새 폴더를 만듭니다.

1. **지원 파일** 폴더에서 **devsettings.plist라는** 새 p-목록 파일을 만듭니다. git repo로 작업할 때 커밋되지 않도록 이 폴더를 **gitignore** 파일에 추가해야 합니다. 프로덕션 앱에서는 자동화된 빌드 프로세스의 일부로 이러한 비밀을 조건부로 설정할 수 있습니다. 이러한 설정은 이 연습에서 다루지 않습니다.

1. 프로비저닝한 알림 허브에서 사용자 고유의 값을 사용하여 다음 구성 항목을 포함하도록 **devsettings.plist를** 업데이트합니다.

   | Key                            | Type                     | 값                     |
   |--------------------------------| -------------------------| --------------------------|
   | 알림허브키             | String                   | \<허브키>                  |
   | 알림허브키이름         | String                   | \<허브키네임>              |
   | 알림허브이름            | String                   | \<허브 이름>                 |
   | 알림허브네임스페이스       | String                   | \<허브네임스페이스>            |

   Azure 포털에서 알림 허브 리소스로 이동하여 필요한 값을 찾을 수 있습니다. 특히 **알림허브이름** 및 **알림허브네임스페이스** 값은 **개요** 페이지 내의 **Essentials** 요약의 오른쪽 위 모서리에 있습니다.

   ![알림 허브 필수 요약](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   또한 **액세스 정책으로** 이동하여 에서와 같은 `DefaultFullSharedAccessSignature`각 **액세스 정책을**선택하여 **notificationHubKeyName** 및 **notificationHubKey** 값을 찾을 수도 있습니다. 그런 다음 기본 **연결 문자열에서** 에 `SharedAccessKeyName=` 대한 `notificationHubKeyName` 접두사 값과 `SharedAccessKey=` `notificationHubKey`에 대한 접두사 값을 복사합니다.

   연결 문자열은 다음과 같은 형식이어야 합니다.

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   간단하게 유지하려면 `DefaultFullSharedAccessSignature` 토큰을 사용하여 알림을 보낼 수 있도록 지정합니다. 실제로 `DefaultListenSharedAccessSignature` 알림을 받으려는 상황에 더 적합한 선택입니다.

1. **프로젝트 네비게이터에서** **프로젝트 이름을** 선택한 다음 **일반** 탭을 선택합니다.

1. **ID를** 찾은 다음 이전 단계에서 앱 `com.<organization>.PushDemo` **ID에** 사용되는 값인 **번들 식별자** 값이 일치하도록 설정합니다.

1. **서명 & 기능을**찾은 다음 Apple **개발자 계정에**적합한 **팀을** 선택합니다. **팀** 값은 인증서 및 프로필을 만든 값과 일치해야 합니다.

1. Xcode는 **번들 식별자를**기반으로 적절한 **프로비저닝 프로필** 값을 자동으로 끌어내야 합니다. 새 **프로비저닝 프로필** 값이 표시되지 않으면 **Xcode** > **기본 설정** > **계정을** 선택하여 **서명 ID에** 대한 프로필을 새로 고쳐 보고 수동 프로필 **다운로드** 단추를 선택하여 프로필을 다운로드합니다.

1. 여전히 **& 기능 서명** 탭에서 + **기능** 버튼을 클릭하고 목록에서 푸시 알림을 두 번 탭하여 **푸시 알림이** 활성화되었는지 확인합니다. **Push Notifications**

1. **AppDelegate.swift** 파일을 열어 **UNUserNotificationCenterDelegate** 프로토콜을 구현하고 다음 코드를 클래스의 맨 위에 추가합니다.

    ```swift
    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {
        
        ...

        var configValues: NSDictionary?
        var notificationHubNamespace : String?
        var notificationHubName : String?
        var notificationHubKeyName : String?
        var notificationHubKey : String?
        let tags = ["12345"]
        
        ...
    }
    ```

    나중에 이러한 멤버를 사용합니다. 특히 **사용자 지정 템플릿을**사용하여 **태그** 멤버를 등록의 일부로 사용합니다. 태그에 대한 자세한 내용은 등록 및 [템플릿 등록에](notification-hubs-templates-cross-platform-push-messages.md) [대한 태그를](notification-hubs-tags-segment-push-message.md) 참조하십시오.

1. 동일한 파일에서 다음 코드를 **didFinishLaunchingWithOptions** 함수에 추가합니다.

    ```swift
    if let path = Bundle.main.path(forResource: "devsettings", ofType: "plist") {
        if let configValues = NSDictionary(contentsOfFile: path) {
            self.notificationHubNamespace = configValues["notificationHubNamespace"] as? String
            self.notificationHubName = configValues["notificationHubName"] as? String
            self.notificationHubKeyName = configValues["notificationHubKeyName"] as? String
            self.notificationHubKey = configValues["notificationHubKey"] as? String
        }
    }

    if #available(iOS 10.0, *){
        UNUserNotificationCenter.current().delegate = self
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
            (granted, error) in

            if (granted)
            {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }
    }

    return true
    ```

    이 코드는 **devsettings.plist에서**설정 값을 검색하고, **AppDelegate** 클래스를 **UNUserNotificationCenter** 대리인으로 설정하고, 푸시 알림에 대한 권한 부여를 요청한 다음 **registerForRemoteNotifications**를 호출합니다.

    간단하게 하기 위해 코드는 *iOS 10 이상만*지원합니다. 평소와 같이 각 API와 접근 방식을 조건부로 사용하여 이전 OS 버전에 대한 지원을 추가할 수 있습니다.

1. 동일한 파일에 다음 함수를 추가합니다.

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        let keyWindow = UIApplication.shared.windows.filter {$0.isKeyWindow}.first
        keyWindow?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    코드는 **설치Id** 및 **pushChannel** 값을 사용하여 알림 허브에 등록합니다. 이 경우 **UIDevice.current.identifierForVendor을** 사용하여 장치를 식별하는 고유한 값을 제공한 다음 원하는 **푸시채널** 값을 제공하기 위해 **deviceToken을** 서식을 지정합니다. **showAlert** 기능은 데모를 위해 일부 메시지 텍스트를 표시하기 위해 단순히 존재합니다.

1. 여전히 **AppDelegate.swift** 파일에 는 **unUserNotificationCenterDelegate에** **해당** **함수를** 추가합니다. 이러한 함수는 앱이 포그라운드 또는 백그라운드에서 각각 실행중이라는 알림을 받으면 경고를 표시합니다.

    ```swift
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        willPresent notification: UNNotification, 
        withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        showAlert(withText: notification.request.content.body)
    }

    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        didReceive response: UNNotificationResponse, 
        withCompletionHandler completionHandler: @escaping () -> Void) {
        showAlert(withText: response.notification.request.content.body)
    }
    ```

1. **didRegisterForRemoteNotificationsDeviceToken** 기능의 맨 아래에 인쇄 문을 추가하여 **설치Id** 및 **pushChannel값이** 할당되고 있는지 확인합니다.

    ```swift
    print(installationId)
    print(pushChannel)
    ```

1. 나중에 프로젝트에 추가할 기본 구성 요소에 대한 **모델,** **서비스**및 **유틸리티** 폴더를 만듭니다.

1. 프로젝트가 실제 장치에서 빌드되고 실행되는지 확인합니다. 푸시 알림은 시뮬레이터를 사용하여 테스트할 수 없습니다.

### <a name="create-models"></a>모델 만들기

이 단계에서는 [알림 허브 REST API](/rest/api/notificationhubs/) 페이로드를 나타내고 필요한 공유 액세스 서명(SAS) 토큰 데이터를 저장하는 모델 집합을 만듭니다.

1. **모델** 폴더에 **PushTemplate.swift라는** 새 Swift 파일을 추가합니다. 이 모델은 **DeviceInstallation** 페이로드의 일부로 개별 템플릿의 **BODY를** 나타내는 구조체를 제공합니다.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. **장치설치.swift라는** 새 Swift 파일을 **모델** 폴더에 추가합니다. 이 파일은 **장치 설치를**만들거나 업데이트하기 위한 페이로드를 나타내는 구조체를 정의합니다. 파일에 다음 코드를 추가합니다.

    ```swift
    import Foundation

    struct DeviceInstallation : Codable {
        let installationId : String
        let pushChannel : String
        let platform : String = "apns"
        var tags : [String]
        var templates : Dictionary<String, PushTemplate>

        init(withInstallationId installationId : String, andPushChannel pushChannel : String) {
            self.installationId = installationId
            self.pushChannel = pushChannel
            self.tags = [String]()
            self.templates = Dictionary<String, PushTemplate>()
        }
    }
    ```

1. **모델** 폴더에 **TokenData.swift라는** 새 Swift 파일을 추가합니다. 이 모델은 만료와 함께 SAS 토큰을 저장하는 데 사용됩니다.

    ```swift
    import Foundation

    struct TokenData {

        let token : String
        let expiration : Int

        init(withToken token : String, andTokenExpiration expiration : Int) {
            self.token = token
            self.expiration = expiration
        }
    }
    ```

### <a name="generate-a-sas-token"></a>SAS 토큰 만들기

알림 허브는 Azure Service Bus와 동일한 보안 인프라를 사용합니다. REST API를 호출하려면 요청의 **권한 부여** 헤더에서 사용할 수 있는 [SAS 토큰을 프로그래밍 방식으로 생성해야](/rest/api/eventhub/generate-sas-token) 합니다.  

결과 토큰은 다음과 같은 형식으로 표시됩니다.

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

프로세스 자체에는 동일한 6가지 주요 단계가 포함됩니다.  

1. 1970년 1월 1일 자정 부터 경과된 초수를 의미하는 [UNIX Epoch 시간](https://en.wikipedia.org/wiki/Unix_time) 형식의 만료를 계산합니다.
1. 액세스하려는 리소스를 나타내는 **ResourceUrl서를** 지정하여 백분율 인코딩 및 소문자가 되도록 합니다. **ResourceUrl에는** 양식이 `'https://<namespace>.servicebus.windows.net/<hubName>'`있습니다.
1. 의 서식이 지정된 **StringToSign** `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`을 준비합니다.
1. **StringToSign** 값의 HMAC-SHA256 해시를 사용하여 **서명을** 계산하고 Base64인코딩합니다. 해시 값은 각 **권한 부여 규칙에**대한 연결 **문자열의** **키** 부분과 함께 사용됩니다.
1. Base64 인코딩된 **서명을** 서식을 지정하여 백분율인코딩됩니다.
1. **UrlEncodedSignature,** **ExpiryEpoch,** **KeyName**및 **UrlEncodedResourceUrl** 값을 사용하여 예상 된 형식으로 토큰을 생성합니다.

공유 액세스 서명에 대한 자세한 개요와 Azure Service Bus 및 알림 허브가 이를 사용하는 방법에 대한 자세한 내용은 [Azure Service Bus 설명서를](../service-bus-messaging/service-bus-sas.md) 참조하십시오.

이 Swift 예제에서는 Apple의 오픈 소스 **CommonCrypto** 라이브러리를 사용하여 서명 해시를 사용할 수 있습니다. C 라이브러리이기 때문에 Swift에서 즉시 액세스할 수 없습니다. 브리징 헤더를 사용하여 라이브러리를 사용할 수 있도록 할 수 있습니다.

브리징 헤더를 추가하고 구성하려면 다음을 수행하십시오.

1. Xcode에서 > **새** > **File** >  **파일****헤더 파일**파일을 선택합니다. 헤더 파일 **브리징헤더.h.**

1. 파일을 편집하여 **CommonHMAC.h를 가져옵니다.**

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. 대상의 빌드 **설정을** 업데이트하여 브리징 헤더를 참조합니다.

   1. **PushDemo** 프로젝트를 탭하고 아래로 스크롤하여 **Swift 컴파일러** 섹션으로 이동합니다.

   1. **설치 목표-C 호환성 헤더** 옵션이 **예로**설정되어 있는지 확인합니다.

   1. 파일 경로를 `'<ProjectName>/BridgingHeader.h'` **Objective-C 브리징 헤더** 옵션에 입력합니다. 브리징 헤더의 파일 경로입니다.

   이러한 옵션을 찾을 수 없는 경우 **기본** 또는 **사용자 지정**이 아닌 **모든** 보기를 선택해야 합니다.

   **CommonCrypto를** 좀 더 쉽게 사용할 수 있는 많은 타사 오픈 소스 래퍼 라이브러리가 있습니다. 그러나 이러한 라이브러리에 대한 논의는 이 문서의 범위를 벗어납니다.

1. 유틸리티 폴더 내에 **TokenUtility.swift라는** 새 Swift 파일을 추가하고 다음 코드를 **추가합니다.**

   ```swift
   import Foundation

   struct TokenUtility {    
        typealias Context = UnsafeMutablePointer<CCHmacContext>

        static func getSasToken(forResourceUrl resourceUrl : String, withKeyName keyName : String, andKey key : String, andExpiryInSeconds expiryInSeconds : Int = 3600) -> TokenData {
            let expiry = (Int(NSDate().timeIntervalSince1970) + expiryInSeconds).description
            let encodedUrl = urlEncodedString(withString: resourceUrl)
            let stringToSign = "\(encodedUrl)\n\(expiry)"
            let hashValue = sha256HMac(withData: stringToSign.data(using: .utf8)!, andKey: key.data(using: .utf8)!)
            let signature = hashValue.base64EncodedString(options: .init(rawValue: 0))
            let encodedSignature = urlEncodedString(withString: signature)
            let sasToken = "SharedAccessSignature sr=\(encodedUrl)&sig=\(encodedSignature)&se=\(expiry)&skn=\(keyName)"
            let tokenData = TokenData(withToken: sasToken, andTokenExpiration: expiryInSeconds)

            return tokenData
        }

        private static func sha256HMac(withData data : Data, andKey key : Data) -> Data {
            let context = Context.allocate(capacity: 1)
            CCHmacInit(context, CCHmacAlgorithm(kCCHmacAlgSHA256), (key as NSData).bytes, size_t((key as NSData).length))
            CCHmacUpdate(context, (data as NSData).bytes, (data as NSData).length)
            var hmac = Array<UInt8>(repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
            CCHmacFinal(context, &hmac)

            let result = NSData(bytes: hmac, length: hmac.count)
            context.deallocate()

            return result as Data
        }

        private static func urlEncodedString(withString stringToConvert : String) -> String {
            var encodedString = ""
            let sourceUtf8 = (stringToConvert as NSString).utf8String
            let length = strlen(sourceUtf8)

            let charArray: [Character] = [ ".", "-", "_", "~", "a", "z", "A", "Z", "0", "9"]
            let asUInt8Array = String(charArray).utf8.map{ Int8($0) }

            for i in 0..<length {
                let currentChar = sourceUtf8![i]

                if (currentChar == asUInt8Array[0] || currentChar == asUInt8Array[1] || currentChar == asUInt8Array[2] || currentChar == asUInt8Array[3] ||
                    (currentChar >= asUInt8Array[4] && currentChar <= asUInt8Array[5]) ||
                    (currentChar >= asUInt8Array[6] && currentChar <= asUInt8Array[7]) ||
                    (currentChar >= asUInt8Array[8] && currentChar <= asUInt8Array[9])) {
                    encodedString += String(format:"%c", currentChar)
                }
                else {
                    encodedString += String(format:"%%%02x", currentChar)
                }
            }

            return encodedString
        }
    }
   ```

   이 유틸리티는 SAS 토큰을 생성하는 논리를 캡슐화합니다.

   앞서 설명한 대로 **getSasToken** 함수는 토큰을 준비하는 데 필요한 상위 단계를 오케스트레이션합니다. 이 자습서의 후반부에서 설치 서비스에서 함수를 호출합니다.

   다른 두 함수는 **getSasToken** 함수: 서명을 계산하기 위한 **sha256HMac** 및 관련 URL 문자열을 인코딩하기 위한 **urlEncodedString에** 의해 호출됩니다. **urlEncodedString** 함수는 기본 제공 **addPercentEncoding** 함수를 사용하여 필요한 출력을 얻을 수 없기 때문에 필요합니다.

   [Azure 저장소 iOS SDK는](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) Objective-C에서 이러한 작업에 접근하는 방법의 훌륭한 예입니다. Azure Service Bus SAS 토큰에 대한 자세한 내용은 [Azure Service Bus 설명서에서](../service-bus-messaging/service-bus-sas.md)확인할 수 있습니다.

1. **AppDelegate.swift에서**다음 코드를 *didRegisterForRemoteNotificationsWithDeviceToken* 함수에 추가하여 **TokenUtility.getSasToken이** 유효한 토큰을 생성하고 있는지 확인합니다.
    
    ```swift
    let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

    let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                                withKeyName: self.notificationHubKeyName!,
                                                andKey: self.notificationHubKey!)
    
    print(tokenData.token)
    ```

    **baseAddress** 문자열의 자리 표시자 값을 사용자 고유의 문자열로 바꿔야 합니다.

### <a name="verify-the-sas-token"></a>SAS 토큰 확인

클라이언트에서 설치 서비스를 구현하기 전에 선택한 HTTP 유틸리티를 사용하여 앱이 SAS 토큰을 올바르게 생성하고 있는지 확인합니다. 이 튜토리얼의 목적을 위해, 선택의 우리의 도구는 **우체부가**될 것입니다 .

앱에서 생성되는 **설치Id** 및 **토큰** 값을 기록합니다.

다음 단계에 따라 **설치 API를 호출합니다.**

1. **우체부에서**새 탭을 엽니다.

1. 요청을 **GET으로** 설정하고 다음 주소를 지정합니다.

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. 다음과 같이 요청 헤더를 구성합니다.

   | Key           | 값            |
   | ------------- | ---------------- |
   | 콘텐츠 형식  | application/json |
   | 권한 부여 | \<sasToken>       |
   | x-ms-version  | 2015-01          |

1. **저장** 버튼 아래 오른쪽 상단에 표시되는 **코드** 단추를 선택합니다. 요청은 다음 예제와 유사해야 합니다.

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. **보내기** 단추를 선택합니다.

지정된 설치에 대한 등록이 없습니다이 **시점에서Id.** 확인결과 "401 승인되지 않은" 응답이 아니라 "404 찾을 수 없는" 응답이 생성되어야 합니다. 이 결과는 SAS 토큰이 수락되었는지 확인해야 합니다.

### <a name="implement-the-installation-service-class"></a>설치 서비스 클래스 구현

다음으로 설치 REST API를 중심으로 기본 [래퍼를 구현합니다.](/rest/api/notificationhubs/create-overwrite-installation)  

**서비스** 폴더 아래에 **NotificationRegistrationService.swift라는** 새 Swift 파일을 추가한 다음 이 파일에 다음 코드를 추가합니다.

```swift
import Foundation

class NotificationRegistrationService {
    private let tokenizedBaseAddress: String = "https://%@.servicebus.windows.net/%@"
    private let tokenizedCreateOrUpdateInstallationRequest = "/installations/%@?api-version=%@"
    private let session = URLSession(configuration: URLSessionConfiguration.default)
    private let apiVersion = "2015-01"
    private let jsonEncoder = JSONEncoder()
    private let defaultHeaders: [String : String]
    private let installationId : String
    private let pushChannel : String
    private let hubNamespace : String
    private let hubName : String
    private let keyName : String
    private let key : String
    private var tokenData : TokenData? = nil
    private var tokenExpiryDate : Date? = nil
    
    init(withInstallationId installationId : String,
            andPushChannel pushChannel : String,
            andHubNamespace hubNamespace : String,
            andHubName hubName : String,
            andKeyName keyName : String,
            andKey key: String) {
        self.installationId = installationId
        self.pushChannel = pushChannel
        self.hubNamespace = hubNamespace
        self.hubName = hubName
        self.keyName = keyName
        self.key = key
        self.defaultHeaders = ["Content-Type": "application/json", "x-ms-version": apiVersion]
    }
    
    func register(
        withTags tags : [String]? = nil,
        andTemplates templates : Dictionary<String, PushTemplate>? = nil,
        completeWith completion: ((_ result: Bool) -> ())? = nil) {
        
        var deviceInstallation = DeviceInstallation(withInstallationId: installationId, andPushChannel: pushChannel)
        
        if let tags = tags {
            deviceInstallation.tags = tags
        }
        
        if let templates = templates {
            deviceInstallation.templates = templates
        }
        
        if let deviceInstallationJson = encodeToJson(deviceInstallation) {
            let sasToken = getSasToken()
            let requestUrl = String.init(format: tokenizedCreateOrUpdateInstallationRequest, installationId, apiVersion)
            let apiEndpoint = "\(getBaseAddress())\(requestUrl)"
            
            var request = URLRequest(url: URL(string: apiEndpoint)!)
            request.httpMethod = "PUT"
            
            for (key,value) in self.defaultHeaders {
                request.addValue(value, forHTTPHeaderField: key)
            }
            
            request.addValue(sasToken, forHTTPHeaderField: "Authorization")
            request.httpBody = Data(deviceInstallationJson.utf8)
            
            (self.session.dataTask(with: request) { dat, res, err in
                if let completion = completion {
                        completion(err == nil && 
                        (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }
    
    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }
    
    private func getSasToken() -> String {
        if (tokenData == nil ||
            tokenExpiryDate == nil ||
            Date() >= tokenExpiryDate!) {
            
            self.tokenData = TokenUtility.getSasToken(
                forResourceUrl: getBaseAddress(),
                withKeyName: self.keyName,
                andKey: self.key)
            
            self.tokenExpiryDate = Date(timeIntervalSinceNow: -(5 * 60))
                .addingTimeInterval(TimeInterval(tokenData!.expiration))
        }

        return (tokenData?.token)!
    }
    
    private func encodeToJson<T : Encodable>(_ object: T) -> String? {
        do {
            let jsonData = try jsonEncoder.encode(object)
            if let jsonString = String(data: jsonData, encoding: .utf8) {
                return jsonString
            } else {
                return nil
            }
        }
        catch {
            return nil
        }
    }
}
```

필요한 세부 정보는 초기화의 일부로 제공됩니다. 태그와 템플릿은 선택적으로 장치 **설치** JSON 페이로드의 일부를 형성하기 위해 **레지스터** 함수로 전달됩니다.  

**레지스터** 함수는 요청을 준비하기 위해 다른 개인 함수를 호출합니다. 응답을 받은 후 완료가 호출되고 등록이 성공했는지 여부를 나타냅니다.  

요청 끝점은 **getBaseAddress** 함수에 의해 생성됩니다. 구성은 초기화 중에 제공된 알림 허브 매개 변수 *네임스페이스* 및 *이름을* 사용합니다.  

**getSasToken** 함수는 현재 저장된 토큰이 유효한지 여부를 확인합니다. 토큰이 유효하지 않으면 함수는 **TokenUtility를** 호출하여 새 토큰을 생성한 다음 값을 반환하기 전에 저장합니다.

마지막으로 **encodeToJson** 요청 본문의 일부로 사용 하기 위해 JSON으로 각 모델 개체를 변환 합니다.

### <a name="invoke-the-notification-hubs-rest-api"></a>알림 허브 REST API 호출

마지막 단계는 **NotificationRegistrationService를** 사용하여 **NotificationHub**에 등록하도록 **AppDelegate를** 업데이트하는 것입니다.

1. **AppDelegate.swift를** 열고 클래스 수준 변수를 추가하여 **NoficiationRegistrationService** 및 일반 **푸시 템플릿에**대한 참조를 저장합니다.

    ```swift
    var registrationService : NotificationRegistrationService?
    let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
    ```

1. 동일한 파일에서 **didRegisterForRemoteNotificationsDeviceToken** 기능을 업데이트하여 필수 매개 변수로 **NotificationRegistrationService를** 초기화한 다음 **레지스터** 함수를 호출합니다.

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})

        // Initialize the Notification Registration Service
        self.registrationService = NotificationRegistrationService(
            withInstallationId: installationId,
            andPushChannel: pushChannel,
            andHubNamespace: notificationHubNamespace!,
            andHubName: notificationHubName!,
            andKeyName: notificationHubKeyName!,
            andKey: notificationHubKey!)

        // Call register, passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    간단하게 하기 위해 몇 개의 인쇄 문을 사용하여 **레지스터** 작업의 결과로 출력 창을 업데이트합니다.

1. 이제 실제 장치에서 앱을 빌드하고 실행합니다. 출력 창에 "등록"이 표시됩니다.

## <a name="test-the-solution"></a>솔루션 테스트

이 단계에서 우리의 응용 프로그램은 **NotificationHub에** 등록하고 푸시 알림을받을 수 있습니다. Xcode에서 디버거를 중지하고 현재 실행 중인 경우 앱을 닫습니다. 그런 다음 **장치 설치** 세부 정보가 예상대로되고 앱에서 푸시 알림을 받을 수 있는지 확인합니다.  

### <a name="verify-the-device-installation"></a>장치 설치 확인

이제 **Postman을** 사용하여 [SAS 토큰을 확인하여](#verify-the-sas-token)이전과 동일한 요청을 할 수 있습니다. SAS 토큰이 만료되지 않았다고 가정하면 이제 응답에 템플릿 및 태그와 같이 제공한 설치 세부 정보가 포함되어야 합니다.

```json
{
    "installationId": "<installationId>",
    "pushChannel": "<pushChannel>",
    "pushChannelExpired": false,
    "platform": "apns",
    "expirationTime": "9999-12-31T23:59:59.9999999Z",
    "tags": [
        "12345"
    ],
    "templates": {
        "genericTemplate": {
            "body": "{\"aps\":{\"alert\":\"$(message)\"}}",
            "tags": [
                "genericTemplate"
            ]
        }
    }
}
```

이전 **SAS 토큰이** 만료된 경우 **TokenUtility** 클래스의 **24줄에** **중단점을** 추가하여 새 **SAS 토큰을** 얻고 **권한 부여** 헤더를 새 값으로 업데이트할 수 있습니다.

### <a name="send-a-test-notification-azure-portal"></a>테스트 알림 보내기(Azure 포털)

이제 알림을 받을 수 있는 테스트하는 가장 빠른 방법은 Azure Portal의 알림 허브로 찾아보는 것입니다.

1. Azure 포털에서 알림 허브의 **개요** 탭으로 검색합니다.

1. 포털 창의 왼쪽 상단에 있는 **필수** 요약 위에 있는 **테스트 보내기**를 선택합니다.

    ![알림 허브 필수 요약 테스트 보내기 단추](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. **플랫폼** 목록에서 **사용자 지정 템플릿을** 선택합니다.

1. **태그로 보내기 표현식에**대해 **12345를** 입력합니다. 이전에 설치시 이 태그를 지정했습니다.

1. 선택적으로 JSON 페이로드에서 **메시지를** 편집합니다.

    ![알림 허브 테스트 보내기](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. **전송을 선택합니다.** 포털은 알림이 장치에 성공적으로 전송되었는지 여부를 표시해야 합니다.

    ![알림 허브 테스트 보내기 결과](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    앱이 포그라운드에서 실행되고 있지 않다고 가정하면 기기의 알림 **센터에알림도** 표시됩니다. 알림을 탭하면 앱이 열리고 경고가 표시됩니다.

    ![알림 수신 예](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>테스트 알림 보내기(메일 캐리어)

**POSTMAN을**사용하여 [REST API를](/rest/api/notificationhubs/) 통해 알림을 보낼 수 있습니다.

1. **우체부에서**새 탭을 엽니다.

1. 요청을 **POST로**설정하고 다음 주소를 입력합니다.

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. 다음과 같이 요청 헤더를 구성합니다.

   | Key                            | 값                          |
   | ------------------------------ | ------------------------------ |
   | 콘텐츠 형식                   | 응용 프로그램/json;charset=utf-8 |
   | 권한 부여                  | \<sasToken>                     |
   | ServiceBusNotification-Format  | template                       |
   | 태그들                           | "12345"                        |

1. 다음 JSON 페이로드를 사용하여 **RAW - JSON(application.json)을** 사용하도록 요청 **BODY를** 구성합니다.

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. 창 오른쪽 상단에 있는 **저장** 버튼 아래에 있는 **코드** 단추를 선택합니다. 요청은 다음 예제와 유사해야 합니다.

    ```html
    POST /<hubName>/messages/?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json;charset=utf-8.
    ServiceBusNotification-Format: template
    Tags: "12345"
    Authorization: <sasToken>
    Cache-Control: no-cache
    Postman-Token: <postmanToken>

    {
        "message" : "Hello from Postman!"
    }
    ```

1. **보내기** 단추를 선택합니다.

**201 Created** 성공 상태 코드를 받고 클라이언트 장치에서 알림을 받아야 합니다.

## <a name="next-steps"></a>다음 단계
이제 [REST API를](/rest/api/notificationhubs/) 통해 알림 허브에 연결된 기본 iOS Swift 앱이 있으며 알림을 보내고 받을 수 있습니다. 자세한 내용은 다음 문서를 참조하세요.

- [Azure 알림 허브 개요](notification-hubs-push-notification-overview.md)
- [Notification Hubs REST API](/rest/api/notificationhubs/)
- [백 엔드 작업에 대한 알림 허브 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [GitHub의 알림 허브 SDK](https://github.com/Azure/azure-notificationhubs)
- [응용 프로그램 백 엔드에 등록](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [등록 관리](notification-hubs-push-notification-registration-management.md)
- [태그 작업](notification-hubs-tags-segment-push-message.md) 
- [사용자 지정 템플릿 작업](notification-hubs-templates-cross-platform-push-messages.md)
- [공유 액세스 서명을 가진 서비스 버스 액세스 제어](../service-bus-messaging/service-bus-sas.md)
- [프로그래밍 방식으로 SAS 토큰 생성](/rest/api/eventhub/generate-sas-token)
- [애플 보안: 일반적인 암호](https://developer.apple.com/security/)
- [유닉스 시대 시간](https://en.wikipedia.org/wiki/Unix_time)
- [Hmac](https://en.wikipedia.org/wiki/HMAC)
