---
title: 빠른 시작 - Azure Communication Services를 사용하여 iOS 앱에 Teams 미팅 참가 추가
description: 이 빠른 시작에서는 iOS용 Azure Communication Services Teams 포함 라이브러리를 사용하는 방법을 알아봅니다.
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 4d28864d41d6540afc87126daf589ed2929f891d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803872"
---
이 빠른 시작에서는 iOS용 Azure Communication Services Teams 포함 라이브러리를 사용하여 Teams 미팅에 참가하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작을 완료하려면 다음 필수 구성 요소가 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 키 집합에 설치된 유효한 개발자 인증서와 함께 [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)를 실행하는 Mac
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../create-communication-resource.md).
- [사용자 액세스 토큰](../../access-tokens.md)(Azure Communication Service용)
- [CocoaPods](https://cocoapods.org/)(빌드 환경에 설치됨)

## <a name="setting-up"></a>설치

### <a name="creating-the-xcode-project"></a>Xcode 프로젝트 만들기

Xcode에서 새 iOS 프로젝트를 만들고, **앱** 템플릿을 선택합니다. UIKit 스토리보드가 사용됩니다. 이 빠른 시작에서는 테스트를 만들지 않습니다. **테스트 포함** 을 선택 취소합니다.

:::image type="content" source="../media/ios/xcode-new-project-template-select.png" alt-text="Xcode 내에서 새 프로젝트 템플릿 선택을 보여주는 스크린샷":::

프로젝트 이름을 `TeamsEmbedGettingStarted`로 지정합니다.

:::image type="content" source="../media/ios/xcode-new-project-details.png" alt-text="Xcode 내에서 새 프로젝트 세부 정보를 보여주는 스크린샷":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>CocoaPods를 사용하여 패키지 및 종속성 설치

1. 애플리케이션에 대한 Podfile을 만듭니다.

```
platform :ios, '12.0'
use_frameworks!

target 'TeamsEmbedGettingStarted' do
    pod 'AzureCommunication', '~> 1.0.0-beta.8'
end

azure_libs = [
'AzureCommunication',
'AzureCore']

post_install do |installer|
    installer.pods_project.targets.each do |target|
    if azure_libs.include?(target.name)
        puts "Adding BUILD_LIBRARY_FOR_DISTRIBUTION to #{target.name}"
        target.build_configurations.each do |config|
        xcconfig_path = config.base_configuration_reference.real_path
        File.open(xcconfig_path, "a") {|file| file.puts "BUILD_LIBRARY_FOR_DISTRIBUTION = YES"}
        end
    end
    end
end
```

2. `pod install`을 실행합니다.
3. Xcode를 사용하여 생성된 `.xcworkspace`를 엽니다.

### <a name="request-access-to-the-microphone-camera-etc"></a>마이크, 카메라 등에 대한 액세스 요청

디바이스의 하드웨어에 액세스하려면 앱의 [정보 속성] 목록을 업데이트합니다. 연결된 값을 시스템에서 사용자로부터 액세스를 요청하는 데 사용하는 대화 상자에 포함될 `string`으로 설정합니다.

프로젝트 트리의 `Info.plist` 항목을 마우스 오른쪽 단추로 클릭하고 **다음 형식으로 열기** > **소스 코드** 를 선택합니다. 최상위 `<dict>` 섹션에 다음 줄을 추가한 다음, 파일을 저장합니다.

```xml
<key>NSBluetoothAlwaysUsageDescription</key>
<string></string>
<key>NSBluetoothPeripheralUsageDescription</key>
<string></string>
<key>NSCameraUsageDescription</key>
<string></string>
<key>NSContactsUsageDescription</key>
<string></string>
<key>NSMicrophoneUsageDescription</key>
<string></string>
```

### <a name="add-the-teams-embed-framework"></a>Teams 포함 프레임워크 추가

1. 프레임워크를 다운로드합니다.
2. 프로젝트 루트에서 `Frameworks` 폴더를 만듭니다. 예: `\TeamsEmbedGettingStarted\Frameworks\`
3. 다운로드한 `TeamsAppSDK.framework` 및 `MeetingUIClient.framework` 프레임워크를 이 폴더에 복사합니다.
4. `TeamsAppSDK.framework` 및 `MeetingUIClient.framework`를 일반 탭 아래의 프로젝트 대상에 추가합니다. 프레임워크 파일로 이동하여 추가하려면 `Add Other` -> `Add Files...`를 사용합니다.

:::image type="content" source="../media/ios/xcode-add-frameworks.png" alt-text="Xcode에서 추가된 프레임워크를 보여주는 스크린샷":::

5. 아직 추가되지 않은 경우 `$(PROJECT_DIR)/Frameworks`를 프로젝트 대상 빌드 설정 탭 아래의 `Framework Search Paths`에 추가합니다. 설정을 찾기 위해 필터를 `basic`에서 `all`로 변경했습니다. 오른쪽의 검색 창을 사용할 수도 있습니다.

:::image type="content" source="../media/ios/xcode-add-framework-search-path.png" alt-text="Xcode에서 프레임워크 검색 경로를 보여주는 스크린샷":::

### <a name="turn-off-bitcode"></a>Bitcode 해제

프로젝트 빌드 설정에서 `Enable Bitcode` 옵션을 `No`로 설정합니다. 설정을 찾기 위해 필터를 `basic`에서 `all`로 변경했습니다. 오른쪽의 검색 창을 사용할 수도 있습니다.

:::image type="content" source="../media/ios/xcode-bitcode-option.png" alt-text="Xcode에서 BitCode 옵션을 보여주는 스크린샷":::

### <a name="add-framework-signing-script"></a>프레임워크 서명 스크립트 추가

앱 대상, `Build Phases` 탭을 차례로 선택합니다. 그런 다음, `+`, `New Run Script Phase`를 차례로 클릭합니다. 이 새 단계가 `Embed Frameworks` 단계 이후에 발생하는지 확인합니다.



:::image type="content" source="../media/ios/xcode-build-script.png" alt-text="Xcode에서 빌드 스크립트 추가를 보여주는 스크린샷":::

```bash
#!/bin/sh
if [ -d "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks ]; then
    pushd "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks
    for EACH in *.framework; do
        echo "-- signing ${EACH}"
        /usr/bin/codesign --force --deep --sign "${EXPANDED_CODE_SIGN_IDENTITY}" --entitlements "${TARGET_TEMP_DIR}/${PRODUCT_NAME}.app.xcent" --timestamp=none $EACH
        echo "-- moving ${EACH}"
        mv -nv ${EACH} ../../
    done
    rm -rf "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks
    popd
    echo "BUILD DIR ${TARGET_BUILD_DIR}"
fi
```

### <a name="turn-on-voice-over-ip-background-mode"></a>Voice over IP 백그라운드 모드 설정

앱 대상을 선택하고, [기능] 탭을 클릭합니다.

위쪽에서 `+ Capabilities`를 클릭하여 `Background Modes`를 설정하고, `Background Modes`를 선택합니다.

`Voice over IP` 확인란을 선택합니다.

:::image type="content" source="../media/ios/xcode-background-voip.png" alt-text="Xcode에서 사용하도록 설정된 VOIP를 보여주는 스크린샷":::

### <a name="add-a-window-reference-to-appdelegate"></a>AppDelegate에 창 참조 추가

프로젝트의 **AppDelegate.swift** 파일을 열고, 'window'에 대한 참조를 추가합니다.

```swift
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?
```

### <a name="add-a-button-to-the-viewcontroller"></a>ViewController에 단추 추가

단추를 **ViewController.swift** 의 `viewDidLoad` 콜백에 만듭니다.

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    
    let button = UIButton(frame: CGRect(x: 100, y: 100, width: 200, height: 50))
    button.backgroundColor = .black
    button.setTitle("Join Meeting", for: .normal)
    button.addTarget(self, action: #selector(joinMeetingTapped), for: .touchUpInside)
    
    button.translatesAutoresizingMaskIntoConstraints = false
    self.view.addSubview(button)
    button.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    button.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
```

단추에 대한 콘센트를 **ViewController.swift** 에 만듭니다.

```swift
@IBAction func joinMeetingTapped(_ sender: UIButton) {

}
```

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

프로젝트의 **ViewController.swift** 파일을 열고, `AzureCommunication library` 및 `MeetingUIClient`를 가져오기 위한 `import` 선언을 파일 위쪽에 추가합니다. 

```swift
import UIKit
import AzureCommunication
import MeetingUIClient
```

사용자가 미팅에 참가할 수 있도록 `ViewController` 클래스 구현을 간단한 단추로 바꿉니다. 이 빠른 시작에서는 비즈니스 논리를 단추에 연결합니다.

```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    
    override func viewDidLoad() {
        super.viewDidLoad()

        // Initialize meetingClient
    }

    @IBAction func joinMeetingTapped(_ sender: UIButton) {
        joinMeeting()
    }
    
    private func joinMeeting() {
        // Add join meeting logic
    }
}
```

## <a name="object-model"></a>개체 모델

Azure Communication Services Teams 포함 라이브러리의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

| Name                                  | 설명                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingUIClient | MeetingUIClient는 Teams 포함 라이브러리의 주 진입점입니다. |
| MeetingUIClientDelegate | MeetingUIClientDelegate는 통화 상태 변경과 같은 이벤트를 받는 데 사용됩니다. |
| MeetingJoinOptions | MeetingJoinOptions는 표시 이름과 같은 구성 가능한 옵션에 사용됩니다. | 
| CallState | CallState는 통화 상태 변경 내용을 보고하는 데 사용됩니다. 옵션은 connecting, waitingInLobby, connected 및 ended입니다. |

## <a name="create-and-authenticate-the-client"></a>클라이언트 만들기 및 인증

미팅에 참가할 수 있는 사용자 액세스 토큰을 사용하여 `MeetingUIClient` 인스턴스를 초기화합니다. 다음 코드를 **ViewController.swift** 의 `viewDidLoad` 콜백에 추가합니다.

```swift
do {
    let communicationTokenRefreshOptions = CommunicationTokenRefreshOptions(initialToken: "<USER_ACCESS_TOKEN>", refreshProactively: true, tokenRefresher: fetchTokenAsync(completionHandler:))
    let credential = try CommunicationTokenCredential(with: communicationTokenRefreshOptions)
    meetingUIClient = MeetingUIClient(with: credential)
}
catch {
    print("Failed to create communication token credential")
}
```

`<USER_ACCESS_TOKEN>`을 리소스에 대한 유효한 사용자 액세스 토큰으로 바꿉니다. 사용할 수 있는 토큰이 아직 없는 경우 [사용자 액세스 토큰](../../access-tokens.md) 설명서를 참조하세요.

### <a name="setup-token-refreshing"></a>토큰 새로 고침 설정

`fetchTokenAsync` 메서드를 만듭니다. 그런 다음, 사용자 토큰을 가져오는 `fetchToken` 논리를 추가합니다.

```swift
private func fetchTokenAsync(completionHandler: @escaping TokenRefreshOnCompletion) {
    func getTokenFromServer(completionHandler: @escaping (String) -> Void) {
        completionHandler("<USER_ACCESS_TOKEN>")
    }
    getTokenFromServer { newToken in
        completionHandler(newToken, nil)
    }
}
```

`<USER_ACCESS_TOKEN>`을 리소스에 대한 유효한 사용자 액세스 토큰으로 바꿉니다.

## <a name="join-a-meeting"></a>미팅에 참가

`joinMeeting` 메서드는 *미팅 참가* 단추를 탭하면 수행되는 작업으로 설정됩니다. `MeetingUIClient`를 사용하여 미팅에 참가하도록 구현을 업데이트합니다.

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingJoinOptions(displayName: "John Smith")
        
    meetingUIClient?.join(meetingUrl: "<MEETING_URL>", meetingJoinOptions: meetingJoinOptions, completionHandler: { (error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
    })
}
```

`<MEETING URL>`을 Teams 미팅 링크로 바꿉니다.

### <a name="get-a-teams-meeting-link"></a>Teams 미팅 링크 가져오기

Teams 미팅 링크는 Graph API를 사용하여 검색할 수 있습니다. 자세한 내용은 [Graph 설명서](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)에서 설명하고 있습니다.
Communication Services Calling SDK는 전체 Teams 미팅 링크를 수락합니다. 이 링크는 [`joinWebUrl` 속성](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true)에서 액세스할 수 있는 `onlineMeeting` 리소스의 일부로 반환됩니다. Teams 미팅 초대 자체의 **미팅 조인** URL에서 필요한 미팅 정보를 가져올 수도 있습니다.

## <a name="run-the-code"></a>코드 실행

**제품** > **실행** 을 선택하거나 (&#8984;-R) 키보드 단축키를 사용하여 iOS 시뮬레이터에서 앱을 빌드하고 실행할 수 있습니다.

:::image type="content" source="../media/ios/quick-start-join-meeting.png" alt-text="빠른 시작 앱의 최종 모양과 느낌":::

:::image type="content" source="../media/ios/quick-start-meeting-joined.png" alt-text="미팅에 참가한 후의 최종 모양과 느낌":::

> [!NOTE]
> 처음으로 미팅에 참가하면 시스템에서 마이크에 액세스하라는 메시지를 표시합니다. 프로덕션 애플리케이션에서는 `AVAudioSession` API를 사용하여 [권한 상태를 확인](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources)하고 권한이 부여되지 않은 경우 애플리케이션의 동작을 정상적으로 업데이트해야 합니다.

## <a name="add-localization-support-based-on-your-app"></a>앱에 따라 지역화 지원 추가

Microsoft Teams SDK는 100개가 넘는 문자열과 리소스를 지원합니다. 프레임워크 번들에는 기본 및 영어 언어가 포함되어 있습니다. 나머지는 패키지에 포함된 `Localizations.zip` 파일에 포함되어 있습니다.

#### <a name="add-localizations-to-the-sdk-based-on-what-your-app-supports"></a>앱에서 지원하는 항목에 따라 SDK에 지역화 추가

1. 앱 Xcode 프로젝트 > 정보 > 지역화 목록에서 애플리케이션에서 지원하는 지역화의 종류를 결정합니다.
2. 패키지에 포함된 Localizations.zip의 압축을 풉니다.
3. 앱에서 지원하는 항목에 따라 압축을 푼 폴더의 지역화 폴더를 TeamsAppSDK.framework의 루트에 복사합니다.

## <a name="preparation-for-app-store-upload"></a>App Store 업로드 준비

보관의 경우 프레임워크에서 i386 및 x86_64 아키텍처를 제거합니다.

애플리케이션을 보관하려면 스크립트를 제거하는 `i386` 및 `x86_64` 아키텍처를 프레임워크 코드 서명 단계 앞의 빌드 단계에 추가합니다.

프로젝트 탐색기에서 프로젝트를 선택합니다. 편집기 창에서 빌드 단계로 이동하고, + 서명 → 새 실행 스크립트 만들기 단계를 차례로 클릭합니다.

```bash
echo "Target architectures: $ARCHS"
APP_PATH="${TARGET_BUILD_DIR}/${WRAPPER_NAME}"
find "$APP_PATH" -name '*.framework' -type d | while read -r FRAMEWORK
do
FRAMEWORK_EXECUTABLE_NAME=$(defaults read "$FRAMEWORK/Info.plist" CFBundleExecutable)
FRAMEWORK_EXECUTABLE_PATH="$FRAMEWORK/$FRAMEWORK_EXECUTABLE_NAME"
echo "Executable is $FRAMEWORK_EXECUTABLE_PATH"
echo $(lipo -info "$FRAMEWORK_EXECUTABLE_PATH")
FRAMEWORK_TMP_PATH="$FRAMEWORK_EXECUTABLE_PATH-tmp"
# remove simulator's archs if location is not simulator's directory
case "${TARGET_BUILD_DIR}" in
*"iphonesimulator")
    echo "No need to remove archs"
    ;;
*)
    if $(lipo "$FRAMEWORK_EXECUTABLE_PATH" -verify_arch "i386") ; then
    lipo -output "$FRAMEWORK_TMP_PATH" -remove "i386" "$FRAMEWORK_EXECUTABLE_PATH"
    echo "i386 architecture removed"
    rm "$FRAMEWORK_EXECUTABLE_PATH"
    mv "$FRAMEWORK_TMP_PATH" "$FRAMEWORK_EXECUTABLE_PATH"
    fi
    if $(lipo "$FRAMEWORK_EXECUTABLE_PATH" -verify_arch "x86_64") ; then
    lipo -output "$FRAMEWORK_TMP_PATH" -remove "x86_64" "$FRAMEWORK_EXECUTABLE_PATH"
    echo "x86_64 architecture removed"
    rm "$FRAMEWORK_EXECUTABLE_PATH"
    mv "$FRAMEWORK_TMP_PATH" "$FRAMEWORK_EXECUTABLE_PATH"
    fi
    ;;
esac
echo "Completed for executable $FRAMEWORK_EXECUTABLE_PATH"
echo $(lipo -info "$FRAMEWORK_EXECUTABLE_PATH")
done
```

## <a name="sample-code"></a>샘플 코드

샘플 앱은 [GitHub](https://github.com/Azure-Samples/teams-embed-ios-getting-started)에서 다운로드할 수 있습니다.
