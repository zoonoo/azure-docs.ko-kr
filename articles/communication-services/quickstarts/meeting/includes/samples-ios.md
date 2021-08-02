---
title: iOS용 Azure Communication Services Teams Embed 사용
description: 이 개요에서는 iOS용 Azure Communication Services Teams Embed 라이브러리를 사용하는 방법을 알아봅니다.
author: palatter
ms.author: palatter
ms.date: 24/02/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 1a6c8d05da04dc0f32fb278baf946ea363010903
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111546433"
---
## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../create-communication-resource.md).
- 호출 클라이언트를 사용하도록 설정하는 `User Access Token`입니다. [`User Access Token`를 가져오는 방법](../../access-tokens.md)에 대한 자세한 정보
- [애플리케이션에 Teams Embed 추가 시작](../getting-started-with-teams-embed.md)에 대한 빠른 시작을 완료합니다.

## <a name="teams-embed-call-or-meeting-status-events-capturing"></a>Teams Embed 통화 또는 모임 상태 이벤트 캡처

참여한 그룹 통화 또는 모임 상태는 `MeetingUIClientCallDelegate` 대리자로부터 캡처할 수 있습니다. 상태에는 연결 상태, 참가자 수, 그리고 마이크 또는 카메라 상태와 같은 형식이 포함됩니다.   

`MeetingUIClientCallDelegate`를 클래스에 추가하고 필요한 변수를 추가합니다.

```swift
class ViewController: UIViewController, MeetingUIClientCallDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
    
    //Rest of the UIViewController code
}
```

통화 또는 모임에 성공적으로 참여하기 시작한 후 `self.meetingUIClientCall?.meetingUIClientCallDelegate`를 `self`로 설정합니다.

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```

앱에 필요한 `MeetingUIClientCallDelegate` 프로토콜 메서드를 구현하고 필요하지 않은 메서드에는 스텁을 추가합니다.

```swift
    func meetingUIClient(didUpdateCallState callState: MeetingUIClientCallState) {
        switch callState {
        case .connecting:
            print("Call state has changed to 'Connecting'")
        case .connected:
            print("Call state has changed to 'Connected'")
        case .waitingInLobby:
            print("Call state has changed to 'Waiting in Lobby'")
        case .ended:
            print("Call state has changed to 'Ended'")
        }
    }
    
    func meetingUIClient(didUpdateRemoteParticipantCount remoteParticipantCount: UInt) {
        print("Remote participant count has changed to: \(remoteParticipantCount)")
    }
    
    func onIsMutedChanged() {
    }
    
    func onIsSendingVideoChanged() {
    }
    
    func onIsHandRaisedChanged(_ participantIds: [Any]) {
    }
```

## <a name="bring-your-own-identity-from-the-app-to-the-participants-in-the-sdk-call"></a>앱에서 SDK 통화의 참가자로 고유한 ID를 가져옵니다.

앱은 통화 또는 모임의 참가자에게 사용자 ID 값을 할당하고 기본값을 재정의할 수 있습니다. 값에는 아바타, 이름, 부제목, 역할이 포함됩니다.  

### <a name="assigning-avatars-for-call-participants"></a>통화 참가자용 아바타 할당

클래스에 `MeetingUIClientCallIdentityProviderDelegate`를 추가합니다.

```swift
class ViewController: UIViewController, MeetingUIClientCallIdentityProviderDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

