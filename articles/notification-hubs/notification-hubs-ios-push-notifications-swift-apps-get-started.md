---
title: Azure Notification Hubs를 사용 하 여 Swift iOS 앱에 알림 푸시 | Microsoft Docs
description: Azure Notification Hubs를 사용 하 여 Swift iOS 앱에 알림을 푸시하는 방법을 알아봅니다.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: patniko
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.openlocfilehash: 64a05c6cbd412953ae10b31efc1d141c9cefd062
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65994768"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-using-the-notification-hubs-rest-api"></a>자습서: Notification Hubs REST API를 사용 하 여 Swift iOS 앱에 알림 푸시

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

이 자습서를 사용 하 여 Azure Notification Hubs 푸시 알림을 사용 하 여 iOS Swift 기반 응용 프로그램을 [REST API](/rest/api/notificationhubs/)합니다. [APNS(Apple Push Notification Service)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)를 사용하여 푸시 알림을 받는 빈 iOS 앱을 만듭니다.

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 인증서 서명 요청 파일 생성
> * 푸시 알림에 대해 앱 요청
> * 앱용 프로비저닝 프로필 만들기
> * 알림 허브 만들기
> * APNS 정보로 알림 허브 구성
> * 알림 허브에 iOS 앱 연결
> * 솔루션 테스트

## <a name="prerequisites"></a>필수 조건
필수 조건을 충족하려면 다음이 필요합니다.

