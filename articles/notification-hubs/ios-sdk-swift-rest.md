---
title: Azure Notification Hubs 및 REST API를 사용하여 Swift iOS 앱에 푸시 알림 보내기
description: 이 자습서에서는 Azure Notification Hubs 및 REST API를 사용하여 푸시 알림을 iOS 디바이스에 보내는 방법을 알아봅니다.
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/03/2020
ms.openlocfilehash: 861011874c63f4c5d18ce33b107bbe9a80359045
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2020
ms.locfileid: "85127266"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-apis"></a>자습서: Notification Hubs REST API를 사용하여 Swift iOS 앱에 푸시 알림 보내기

이 자습서에서는 Azure Notification Hubs를 사용하여 REST API를 통해 푸시 알림을 iOS 애플리케이션에 보내는 방법에 대해 설명합니다.

이 자습서에서 다루는 단계는 다음과 같습니다.

- iOS 앱 샘플을 만듭니다.
- iOS 앱을 Azure Notification Hubs에 연결합니다.
- 테스트 푸시 알림을 보냅니다.
- 앱에서 알림을 받는지 확인합니다.

이 자습서의 전체 코드는 [GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/v3-preview2/Samples)에서 다운로드할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

- 키 집합에 설치된 유효한 개발자 인증서와 함께 [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)를 실행하는 Mac

- iOS 버전 10 이상을 실행하는 iPhone 또는 iPad