통화 또는 모임에 성공적으로 참여하기 시작한 후 `self.meetingUIClientCall?.MeetingUIClientIdentityProviderDelegate`를 `self`로 설정합니다.

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallIdentityProviderDelegate = self
            }
        }
    })
}
```

`avatarFor` 프로토콜 메서드를 추가 및 구현하고 각 `identifier`를 해당 아바타에 매핑합니다.

```swift
    func avatarFor(identifier: CommunicationIdentifier, size: MeetingUIClientAvatarSize, completionHandler: @escaping (UIImage?) -> Void) {
        if let userIdentifier = identifier as? CommunicationUserIdentifier
        {
            if (userIdentifier.identifier.starts(with: "8:teamsvisitor:")) {
                // Anonymous teams user will start with prefix 8:teamsvistor:
                let image = UIImage (named: "avatarPink")
                completionHandler(image!)
            }
            else if (userIdentifier.identifier.starts(with: "8:orgid:")) {
                // OrgID user will start with prefix 8:orgid:
                let image = UIImage (named: "avatarDoctor")
                completionHandler(image!)
            }
            else if (userIdentifier.identifier.starts(with: "8:acs:")) {
                // ACS user will start with prefix 8:acs:
                let image = UIImage (named: "avatarGreen")
                completionHandler(image!)
            } else {
                completionHandler(nil)
            }
        } else {
            completionHandler(nil)
        }
    }

```

다른 필수 MeetingUIClientCallIdentityProviderDelegate 프로토콜 메서드를 클래스에 추가하면 빈 구현으로 남아 있을 수 있습니다.
```swift
    func displayNameFor(identifier: CommunicationIdentifier, completionHandler: @escaping (String?) -> Void) {
    }
    
    func subTitleFor(identifier: CommunicationIdentifier, completionHandler: @escaping (String?) -> Void) {
    }
    
    func roleFor(identifier: CommunicationIdentifier, completionHandler: @escaping (MeetingUIClientUserRole) -> Void) {
    }
```

## <a name="use-teams-embed-sdk-and-azure-communication-calling-sdk-in-the-same-app"></a>동일한 앱에서 Teams Embed SDK와 Azure Communication Calling SDK 사용

Teams Embed SDK는 ACS(Azure Communication Calling SDK)도 제공하므로 동일한 앱에서 두 SDK 기능을 모두 사용할 수 있습니다. 한 번에 하나의 SDK만 초기화하고 사용할 수 있습니다. 두 SDK를 모두 초기화하고 동시에 사용하면 예기치 않은 동작이 발생합니다. 

Teams Embed SDK에서 Azure Communication Calling SDK에 액세스하려면 `TeamsAppSDK`를 가져옵니다. 
```swift
import TeamsAppSDK
```

ACS 사용에 대한 변수 선언
```swift
class ViewController: UIViewController {

    private var callClient: CallClient?
    private var callAgent: CallAgent?
    private var call: Call?
    
    //Rest of the UIViewController code
}
    
```

초기화는 새 `CallClient`를 만들어 수행합니다. 만든 항목을 `viewDidLoad` 또는 다른 메서드에 추가합니다.

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    self.callClient = CallClient()
}
```
해당 설명서에 설명된 것처럼 모든 ACS API를 사용합니다. API 사용에 대해서는 이 설명서에서 다루지 않습니다. 

사용이 더 이상 필요하지 않거나 앱이 Teams Embed SDK를 사용해야 하는 경우 ACS SDK를 삭제하고 `nil`을 해당 변수로 설정합니다.
```swift
    public func disposeAcsSdk()
    {
        self.call = nil
        self.callAgent?.dispose()
        self.callClient?.dispose()
        self.callAgent = nil
        self.callClient = nil
    }

```

`MeetingUIClient`를 만드는 동안에는 Teams Embed SDK 초기화도 이루어집니다. 만든 항목을 `viewDidLoad` 또는 다른 메서드에 추가합니다.
```swift
override func viewDidLoad() {
    super.viewDidLoad()
    do {
        let communicationTokenRefreshOptions = CommunicationTokenRefreshOptions(initialToken: "<USER_ACCESS_TOKEN>", refreshProactively: true, tokenRefresher: fetchTokenAsync(completionHandler:))
        let credential = try CommunicationTokenCredential(withOptions: communicationTokenRefreshOptions)
        meetingUIClient = MeetingUIClient(with: credential)
    }
    catch {
        print("Failed to create communication token credential")
    }
}

private func fetchTokenAsync(completionHandler: @escaping TokenRefreshHandler) {
    func getTokenFromServer(completionHandler: @escaping (String) -> Void) {
        completionHandler("<USER_ACCESS_TOKEN>")
    }
    getTokenFromServer { newToken in
        completionHandler(newToken, nil)
    }
}

```
해당 설명서에 설명된 것처럼 Teams Embed SDK API를 사용합니다. API 사용에 대해서는 이 설명서에서 다루지 않습니다. 