- 진행 [Azure Notification Hubs 개요](notification-hubs-push-notification-overview.md) 서비스에 익숙하지 않은 경우. 
- 등록 및 설치에 알아봅니다. [등록 관리](notification-hubs-push-notification-registration-management.md)
- 활성 [Apple 개발자 계정](https://developer.apple.com) 
- Keychain에 설치 된 유효한 개발자 인증서와 함께 Xcode 사용 하 여 Mac
- 물리적 iPhone 장치를 실행 하 고 디버그할 수 있는 (시뮬레이터를 사용 하 여 푸시 알림을 테스트할 수 없는)
- 물리적 iPhone 장치에 등록 합니다 [Apple 포털](https://developer.apple.com) 인증서를 사용 하 여 연결
- [Azure 구독](https://portal.azure.com) 만들고 리소스를 관리 하는 위치

이전에 본 경험이 없는이 첫 번째 원칙은 예제를 만드는 과정을 따르려면 수 있어야 합니다. 그러나 다음과 같은 개념 사용 경험 상당히 유용 합니다.

- Xcode 및 Swift로 iOS 앱 구축
- 구성 된 [Azure 알림 허브](notification-hubs-ios-apple-push-notification-apns-get-started.md) iOS 용
- 사용 경험을 [Apple Developer 포털](https://developer.apple.com) 고 [Azure portal](https://portal.azure.com)

> [!NOTE]
> 알림 허브는 사용 하도록 구성 합니다 *샌드박스* 인증 모드에만 해당 합니다. 프로덕션 워크 로드에 대 한이 인증 모드를 사용 하지 해야 합니다.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-notification-hubs"></a>Notification Hubs에 iOS 앱 연결
이 섹션에서는 알림 허브에 연결 하는 iOS 앱을 빌드합니다.  

### <a name="create-an-ios-project"></a>IOS 프로젝트 만들기
1. **Xcode**새 iOS 프로젝트를 만들고 선택 합니다 **Single View Application** 템플릿.
2. 새 프로젝트에 대 한 옵션을 설정할 때 다음이 단계를 수행 합니다.
    1. 사용한 것과 동일한 **Product Name** (즉, **PushDemo**) 및 **조직 식별자** (즉, `com.<organization>`) 때 사용 하는 **번들 식별자** 에서 설정한 합니다 **Apple Developer 포털**합니다. 
    2. 선택 합니다 **팀** 는 **앱 ID** 등록 설정 되었습니다.
    3. 설정 된 **언어** 하 **Swift**합니다.
    4. **다음**을 클릭합니다.
3. 라는 새 폴더를 만듭니다 **SupportingFiles**합니다.
4. 새 **plist** 라는 파일 **devsettings.plist** 아래의 합니다 **SupportingFiles** 폴더입니다. 이 폴더에 추가 해야 하 **gitignore** 아니기 때문 커밋된 작업할 때 파일을 **git 리포지토리**합니다. 프로덕션 앱의 경우에 가능성이 수 조건부로 설정 하 게 이러한 비밀 자동화 된 빌드 프로세스의 일부로. 그러나이 연습 일부로 설명 하지 않습니다.
5. 업데이트 **devsettings.plist** 다음 구성 항목을 포함 하 여 (고유한 값을 사용 하는 **알림 허브** 프로 비전 하면):

   | 키                            | Type                     | 값                     |               
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | \<hubKey\>                |
   | notificationHubKeyName         | String                   | \<hubKeyName\>            |
   | notificationHubName            | String                   | \<hubName\>               |
   | notificationHubNamespace       | String                   | \<hubNamespace\>          |
    
   로 이동 하 여 필수 값을 찾을 수 있습니다 합니다 **알림 허브** 리소스에는 **Azure portal**합니다. 찾을 수 있습니다는 **notificationHubName** 및 **notificationHubNamespace** 의 오른쪽 위 모서리에 있는 값을 **Essentials** 내에서 요약는  **개요** 페이지입니다.

   ![Notification Hubs Essentials 요약](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   찾을 수 있습니다 합니다 **notificationHubKeyName** 하 고 **notificationHubKey** 로 이동 하 여 값 **액세스 정책을**각각 클릭 하 **액세스 정책**합니다. 예: `DefaultFullSharedAccessSignature`. 복사 합니다 **기본 연결 문자열** 값을 접두사로 `SharedAccessKeyName=` 에 대 한 `notificationHubKeyName` 값 접두사가 붙은 `SharedAccessKey=` 에 대 한는 `notificationHubKey`합니다. 연결 문자열 형식 이어야 합니다.
    
   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   간단히 말하자면, 사용할지 *DefaultFullSharedAccessSignature* 뿐만 아니라 알림에서 보낼 토큰을 사용할 수 있도록 합니다. 그러나 연습에에서는 `DefaultListenSharedAccessSignature` 만 하려는 알림을 수신 하는 상황에 보다 적합할 것입니다.       
6. 아래 **Project Navigator**, 클릭는 **프로젝트 이름**를 클릭 합니다 **일반** 탭
7. 찾을 **Identity**, 설정한 합니다 **번들 식별자** 에 사용 된 계정이 일치 하도록 값을 **앱 ID** (이전 단계)에서, `'com.<organization>.PushDemo'`
8. 찾을 **서명**, 한 다음 적절 한 선택 했는지 **팀** 에 대 한 프로그램 **Apple 개발자 계정** (하나는 만든 인증서 및 프로필 이전).  **Xcode** 이 자동으로 적절 한 아래로 끌어오기 **프로 비전 프로필** 기반으로 합니다 **번들 식별자**합니다. 새 표시 되지 않으면 **프로 비전 프로필**에 대 한 프로필을 새로 고쳐 보세요 합니다 **서명 Id** (*Xcode > 기본 설정 > 계정 > 세부 정보 보기*). 클릭 하는 **서명 Id**를 클릭 한 다음는 **새로 고침** 단추 오른쪽 아래에서에서 프로필을 다운로드 해야 합니다.
9. 선택 합니다 **기능** 탭을 확인 **푸시 알림** 활성화 됩니다.
10. 오픈 프로그램 **AppDelegate.swift** 구현 하는 파일을 *UNUserNotificationCenterDelegate* 프로토콜 및 클래스의 맨 위에 다음 코드를 추가:
    
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
        let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
        
        ...
    }
    ```

    이러한 멤버는 나중에 사용할 수 있습니다. 합니다 *태그* 하 고 *genericTemplate* 등록의 일환으로 사용할 합니다. 태그에 대 한 자세한 내용은 참조 하세요. [등록에 대 한 태그](notification-hubs-tags-segment-push-message.md) 하 고 [템플릿 등록](notification-hubs-templates-cross-platform-push-messages.md)합니다.
 
11. 동일한 파일에 다음 코드를 추가 합니다 *didFinishLaunchingWithOptions* 함수:

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

    이 코드에서 설정 값을 검색 **devsettings.plist**를 설정 합니다 **AppDelegate** 클래스를 *UNUserNotificationCenter* 대리자 요청 권한 부여 푸시 알림, 한 다음 호출 *registerForRemoteNotifications*합니다.
    
    간단히 말하자면, 코드를 지 원하는 **iOS 10 이상에 해당**합니다. 조건에 따라 사용 하 여 해당 Api 및 접근 방식을 마찬가지로 일반적으로 이전 OS 버전에 대 한 지원을 추가할 수 있습니다.

12. 동일한 파일에서 다음 함수를 추가 합니다.

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        self.window?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    코드를 사용 하는 *installationId* 및 *pushChannel* 사용 하 여 등록 하는 값을 **알림 허브**합니다. 이 경우 사용 중인 *UIDevice.current.identifierForVendor* 장치를 식별 한 다음 서식을 지정 하는 고유 값을 제공 합니다 *deviceToken* 원하는 제공*pushChannel* 값입니다. 합니다 *showAlert* 함수 데모용으로 일부 메시지 텍스트를 표시 하는 것입니다.

13. 여전히 **AppDelegate.swift**를 추가 합니다 *willPresent* 및 *didReceive* **UNUserNotificationCenterDelegate** 함수 앱 각각 포그라운드 및 백그라운드에서 실행 중인 경우 알림이 도착 하는 경우 경고를 표시 합니다.
    
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

14. Print 문은 맨 아래에 추가 합니다 *didRegisterForRemoteNotificationsWithDeviceToken* 되었는지 확인 하는 함수 *installationId* 하 고 *pushChannel* 되 할당 된 값
15. 폴더를 만듭니다 (**모델**를 **Services**, 및 **유틸리티**) 나중에 프로젝트에 추가 될에서는 기본적인 구성 요소에 대 한
16. 프로젝트가 빌드되고 물리적 장치 (푸시 알림은 시뮬레이터를 사용 하 여 테스트할 수 없습니다)에서 실행 확인

### <a name="create-models"></a>모델 만들기
이 단계에서는 모델을 나타내는 집합을 만든 합니다 [Notification Hubs REST API](/rest/api/notificationhubs/) 페이로드 및 필요한 스토어로 **SAS 토큰** 데이터입니다.


1.  새 swift 파일을 추가 합니다 **모델** 호출 **PushTemplate.swift**합니다. 이 모델은 나타내는 구조체를 제공 합니다 **본문** 일환으로 개별 템플릿의 합니다 **DeviceInstallation** 페이로드:
    
    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String
    
        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. 새 swift 파일을 추가 합니다 **모델** 폴더가 **DeviceInstallation.swift**합니다. 이 파일을 만들거나 업데이트 하기 위한 페이로드를 나타내는 구조체 정의 **장치 설치**합니다. 파일에 다음 코드를 추가합니다.
    
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

3.  새 swift 파일을 추가 **모델** 호출 **TokenData.swift**합니다. 이 모델에서 저장 하는 데 사용 됩니다는 **SAS 토큰** 해당 만료와 함께

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

### <a name="generate-a-sas-token"></a>SAS 토큰 생성
**Notification Hubs** 와 동일한 보안 인프라를 사용 하 여 **Azure Service Bus**합니다. REST API를 호출 하려면 해야 [프로그래밍 방식으로 SAS 토큰을 생성할](/rest/api/eventhub/generate-sas-token) 에서 사용할 수 있는 합니다 **권한 부여** 요청의 헤더입니다.  

결과 토큰 형식은 됩니다. 

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

프로세스 자체에 동일한 6 개의 주요 단계가 포함 됩니다.  

1.  만료 컴퓨팅 [UNIX Epoch 시간](https://en.wikipedia.org/wiki/Unix_time) 형식 (00시: 00 UTC 1970 년 1 월 1 이후 경과 된 초)
2.  서식 지정 합니다 **ResourceUrl** (즉,에 액세스 하려면 하려는 리소스를 나타내는 `'https://\<namespace\>.servicebus.windows.net/\<hubName\>'`) 백분율로 인코딩된 대 / 소문자 이므로
3.  준비 합니다 **StringToSign**는 이루어집니다 `'\<**UrlEncodedResourceUrl**\>\n\<**ExpiryEpoch**\>'`
4.  컴퓨팅 (및 기본 64 인코딩)를 **서명** 를 사용 하 여는 **HMAC-SHA256** 의 **StringToSign** 값과 **키** 부분는 **연결 문자열** (각각에 대 한 **권한 부여 규칙**)
5.  Base 64로 인코드됩니다 서식을 **서명을** 이므로 % 인코딩
6.  생성 된 **토큰** 사용 하 여 예상 되는 형식은 **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**, 및 **UrlEncodedResourceUrl** 값

참조를 [Azure Service Bus 설명서](../service-bus-messaging/service-bus-sas.md) 에 대 한 보다 철저 한 개요 **공유 액세스 서명을** 에서 사용 방법 **Azure Service Bus** 및  **Notification Hubs**합니다.

Swift이 예에서는 Apple의 오픈 소스를 사용 하려는 **CommonCrypto** 라이브러리를 사용 하 여 서명의 해시 하는 데 도움이 됩니다. C 라이브러리를 경우에 Swift-의-기본에서 액세스할 수 없습니다. 그러나 할 수 있습니다이 브리징 헤더를 사용 하 여 사용할 수 있습니다. 추가 합니다 브리징 헤더를 구성 하려면:

1. **Xcode**로 이동 하세요 **파일**, 한 다음 **새로 만들기**, 다음 **파일** 선택 **헤더 파일** 이름을 *'BridgingHeader.h'*
2. 가져올 파일을 편집 **CommonHMAC**

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

3. 대상의 업데이트 **빌드 설정** 합니다 브리징 헤더를 참조 합니다. 열기는 **구성 설정** 까지 아래로 스크롤하여 탭 합니다 **Swift 컴파일러** 섹션입니다. 있는지 확인 합니다 **Objective-c 호환성 헤더 설치** 옵션으로 설정 **예** 에 브리징 헤더는 파일 경로 입력 하 고는 **Objective-c 브리징 헤더**   즉 옵션 `'\<ProjectName\>/BridgingHeader.h'`합니다. 이러한 옵션을 찾을 수 없는 경우는 **모든** 보기를 선택 (대신 **기본** 하거나 **사용자 지정 된**).
    
   많은 타사 오픈 소스 래퍼 라이브러리를 사용할 수 있으며 사용 하 여 **CommonCrypto** 과정은 비교적 간단 합니다. 이 기사의 범위를 벗어납니다.

4. 새 Swift 파일을 추가 합니다 **유틸리티** 라는 폴더 **TokenUtility.swift** 다음 코드를 추가 하 고:

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
    
    이 유틸리티를 생성 하는 일을 담당 하는 논리를 캡슐화 합니다 **SAS 토큰**합니다. 합니다 *getSasToken* 함수는 이전에 설명 된 대로 토큰을 준비 하는 데 필요한 대략적인 단계를 오케스트레이션 하 고이 자습서의 뒷부분에 나오는 단계에서 설치 서비스에서 호출 됩니다. 호출 하는 다른 두 함수는 *getSasToken 함수*; *sha256HMac* 서명을 계산 하 고 *urlEncodedString* 인코딩을 해당 Url 문자열에 대 한 합니다. 합니다 *urlEncodedString* 기본 제공을 사용 하 여 필요한 출력을 얻을 수 되지 않은 것 처럼 함수 필요 했습니다 *addingPercentEncoding* 함수입니다. 합니다 [Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) 주기-C에서 라도 이러한 작업에 접근 하는 방법 보여 주는 좋은 예로 제공 에 대 한 자세한 정보 **Azure Service Bus SAS 토큰** 에서 찾을 수 있습니다 합니다 [Azure Service Bus 설명서](../service-bus-messaging/service-bus-sas.md)합니다. 

### <a name="verify-the-sas-token"></a>SAS 토큰을 확인 합니다.
클라이언트에서 설치 서비스를 구현 하기 전에 앱은 제대로 생성 하는지 확인할 수 있습니다 합니다 **SAS 토큰** 선택한 http 유틸리티를 사용 하 여 합니다. 이 게시물에서는 원하는 도구인 됩니다 **Postman**합니다.

기록해는 *installationId* 하며 *토큰* 적절 하 게 배치를 사용 하 여 앱에서 생성 되는 값 인쇄 문 또는 중단점. 

다음과 같이 호출 하 여 *설치* API:

1. **Postman**, 새 탭 열기
2. 요청을 설정 **가져올** 및 다음 주소:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

3. 요청 헤더를 다음과 같이 구성 합니다.
    
   | 키           | 값            |
   | ------------- | ---------------- |
   | 콘텐츠 형식  | application/json |
   | 권한 부여 | \<sasToken\>     |
   | x-ms-version  | 2015-01          |

4. 클릭 합니다 **코드** 단추 (오른쪽 아래를 **저장** 단추). 요청은 아래 예제와 비슷하게 표시 됩니다.

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

5. 클릭 합니다 **보낼** 단추

지정 된 등록이 없거나 존재 *installationId* 이때 해야 생길에 **404 찾을 수 없음** 응답 대신 **401 권한 없음**합니다. 이 결과 확인 해야 합니다 **SAS 토큰** 수락 되었습니다.

### <a name="implement-the-installation-service-class"></a>설치 서비스 클래스를 구현 합니다.
다음이 기본 래퍼를 구현 하는 [설치 REST API](/rest/api/notificationhubs/create-overwrite-installation)합니다.  

새 Swift 파일을 추가 합니다 **Services** 라는 폴더 **NotificationRegistrationService.swift**,이 파일에 다음 코드를 추가:

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
                        completion(err == nil && (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }
    
    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }
    
    private func getSasToken() -> String {
        if (tokenData == nil ||
            Date(timeIntervalSince1970: Double((tokenData?.expiration)!)) < Date(timeIntervalSinceNow: -(5 * 60))) {
            self.tokenData = TokenUtility.getSasToken(forResourceUrl: getBaseAddress(), withKeyName: self.keyName, andKey: self.key)
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
 
필수 세부 정보는 초기화의 일부로 제공 됩니다. 태그와 템플릿을에 선택적으로 전달 되는 *등록* 부분을 형성 하는 함수는 **장치 설치** JSON 페이로드입니다.  

합니다 *등록* 요청을 준비 하는 다른 개인 함수 함수를 호출 합니다. 응답이 수신 되 면 완료 라고 나타내는 등록이 성공 했는지 여부입니다.  

요청 끝점에서 생성 되는 *getBaseAddress* 함수를 사용 하는 **알림 허브** 매개 변수 **네임 스페이스** 및 **이름**초기화 중에 제공 합니다.  

*getSasToken* 함수는 현재 저장된 된 토큰이 유효한 지, 그렇지 않으면 호출를 확인 합니다 **TokenUtility** 새 암호를 생성 하 고 값을 반환 하기 전에 저장 하려면. 

마지막 합니다 *encodeToJson* 요청 본문의 일부로 해당 모델 개체 JSON으로 변환 됩니다.

### <a name="invoke-the-notification-hubs-rest-api"></a>Notification Hubs REST API를 호출 합니다.
업데이트 하는 마지막 단계 **AppDelegate** 사용 하는 **NotifiationRegistrationService** 등록할 우리의 **NotificationHub**합니다. 

1. 오픈 **AppDelegate.swift** 에 대 한 참조를 저장 하는 클래스 수준 변수를 추가 합니다 **NoficiationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

2. 동일한 파일을 업데이트 합니다 *didRegisterForRemoteNotificationsWithDeviceToken* 초기화 하는 함수는 **NotificationRegistrationService** 필수 매개 변수를 사용 하 여 호출 합니다 *등록* 함수입니다.

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
    
        // Call register passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    간단히 말하자면, print 문은 가지를 사용 하 여 결과 사용 하 여 출력 창 업데이트 하려는 합니다 *등록* 작업 합니다. 

3. 이제 빌드 및 (물리적 장치)에서 앱을 실행 합니다. 나타납니다 **"Registered"** 출력 창에 있습니다.

## <a name="test-the-solution"></a>솔루션 테스트
이 단계에서이 앱을 등록 **NotificationHub** 푸시 알림을 수신할 수 있습니다. **Xcode**디버거를 중지 하 고 (현재 실행 중인) 하는 경우 앱을 닫습니다. 다음으로, 하는지 검사할 수 있습니다 합니다 **장치 설치** 푸시 알림 세부 정보는 예상 대로 및 앱 실제로 이제 받을 수 있습니다.  

### <a name="verify-the-device-installation"></a>장치 설치를 확인 합니다.
사용 하 여 앞서 수행한 것 처럼 현재 같은 요청을 만들 수 있는 **Postman** 에 대 한 [SAS 토큰을 확인 하](#verify-the-sas-token)합니다. 가정 합니다 **SAS 토큰** 되지 않은 만료 응답 이제 포함 되는 템플릿 및 태그 등 사용자가 제공한 설치 세부 정보입니다.  

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

### <a name="send-a-test-notification-azure-portal"></a>(Azure portal) 테스트 알림 보내기
이제 알림을 받을 수 있습니다를 테스트 하는 가장 빠른 방법은로 이동 하는 것을 **알림 허브** 에 **Azure portal**합니다.

1. 에 **Azure portal**로 이동 합니다 **개요** 탭에 **알림 허브**
2. 클릭 **테스트 보내기** (왼쪽 위) 위에 합니다 **Essentials** 요약

    ![Notification Hubs Essentials 요약의 테스트 보내기 단추](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)
3. 선택할 **사용자 지정 템플릿을** 목록에서 **플랫폼**
4. 입력 **12345** 에 대 한 합니다 **태그 식으로 보내기** (우리의 설치에서이 태그 지정 했습니다)
5. 필요에 따라 편집 합니다 **메시지** JSON 페이로드의
    
    ![Notification Hubs 테스트 보내기](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)
6. 클릭 **보낼** 포털 알림이 장치에 성공적으로 전송 된 여부를 나타내야 하 고

    ![Notification Hubs 테스트 보내기 결과](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    알림을 표시 합니다 **알림 센터** (포그라운드에서 실행 되 고 있지 앱 가정) 장치에서. 알림을 탭은 앱을 열고 하 고 경고를 표시 해야 합니다.

    ![알림이 수신 예제](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-postman"></a>(Postman) 테스트 알림 보내기
각각을 통해 알림을 보낼 수 있습니다 [REST API](/rest/api/notificationhubs/) 를 통해 **Postman** 잘 하며 더 편리 하 게 테스트를 수 있습니다. 

1. **Postman**, 새 탭 열기
2. 요청을 설정 **POST** 다음 주소를 입력 합니다.

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

3. 요청 헤더를 다음과 같이 구성 합니다.
    
   | 키                            | 값                          |
   | ------------------------------ | ------------------------------ |
   | 콘텐츠 형식                   | application/json;charset=utf-8 |
   | 권한 부여                  | \<sasToken\>                   |
   | ServiceBusNotification-Format  | template                       |
   | 태그들                           | "12345"                        |

4. 요청을 구성 **본문** 사용 하려면 **RAW-JSON (application.json)** 다음 JSON 페이로드를 사용 하 여:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

5. 클릭 합니다 **코드** 단추 (오른쪽 아래를 **저장** 단추). 요청은 아래 예제와 비슷하게 표시 됩니다.

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

5. 클릭 합니다 **보낼** 단추

성공 상태 코드 및 클라이언트 장치에 알림이 표시 됩니다.

## <a name="next-steps"></a>다음 단계
이제에 연결 하는 기본적인 iOS Swift 앱을 **알림 허브** 를 통해 합니다 [REST API](/rest/api/notificationhubs/) 및 보내고 알림을 받을 수 있습니다. 자세한 내용은 다음 문서를 참조하세요. 

- [Azure Notification Hubs 개요](notification-hubs-push-notification-overview.md)
- [Notification Hubs REST API](/rest/api/notificationhubs/)
- [백 엔드 작업용 notification Hubs SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Notification Hubs SDK GitHub에서](https://github.com/Azure/azure-notificationhubs)
- [응용 프로그램 백 엔드를 사용 하 여 등록](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [등록 관리](notification-hubs-push-notification-registration-management.md)
- [태그 사용](notification-hubs-tags-segment-push-message.md) 
- [사용자 지정 템플릿 사용](notification-hubs-templates-cross-platform-push-messages.md)
- [공유 액세스 서명을 사용한 Service Bus 액세스 제어](../service-bus-messaging/service-bus-sas.md)
- [프로그래밍 방식으로 SAS 토큰을 생성 합니다.](/rest/api/eventhub/generate-sas-token)
- [Apple 보안: 일반 암호화](https://developer.apple.com/security/)
- [UNIX Epoch 시간](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