- [Apple Portal](https://developer.apple.com/)에 등록되고 인증서와 연결된 물리적 디바이스

계속 진행하기 전에 이전 자습서에서 [iOS 앱용 Azure Notification Hubs](https://go.microsoft.com/fwlink/?linkid=2129801)를 시작하여 알림 허브에서 푸시 자격 증명을 설정하고 구성하는 방법에 대한 이전 자습서를 수행해야 합니다. 이전에 iOS를 개발한 경험이 없더라도 이러한 단계를 수행할 수 있습니다.

> [!NOTE]
> 푸시 알림에 대한 구성 요구 사항으로 인해 iOS 시뮬레이터 대신 실제 iOS 디바이스(iPhone 또는 iPad)에서 푸시 알림을 배포하고 테스트해야 합니다.

다음 섹션에서는 알림 허브에 연결하는 iOS 앱을 빌드합니다.

## <a name="create-an-ios-project"></a>iOS 프로젝트 만들기

1. Xcode에서 새 iOS 프로젝트를 만들고 **애플리케이션 단일 보기** 템플릿을 선택합니다.

2. 새 프로젝트에 대한 옵션을 설정하는 경우 다음을 수행합니다.
   - Apple Developer 포털에서 **번들 식별자**를 설정할 때 사용한 **제품 이름**(PushDemo) 및 **조직 식별자**(`com.<organization>`)를 지정합니다.
   -  **앱 ID**가 설정된 **팀**을 선택합니다.
   -  **언어**를  **Swift**로 설정합니다.
   -  **다음**을 선택합니다.

3.  **SupportingFiles**라는 새 폴더를 만듭니다.

4.  **devsettings.plist**라는 새 p-list 파일을 **SupportingFiles** 폴더에 만듭니다. git 리포지토리를 사용할 때 커밋되지 않도록 이 폴더를 **gitignore** 파일에 추가해야 합니다. 프로덕션 앱에서 이러한 비밀을 자동화된 빌드 프로세스의 일부로 조건부로 설정할 수 있습니다. 이러한 설정은 이 자습서에서 다루지 않습니다.

5. 프로비저닝한 알림 허브의 사용자 고유의 값을 사용하여 다음 구성 항목을 포함하도록 **devsettings.plist**를 업데이트합니다.

   | **Key**                  | **형식** | **값**        |
   | ------------------------ | -------- | ---------------- |
   | notificationHubKey       | String   | `<hubKey>`       |
   | notificationHubKeyName   | String   | `<hubKeyName>`   |
   | notificationHubName      | String   | `<hubName>`      |
   | notificationHubNamespace | String   | `<hubNamespace>` |

   Azure Portal에서 알림 허브 리소스로 이동하여 필요한 값을 확인할 수 있습니다. 특히 **notificationHubName** 및 **notificationHubNamespace** 값은 **개요** 페이지에 있는 **기본 정보** 요약의 오른쪽 위 모서리에 있습니다.

   :::image type="content" source="media/ios-sdk-swift-rest/image1.png" alt-text="필요한 값":::

   또한 **액세스 정책**으로 이동하고 각 **액세스 정책**(예: **DefaultFullSharedAccessSignature**)을 선택하여 **notificationHubKeyName** 및 **notificationHubKey** 값을 확인할 수도 있습니다. 그런 다음, **기본 연결 문자열**에서  **notificationHubKeyName**에 대해 `SharedAccessKeyName=`을 접두사로 사용하는 값 및 **notificationHubKey**에 대해 `SharedAccessKey=`를 접두사로 사용하는 값을 복사합니다. 연결 문자열의 형식은 다음과 같습니다.

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   간단히 하기 위해 토큰을 사용하여 알림을 보낼 수 있도록 **DefaultFullSharedAccessSignature**를 지정합니다. 실제로 **DefaultListenSharedAccessSignature**는 알림을 받으려는 경우에만 선택하는 것이 좋습니다.

6.  **프로젝트 탐색기** 아래에서 **프로젝트 이름**을 선택한 다음, **일반** 탭을 선택합니다.

7.  **ID**를 찾은 다음, 이전 단계에서 **앱 ID**에 사용된 값인 `com.<organization>.PushDemo`와 일치하도록 **번들 식별자** 값을 설정합니다.

8.  **서명 및 기능**을 찾은 다음, **Apple Developer 계정**에 적절한 **팀**을 선택합니다.  **팀** 값은 인증서와 프로필을 만든 값과 일치해야 합니다.

9. Xcode는 **번들 식별자**에 따라 적절한 **프로비저닝 프로필** 값을 자동으로 다운로드합니다. 새 **프로비저닝 프로필** 값이 표시되지 않으면 **Xcode**, **기본 설정**, **계정**을 차례로 선택하여  **서명 ID** 에 대한 프로필을 새로 고친 다음,  **수동 프로필 다운로드** 단추를 선택하여 프로필을 다운로드합니다.

10. 여전히 **서명 및 기능** 탭에서 **+ 기능**  단추를 클릭하고, 목록에서 **푸시 알림**을 두 번 탭하여 **푸시 알림**을 사용하도록 설정되었는지 확인합니다.

11.  **AppDelegate.swift** 파일을 열어 **UNUserNotificationCenterDelegate** 프로토콜을 구현하고, 다음 코드를 클래스 위쪽에 추가합니다.

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

    이러한 멤버는 나중에 사용합니다. 특히 **사용자 지정 템플릿**을 사용하여 등록의 일부로 **tags**  멤버를 사용합니다. 태그에 대한 자세한 내용은 [등록에 대한 태그](notification-hubs-tags-segment-push-message.md) 및 [템플릿 등록](notification-hubs-templates-cross-platform-push-messages.md)을 참조하세요.

12. 동일한 파일에서 다음 코드를 **didFinishLaunchingWithOptions** 함수에 추가합니다.

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

    이 코드는 **devsettings.plist**에서 설정을 검색하고, **AppDelegate** 클래스를 ** UNUserNotificationCenter** 대리자로 설정하고, 푸시 알림에 대한 권한 부여를 요청한 다음, **registerForRemoteNotifications**를 호출합니다.

    간단히 하기 위해 이 코드는 iOS 10 이상만 지원합니다. 평소와 같이 각 API 및 방법을 조건부로 사용하여 이전 iOS 버전에 대한 지원을 추가할 수 있습니다.

13. 동일한 파일에서 다음 코드를 추가합니다.

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

    이 코드는 **installationId** 및 **pushChannel** 값을 사용하여 알림 허브에 등록합니다. 이 경우 **UIDevice.current.identifierForVendor**를 사용하여 디바이스를 식별하는 고유한 값을 제공한 다음, 원하는 **pushChannel** 값을 제공하도록 **deviceToken**의 형식을 지정합니다.  **showAlert** 함수는 데모용으로 일부 메시지 텍스트를 표시하기 위해서만 존재합니다.

14. 여전히 **AppDelegate.swift** 파일에서 **willPresent** 및 **didReceive** 함수를 **UNUserNotificationCenterDelegate**에 추가합니다. 이러한 함수는 앱이 포그라운드 또는 백그라운드에서 실행되고 있다는 알림을 받을 때 경고를 표시합니다.

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

15. `print` 문을 **didRegisterForRemoteNotificationsWithDeviceToken** 함수 아래쪽에 추가하여 값이 **installationId** 및 **pushChannel**에 할당되는지 확인합니다.

    ```swift
    print(installationId)
    print(pushChannel)
    ```

16. 나중에 프로젝트에 추가할 기본 구성 요소에 대한 **Models**, **Services** 및 **Utilities** 폴더를 만듭니다.

17. 프로젝트가 물리적 디바이스에서 빌드되고 실행되는지 확인합니다. 푸시 알림은 시뮬레이터를 사용하여 테스트할 수 없습니다.

## <a name="create-models"></a>모델 만들기

이 단계에서는 [Notification Hubs REST API](/rest/api/notificationhubs/) 페이로드를 나타내고 필요한 SAS(공유 액세스 서명) 토큰 데이터를 저장하는 모델 세트를 만듭니다.

1.  **PushTemplate.swift**라는 새 Swift 파일을 **Models** 폴더에 추가합니다. 이 모델은 **DeviceInstallation** 페이로드의 일부로 개별 템플릿의 **본문**을 나타내는 구조체를 정의합니다.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2.  **DeviceInstallation.swift**라는 새 Swift 파일을 **Models** 폴더에 추가합니다. 이 파일은 **디바이스 설치**를 만들거나 업데이트하기 위한 페이로드를 나타내는 구조체를 정의합니다. 파일에 다음 코드를 추가합니다.

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

3.  **TokenData.swift**라는 새 Swift 파일을 **Models** 폴더에 추가합니다. 이 모델은 SAS 토큰을 만료와 함께 저장하는 데 사용됩니다. 파일에 다음 코드를 추가합니다.

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

## <a name="generate-a-sas-token"></a>SAS 토큰 생성

Notification Hubs는 Azure Service Bus와 동일한 보안 인프라를 사용합니다. REST API를 호출하려면 요청의 **Authorization** 헤더에서 사용할 수 있는 [SAS 토큰을 프로그래밍 방식으로 생성합니다](/rest/api/eventhub/generate-sas-token). 

결과 토큰의 형식은 다음과 같습니다.

```xml
SharedAccessSignature sig=\<UrlEncodedSignature\>\&se=\<ExpiryEpoch\>\&skn=\<KeyName\>\&sr=\<UrlEncodedResourceUri\> |
```

프로세스 자체에는 다음과 같은 동일한 6개 단계가 포함됩니다.

1. 만료 시간을 [UNIX Epoch 시간](https://en.wikipedia.org/wiki/Unix_time) 형식으로 계산합니다. 즉, 1970년 1월 1일 자정 UTC(협정 세계시) 이후 경과된 시간(초)입니다.

2. 액세스하려는 리소스를 나타내는 **ResourceUrl**의 형식을 퍼센트로 인코딩하고 소문자가 되도록 지정합니다.  **ResourceUrl**의 형식은 `https://<namespace>.servicebus.windows.net/<hubName>`입니다.

3. `<UrlEncodedResourceUrl>\n<ExpiryEpoch>` 형식의 **StringToSign**을 준비합니다.

4.  **StringToSign** 값의 HMAC-SHA256 해시를 사용하여 **서명**을 계산하고 Base64로 인코딩합니다. 해시 값은 해당 **권한 부여 규칙**에 대한 **연결 문자열**의  **키** 부분과 함께 사용됩니다.

5. Base64로 인코딩된 **서명**의 형식을 퍼센트로 인코딩되도록 지정합니다.

6.  **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName** 및 **UrlEncodedResourceUrl** 값을 사용하여 토큰을 필요한 형식으로 생성합니다.

공유 액세스 서명에 대한 자세한 개요와 Azure Service Bus 및 Notification Hubs에서 이를 사용하는 방법에 대한 자세한 내용은 [Azure Service Bus 설명서](../service-bus-messaging/service-bus-sas.md)를 참조하세요.

이 Swift 예제의 목적을 위해 Apple 오픈 소스 **CommonCrypto** 라이브러리를 사용하여 서명 해시를 지원합니다. 이는 C 라이브러리이므로 기본적으로 Swift에서 액세스할 수 없습니다. 브리징 헤더를 사용하여 라이브러리를 사용할 수 있습니다.

브리징 헤더를 추가하고 구성하려면 다음을 수행합니다.

1. Xcode에서 **파일**, **새로 만들기**, **파일**을 차례로 선택한 다음, **헤더 파일**을 선택합니다. 헤더 파일 이름을 **BridgingHeader.h**로 지정합니다.

2.  **CommonHMAC.h**를 가져오도록 파일을 편집합니다.

   ```swift
   #import <CommonCrypto/CommonHMAC.h>

   #ifndef BridgingHeader_h
   #define BridgingHeader_h

   #endif /* BridgingHeader_h */
   ```

3. 브리징 헤더를 참조하도록 대상 **빌드 설정**을 업데이트합니다.

   1.  **PushDemo** 프로젝트를 선택하고, **Swift 컴파일러** 섹션까지 아래로 스크롤합니다.

   2.  **Objective-C 호환성 헤더 설치** 옵션이 **예**로 설정되어 있는지 확인합니다.

   3.  **Objective-C 브리징 헤더** 옵션에서 `<ProjectName>/BridgingHeader.h` 파일 경로를 입력합니다. 이는 브리징 헤더의 파일 경로입니다.

   이러한 옵션을 찾을 수 없는 경우 **기본** 또는 **사용자 지정**이 아니라 **모두** 보기가 선택되어 있는지 확인합니다.

    **CommonCrypto**를 조금 더 쉽게 사용할 수 있는 여러 타사 오픈 소스 래퍼 라이브러리가 있습니다. 그러나 이러한 라이브러리에 대한 설명은 이 문서의 범위를 벗어납니다.

4.  **TokenUtility.swift**라는 새 Swift 파일을 **Utilities** 폴더 내에 추가하고 다음 코드를 추가합니다.

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

   이 유틸리티는 SAS 토큰을 생성해야 하는 논리를 캡슐화합니다.

   앞에서 설명한 대로 **getSasToken** 함수는 토큰을 준비하는 데 필요한 개략적인 단계를 오케스트레이션합니다. 이 함수는 이 자습서의 뒷부분에 나오는 설치 서비스에서 호출됩니다.

   다른 두 함수, 즉 서명 계산용 **sha256HMac** 및 관련 URL 문자열 인코딩용 **urlEncodedString**은  **getSasToken** 함수에서 호출됩니다. 기본 제공 **addingPercentEncoding** 함수를 사용하여 필요한 출력을 얻을 수 없으므로 **urlEncodedString** 함수가 필요합니다.

    [Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m)는 Objective-C에서 이러한 작업에 접근하는 방법에 대한 훌륭한 예입니다. Azure Service Bus SAS 토큰에 대한 자세한 내용은 [Azure Service Bus 설명서](../service-bus-messaging/service-bus-sas.md)를 참조하세요.

5.  **AppDelegate.swift**에서 다음 코드를 `didRegisterForRemoteNotificationsWithDeviceToken` 함수에 추가하여 **TokenUtility.getSasToken**에서 유효한 토큰을 생성하는지 확인합니다.

   ```swift
   let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

   let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                            withKeyName: self.notificationHubKeyName!,
                                            andKey: self.notificationHubKey!)

   print(tokenData.token)
   ```

    **baseAddress** 문자열의 자리 표시자를 사용자 고유의 값으로 바꿔야 합니다.

## <a name="verify-the-sas-token"></a>SAS 토큰 확인

클라이언트에서 설치 서비스를 구현하기 전에 HTTP 유틸리티를 사용하여 앱에서 SAS 토큰을 올바르게 생성하는지 확인합니다. 이 자습서의 목적을 위해 **Postman** 도구를 선택합니다.

앱에서 생성된 **installationId** 및 **token** 값을 적어 둡니다.

다음 단계에 따라 **설치** API를 호출합니다.

1.  **Postman**에서 새 탭을 엽니다.
2. 요청을 **GET**으로 설정하고 다음 주소를 지정합니다.

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
   ```

3. 요청 헤더를 다음과 같이 구성합니다.

   | **Key**       | **값**        |
   | ------------- | ---------------- |
   | 콘텐츠 형식  | application/json |
   | 권한 부여 | \<sasToken\>     |
   | x-ms-version  | 2015-01          |

4. 오른쪽 위의 **저장** 단추 아래에 표시되는 **코드** 단추를 선택합니다. 요청은 다음 예제와 비슷합니다.

   ```xml
   GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
   Host: <namespace>.servicebus.windows.net
   Content-Type: application/json
   Authorization: <sasToken>
   x-ms-version: 2015-01
   Cache-Control: no-cache
   Postman-Token: <postmanToken>
   ```

5.  **보내기** 단추를 선택합니다.

이 시점에서 지정된 **installationId**에 대한 등록이 없습니다. 확인하면 "401 권한 없음" 응답 대신 "404 찾을 수 없음" 응답이 표시됩니다. 이 결과를 통해 SAS 토큰이 수락되었는지 확인할 수 있습니다.

## <a name="implement-the-installation-service-class"></a>설치 서비스 클래스 구현

다음으로, [설치 REST API](/rest/api/notificationhubs/create-overwrite-installation)에 대한 기본 래퍼를 구현합니다.

 **NotificationRegistrationService.swift**라는 새 Swift 파일을 **Services** 폴더 아래에 추가하고, 다음 코드를 이 파일에 추가합니다.

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

필수 세부 정보는 초기화의 일부로 제공됩니다. 태그 및 템플릿은 필요에 따라 **register** 함수에 전달되어 **디바이스 설치** JSON 페이로드의 일부를 구성합니다.

 **register** 함수는 다른 프라이빗 함수를 호출하여 요청을 준비합니다. 응답이 수신되면 완료가 호출되고 등록이 성공했는지 여부를 나타냅니다.

요청 엔드포인트는 **getBaseAddress** 함수에서 생성됩니다. 이 생성에서는 초기화 중에 제공된 *namespace* 및 *name* 알림 허브 매개 변수를 사용합니다.

 **getSasToken** 함수는 현재 저장된 토큰이 유효한지 여부를 확인합니다. 토큰이 유효하지 않으면 이 함수에서 **TokenUtility**를 호출하여 새 토큰을 생성한 다음, 저장한 후에 값을 반환합니다.

마지막으로, **encodeToJson**는 요청 본문의 일부로 사용하기 위해 해당 모델 개체를 JSON으로 변환합니다.

## <a name="invoke-the-notification-hubs-rest-api"></a>Notification Hubs REST API 호출

마지막 단계는 **NotificationRegistrationService**를 사용하여 **NotificationHub**에 등록하도록 **AppDelegate**를 업데이트하는 것입니다.

1.  **AppDelegate.swift**를 열고, 클래스 수준 변수를 추가하여 **NoficiationRegistrationService** 및 제네릭 **PushTemplate**에 대한 참조를 저장합니다.

   ```swift
   var registrationService : NotificationRegistrationService?
   let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
   ```

2. 동일한 파일에서 **NotificationRegistrationService**를 필수 매개 변수로 초기화한 다음, **register** 함수를 호출하도록 **didRegisterForRemoteNotificationsWithDeviceToken** 함수를 업데이트합니다.

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

   간단히 하기 위해 이 코드에서는 `print` 문을 사용하여 출력 창을 **register** 작업의 결과로 업데이트합니다.

3. 물리적 디바이스에서 앱을 빌드하고 실행합니다. 출력 창에 **등록됨**이 표시됩니다.

## <a name="test-the-solution"></a>솔루션 테스트

이 시점에서 앱이 **NotificationHub**에 등록되고 푸시 알림을 받을 수 있습니다. Xcode에서 디버거를 중지하고 앱이 현재 실행되고 있는 앱을 닫습니다. 다음으로 **디바이스 설치** 세부 정보가 예상대로 표시되고 이제 앱에서 푸시 알림을 받을 수 있는지 확인합니다.

### <a name="verify-the-device-installation"></a>디바이스 설치 확인

이제 **Postman**에서  [SAS 토큰을 확인](notification-hubs-ios-push-notifications-swift-apps-get-started.md#verify-the-sas-token)하여 이전과 동일한 요청을 수행할 수 있습니다. 이제 SAS 토큰이 만료되지 않았다고 가정하면 제공한 설치 세부 정보(예: 템플릿 및 태그)가 응답에 포함되어야 합니다.

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

이전 SAS 토큰이 만료된 경우 중단점을 **TokenUtility** 클래스의 24번째 줄에 추가하여 새 SAS 토큰을 가져오고, **Authorization** 헤더를 해당 새 값으로 업데이트할 수 있습니다.

### <a name="send-a-test-notification-azure-portal"></a>테스트 알림 보내기(Azure Portal)

이제 알림을 받을 수 있는지 테스트하는 가장 빠른 방법은 Azure Portal에서 알림 허브로 이동하는 것입니다.

1. Azure Portal에서 알림 허브의 **개요** 탭으로 이동합니다.

2. 포털 창의 왼쪽 위에 있는 **기본 정보** 요약 위에서 **테스트 보내기**를 선택합니다.

   :::image type="content" source="media/ios-sdk-swift-rest/image2.png" alt-text="Notification Hubs 기본 정보 요약 테스트 보내기":::

3.  **플랫폼** 목록에서 **사용자 지정 템플릿**을 선택합니다.

4.  **태그 식으로 보내기**에 대해 **12345**를 입력합니다. 이 태그는 이전에 설치에서 지정되었습니다.

5. 필요에 따라 JSON 페이로드에서 **message**를 편집합니다.

   :::image type="content" source="media/ios-sdk-swift-rest/image3.png" alt-text="Notification Hubs 테스트 보내기":::

6.  **보내기**를 선택합니다. 포털에서 알림을 디바이스에 성공적으로 보냈는지 여부가 표시됩니다.

   :::image type="content" source="media/ios-sdk-swift-rest/image4.png" alt-text="테스트 보내기 결과":::

   앱이 포그라운드에서 실행되지 않는다고 가정하면 디바이스의 **알림 센터**에도 알림이 표시됩니다. 알림을 탭하면 앱이 열리고 경고가 표시됩니다.

   :::image type="content" source="media/ios-sdk-swift-rest/image5.png" alt-text="테스트 알림":::

### <a name="send-a-test-notification-mail-carrier"></a>테스트 알림 보내기(메일 이동 통신 사업자)

 **Postman**을 사용하여 [REST API](/rest/api/notificationhubs/)를 통해 알림을 보낼 수 있으며, 이를 통해 더 편리하게 테스트할 수 있습니다.

1.  **Postman**에서 새 탭을 엽니다.

2. 요청을 **POST**로 설정하고 다음 주소를 입력합니다.

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
   ```

3. 요청 헤더를 다음과 같이 구성합니다.

   | 키                           | 값                          |
   | ----------------------------- | ------------------------------ |
   | 콘텐츠 형식                  | application/json;charset=utf-8 |
   | 권한 부여                 | \<sasToken\>                   |
   | ServiceBusNotification-Format | template                       |
   | 태그들                          | "12345"                        |

4. 다음 JSON 페이로드와 함께 **RAW - JSON (application.json)** 을 사용하도록 요청 **본문**을 구성합니다.

   ```json
   {
   "message" : "Hello from Postman!"
   }
   ```

5. 창의 오른쪽 위에 있는 **저장** 단추 아래에서  **코드** 단추를 선택합니다. 요청은 다음 예제와 비슷합니다.

   ```xml
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

6.  **보내기** 단추를 선택합니다.

클라이언트 디바이스에서  **201 만들어짐** 성공 상태 코드가 표시되고 알림을 받습니다.

## <a name="next-steps"></a>다음 단계

이제 [Notification Hubs REST API](/rest/api/notificationhubs/)를 통해 알림 허브에 연결되는 기본 iOS Swift 앱이 있으며, 알림을 보내고 받을 수 있습니다. 알림을 특정 디바이스에 보내는 방법에 대해 자세히 알아보려면 다음 자습서로 계속 진행하세요.

- [자습서: 특정 디바이스에 푸시 알림](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

자세한 내용은 다음 문서를 참조하세요.

- [Azure Notification Hubs 개요](notification-hubs-push-notification-overview.md)
- [Notification Hubs REST API](/rest/api/notificationhubs/)
- [백 엔드 작업에 대한 Notification Hubs SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [GitHub의 Notification Hubs SDK](https://github.com/Azure/azure-notificationhubs)
- [애플리케이션 백 엔드에 등록](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [등록 관리](notification-hubs-push-notification-registration-management.md)
- [태그 사용](notification-hubs-tags-segment-push-message.md)
- [사용자 지정 템플릿 사용](notification-hubs-templates-cross-platform-push-messages.md)
- [공유 액세스 서명을 사용한 Service Bus 액세스 제어](../service-bus-messaging/service-bus-sas.md)
- [프로그래밍 방식으로 SAS 토큰 생성](/rest/api/eventhub/generate-sas-token)
- [Apple 보안: 일반적인 암호화](https://developer.apple.com/security/)
- [UNIX Epoch 시간](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