사용이 더 이상 필요하지 않거나 앱이 ACS SDK를 사용해야 하는 경우 Teams Embed SDK를 삭제하고 `nil`을 해당 변수로 설정합니다.
```swift
    private func disposeTeamsSdk() {
        self.meetingUIClient?.dispose(completionHandler: { (error: Error?) in
            if (error != nil) {
                print("Dispose failed: \(error!)")
            } else {
                self.meetingUIClient = nil
                self.meetingUIClientCall = nil
            }
        })        
    }

```

Teams Embed SDK 삭제는 통화가 진행 중이 아닐 때만 가능합니다. 통화가 진행 중인 경우 `meetingUIClient?.dispose`가 완료 처리기에서 오류를 반환합니다. 통화를 중단하고 진행 중인 통화가 없으면 `self.disposeTeamsSdk()`를 호출합니다.
```swift
    
    private var shouldDispose: Bool = false

    public func endMeeting() {
        meetingUIClientCall?.hangUp(completionHandler: { (error: Error?) in
            if (error != nil) {
                print("End meeting failed: \(error!)")
                // There are no active calls
                self.disposeTeamsSdk()
            } else {
                // Ending active call is in progress.
                self.shouldDispose = true;
            }
        })
    }

```

`MeetingUIClientCallDelegate` 프로토콜 메서드 `meetingUIClientCall(didUpdateCallState callState: MeetingUIClientCallState)`를 구현하여 종료 중인 통화에 대한 상태 업데이트를 가져오고 이후 Teams Embed SDK를 삭제합니다.
```swift
    func meetingUIClientCall(didUpdateCallState callState: MeetingUIClientCallState) {
        switch callState {
        case .connecting:
        case .connected:
        case .waitingInLobby:
        case .ended:
            if (self.shouldDispose) {
                self.disposeTeamsSdk()
            }
        @unknown default:
            print("Unsupported state")
        }
    }
```

다른 필수 `MeetingUIClientCallDelegate` 프로토콜 메서드를 클래스에 추가합니다.

```swift
    func meetingUIClientCall(didUpdateRemoteParticipantCount remoteParticipantCount: UInt) {
        print("Remote participant count has changed to: \(remoteParticipantCount)")
    }

    func onIsMutedChanged() {
        print("Mute state changed to: \(meetingUIClientCall?.isMuted ?? false)")
    }
    
    func onIsSendingVideoChanged() {
        print("Sending video state changed to: \(meetingUIClientCall?.isSendingVideo ?? false)")
    }
    
    func onIsHandRaisedChanged(_ participantIds: [Any]) {
        print("Self participant raise hand status changed to: \(meetingUIClientCall?.isHandRaised ?? false)")
    }
```

## <a name="receive-information-about-user-actions-in-the-ui-and-add-your-own-custom-functionalities"></a>UI의 사용자 동작에 대한 정보를 수신하고 사용자 지정 기능을 추가합니다.

`MeetingUIClientCallUserEventDelegate` 대리자 메서드는 원격 참가자 프로필의 사용자 작업에 대해 호출됩니다.
통화 또는 모임에 참여하는 동안 참여 옵션 속성 `enableNamePlateOptionsClickDelegate`을 `true`로 설정합니다.
이 속성을 설정하면 원격 참가자의 프로필에서 네임 플레이트 옵션을 사용하고 `MeetingUIClientCallUserEventDelegate`를 사용합니다.

클래스에 `MeetingUIClientCallUserEventDelegate`를 추가합니다.

```swift
class ViewController: UIViewController, MeetingUIClientCallUserEventDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

통화 또는 모임에 성공적으로 참여하기 시작한 후 `self.meetingUIClientCall?.meetingUIClientCallUserEventDelegate`를 `self`로 설정합니다.

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallUserEventDelegate = self
            }
        }
    })
}
```

`onNamePlateOptionsClicked` 프로토콜 메서드를 추가 및 구현하고 각 `identifier`를 해당 통화 참가 사용자에게 매핑합니다.
이 메서드는 통화 기본 화면에서 사용자 타일 또는 사용자 제목 텍스트를 한 번 탭하면 호출됩니다.

```swift
func onNamePlateOptionsClicked(identifier: CommunicationIdentifier) {
    if let userIdentifier = identifier as? CommunicationUserIdentifier
        {
            if (userIdentifier.identifier.starts(with: "8:acs:")) {
                // Custom behavior based on the user here.
                print("Acs user tile clicked")
            }
        }
}
```

`onParticipantViewLongPressed` 프로토콜 메서드를 추가 및 구현하고 각 `identifier`를 해당 통화 참가 사용자에게 매핑합니다.
이 메서드는 통화 기본 화면에서 사용자 타일을 길게 누를 때 호출됩니다.

```swift
func onParticipantViewLongPressed(identifier: CommunicationIdentifier) {
    if let userIdentifier = identifier as? CommunicationUserIdentifier
        {
            if (userIdentifier.identifier.starts(with: "8:acs:")) {
                // Custom behavior based on the user here.
                print("Acs user tile clicked")
            }
        }
}
```
## <a name="user-experience-customization"></a>사용자 환경 사용자 지정

앱 관련 아이콘을 제공하거나 통화 컨트롤 막대를 바꾸어 SDK의 사용자 환경을 사용자 지정할 수 있습니다. 

### <a name="customize-ui-icons-in-a-call-or-meeting"></a>통화 또는 모임의 UI 아이콘 사용자 지정

통화 또는 모임에 표시되는 아이콘은 `MeetingUIClient`에서 노출된 `public func set(iconConfig: Dictionary<MeetingUIClientIconType, String>)` 메서드를 통해 사용자 지정할 수 있습니다.
사용자 지정 가능한 아이콘 목록은 `MeetingUIClientIconType`에서 확인할 수 있습니다.

```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

meetingUIClient를 초기화한 후 `MeetingUIClientIconType`에서 지원되는 통화 아이콘의 `meetingUIClient?.set(iconConfig: self.getIconConfig())` 아이콘 구성을 설정합니다.

```swift
private func initMeetingUIClient() {
    meetingUIClient = MeetingUIClient(with: credential)
    meetingUIClient?.set(iconConfig: self.getIconConfig())
}

func getIconConfig() -> Dictionary<MeetingUIClientIconType, String> {
    var iconConfig = Dictionary<MeetingUIClientIconType, String>()
    iconConfig.updateValue("camera_fill", forKey: MeetingUIClientIconType.VideoOn)
    iconConfig.updateValue("camera_off", forKey: MeetingUIClientIconType.VideoOff)
    iconConfig.updateValue("microphone_fill", forKey: MeetingUIClientIconType.MicOn)
    iconConfig.updateValue("microphone_off", forKey: MeetingUIClientIconType.MicOff)
    iconConfig.updateValue("speaker_fill", forKey: MeetingUIClientIconType.Speaker)
    return iconConfig
}
```

### <a name="customize-main-call-screen"></a>기본 통화 화면 사용자 지정

`MeetingUIClient`는 기본 통화 화면 UI를 사용자 지정할 수 있도록 지원합니다. 현재는 `MeetingUIClientInCallScreenDelegate` 프로토콜 메서드를 사용한 UI 사용자 지정을 지원합니다.
통화 화면 제어 작업은 `MeetingUIClientCall`에 있는 메서드를 통해 노출됩니다.

클래스에 `MeetingUIClientInCallScreenDelegate`를 추가합니다.

```swift
class ViewController: UIViewController, MeetingUIClientInCallScreenDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

통화 또는 모임에 참여하기 전에 `meetingUIClient?.meetingUIClientInCallScreenDelegate`를 `self`로 설정합니다.

```swift
private func joinGroupCall() {
    meetingUIClient?.meetingUIClientInCallScreenDelegate = self
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true)
    let groupLocator = MeetingUIClientGroupCallLocator(groupId: "<GROUP_ID>")
    meetingUIClient?.join(meetingLocator: groupLocator, joinCallOptions: groupJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join call failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```

`provideControlTopBar` 프로토콜 메서드를 추가하고 구현하여 기본 통화 화면 위쪽에 알림 표시줄을 제공합니다.

```swift
func provideControlTopBar() -> UIView? {
    let topView = UIStackView.init()
    // add your customization here
    return topView
}
```

다른 필수 `MeetingUIClientInCallScreenDelegate` 프로토콜 메서드를 클래스에 추가하면 nil을 반환하는 빈 구현으로 남아 있을 수 있습니다.
```swift
func provideControlBottomBar() -> UIView? {
    return nil
}

func provideScreenBackgroudColor() -> UIColor? {
    return nil
}
```

## <a name="customize-on-staging-call-screen"></a>통화 준비 화면 사용자 지정

`MeetingUIClient`는 통화 준비 화면 UI를 사용자 지정할 수 있도록 지원합니다. 현재는 `MeetingUIClientStagingScreenDelegate` 프로토콜 메서드를 사용한 UI 사용자 지정을 지원합니다.
통화 또는 모임에 참여하는 동안 `enableCallStagingScreen` 참여 옵션 속성을 `true`로 설정하여 준비 화면을 표시합니다.

클래스에 `MeetingUIClientStagingScreenDelegate`를 추가합니다.

```swift
class ViewController: UIViewController, MeetingUIClientStagingScreenDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

통화 또는 모임에 참여하기 전에 `meetingUIClient?.meetingUIClientStagingScreenDelegate`를 `self`로 설정합니다.

```swift
private func joinGroupCall() {
    meetingUIClient?.meetingUIClientStagingScreenDelegate = self
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true)
    let groupLocator = MeetingUIClientGroupCallLocator(groupId: "<GROUP_ID>")
    meetingUIClient?.join(meetingLocator: groupLocator, joinCallOptions: groupJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join call failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```

`provideJoinButtonCornerRadius` 프로토콜 메서드를 추가하여 연결 단추 모퉁이를 둥근 모양으로 수정합니다.

```swift
func provideJoinButtonCornerRadius() -> CGFloat {
    return 24
}
```

다른 필수 `MeetingUIClientStagingScreenDelegate` 프로토콜 메서드를 클래스에 추가하면 nil을 반환하는 빈 구현으로 남아 있을 수 있습니다.
```swift
func provideJoinButtonBackgroundColor() -> UIColor? {
    return nil
}

func provideStagingScreenBackgroundColor() -> UIColor? {
    return nil
}
```

## <a name="customize-on-connecting-call-screen"></a>통화 연결 화면 사용자 지정

`MeetingUIClient`는 통화 연결 화면 UI를 사용자 지정할 수 있도록 지원합니다. 현재는 `MeetingUIClientConnectingScreenDelegate` 프로토콜 메서드를 사용한 UI 사용자 지정을 지원합니다.
`MeetingUIClient`에 노출된 `set(iconConfig: Dictionary<MeetingUIClientIconType, String>)` 구성 메서드를 사용하여 표시되는 아이콘만 변경하고 `MeetingUIClient`에서 제공하는 기능을 사용합니다.


클래스에 `MeetingUIClientConnectingScreenDelegate`를 추가합니다.

```swift
class ViewController: UIViewController, MeetingUIClientConnectingScreenDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

통화 또는 모임에 참여하기 전에 `meetingUIClient?.meetingUIClientConnectingScreenDelegate`를 `self`로 설정합니다.

```swift
private func joinGroupCall() {
    meetingUIClient?.meetingUIClientConnectingScreenDelegate = self
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true)
    let groupLocator = MeetingUIClientGroupCallLocator(groupId: "<GROUP_ID>")
    meetingUIClient?.join(meetingLocator: groupLocator, joinCallOptions: groupJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join call failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```

`provideConnectingScreenBackgroundColor` 프로토콜 메서드를 추가하고 구현하여 연결 화면의 배경색을 수정합니다.

```swift
func provideConnectingScreenBackgroundColor() -> UIColor?
    return 24
}
```

## <a name="perform-operations-with-the-call"></a>통화 관련 작업 수행

통화 제어 작업은 `MeetingUIClientCall`에 있는 메서드를 통해 노출됩니다.
이러한 메서드는 `MeetingUIClient` 사용자 지정 대리자를 사용하여 UI를 사용자 지정한 경우 통화 작업을 제어하는 데 유용합니다.

통화에 필요한 변수를 추가했습니다.
```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

`join` 메서드 `completionHandler`에서 `self.meetingUIClientCall` 변수 `meetingUIClientCall` 값을 할당합니다.
```swift
private func joinGroupCall() {
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true)
    let groupLocator = MeetingUIClientGroupCallLocator(groupId: "<GROUP_ID>")
    meetingUIClient?.join(meetingLocator: groupLocator, joinCallOptions: groupJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join call failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```
### <a name="mute-and-unmute"></a>음소거 및 음소거 해제

`mute` 메서드를 호출하여 진행 중인 통화(있는 경우)에서 마이크를 음소거합니다.
마이크 상태는 `MeetingUIClientCallDelegate`의 `onIsMutedChanged` 메서드 내용에 따라 변경됩니다.

```swift
// Mute the microphone for an active call.
public func mute(completionHandler: @escaping (Error?) -> Void)

    meetingUIClientCall?.mute { [weak self] (error) in
        if error != nil {
            print("Mute call failed: \(error!)")
        }
}
```

`unmute` 메서드를 호출하여 진행 중인 통화(있는 경우)에서 마이크의 음소거를 해제합니다.

```swift
// Unmute the microphone for an active call.
public func unmute(completionHandler: @escaping (Error?) -> Void)

meetingUIClientCall?.unmute { [weak self] (error) in
    if error != nil {
        print("Mute call failed: \(error!)")
    }
}
```

### <a name="other-operations-available-in-from-the--meetinguiclientcall-class"></a>다른 작업은 `MeetingUIClientCall` 클래스에서 확인할 수 있습니다.

```swift
// Start the video for an active call.
public func startVideo(completionHandler: @escaping (Error?) -> Void)

// Stop the video for an active call.
public func startVideo(completionHandler: @escaping (Error?) -> Void)

// Set the preferred audio route in the call for self user.
public func setAudio(route: MeetingUIClientAudioRoute, completionHandler: @escaping (Error?) -> Void)

// Raise the hand of current user for an active call.
public func raiseHand(completionHandler: @escaping (Error?) -> Void)

// Lower the hand of user provided in the identifier for an active call.
// public func lowerHand(identifier: CommunicationIdentifier, completionHandler: @escaping (Error?) -> Void)

// Show the call roster for an active call.
public func showCallRoster(completionHandler: @escaping (Error?) -> Void)

// Change the layout in the call for self user.
public func getSupportedLayoutModes() -> [MeetingUIClientLayoutMode]
public func changeLayout(mode: MeetingUIClientLayoutMode, completionHandler: @escaping (Error?) -> Void)

// Hangs up the call or leaves the meeting.
public func hangUp(completionHandler: @escaping (Error?) -> Void)

// Set the user role for an active call.
public func setRoleFor(identifier: CommunicationIdentifier, userRole: MeetingUIClientUserRole, completionHandler: @escaping (Error?) -> Void)
```
