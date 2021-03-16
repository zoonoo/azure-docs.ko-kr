---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: e9c889dcffe42fde244f8a35ce42032e84d78fff
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488119"
---
## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../create-communication-resource.md).
- 호출 클라이언트를 사용하도록 설정하는 `User Access Token`입니다. [`User Access Token`를 가져오는 방법](../../access-tokens.md)에 대한 자세한 정보
- 선택 사항: [애플리케이션에 통화 추가 시작](../getting-started-with-calling.md)에 대한 빠른 시작을 완료합니다.

## <a name="setting-up"></a>설치

### <a name="creating-the-xcode-project"></a>Xcode 프로젝트 만들기

> [!NOTE]
> 이 문서에서는 통화 클라이언트 라이브러리의 버전 1.0.0-beta.8을 사용합니다.

Xcode에서 새 iOS 프로젝트를 만들고 **단일 보기 앱** 템플릿을 선택합니다. 이 빠른 시작에서는 [SwiftUI 프레임워크](https://developer.apple.com/xcode/swiftui/)를 사용하므로 **언어** 를 **Swift** 로 설정하고 **사용자 인터페이스** 를 **SwiftUI** 로 설정해야 합니다. 이 빠른 시작 중에는 단위 테스트 또는 UI 테스트를 만들지 않습니다. **단위 테스트 포함** 을 선택 취소하고 **UI 테스트 포함** 도 선택 취소하십시오.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Xcode 내에서 새 프로젝트 만들기 창을 보여주는 스크린샷":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>CocoaPods를 사용하여 패키지 및 종속성 설치

1. 다음과 같이 애플리케이션에 대한 Podfile을 만듭니다.

   ```
   platform :ios, '13.0'
   use_frameworks!
   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.8'
     pod 'AzureCommunication', '~> 1.0.0-beta.8'
     pod 'AzureCore', '~> 1.0.0-beta.8'
   end
   ```

2. `pod install`을 실행합니다.
3. XCode로 `.xcworkspace`를 엽니다.

### <a name="request-access-to-the-microphone"></a>마이크에 대한 액세스 요청

디바이스의 마이크에 액세스하려면 앱의 정보 속성 목록을 `NSMicrophoneUsageDescription`으로 업데이트해야 합니다. 연결된 값을 시스템이 사용자의 액세스 요청을 요청하는 데 사용하는 대화 상자에 포함될 `string`으로 설정합니다.

프로젝트 트리의 `Info.plist` 항목을 마우스 오른쪽 단추로 클릭하고 **다음 형식으로 열기** > **소스 코드** 를 선택합니다. 최상위 `<dict>` 섹션에 다음 줄을 추가한 다음, 파일을 저장합니다.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

프로젝트의 **ContentView.swift** 파일을 열고 파일 맨 위에 `import` 선언을 추가하여 `AzureCommunicationCalling library`를 가져옵니다. 또한 `AVFoundation`을 가져옵니다. 코드에서 오디오 권한 요청에 필요합니다.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="object-model"></a>개체 모델

iOS용 Azure Communication Services 통화 클라이언트 라이브러리의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.


| 속성                                  | 설명                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | CallClient는 통화 클라이언트 라이브러리의 주 진입점입니다.|
| CallAgent | CallAgent는 통화를 시작하고 관리하는 데 사용됩니다. |
| CommunicationTokenCredential | CommunicationTokenCredential은 CallAgent를 인스턴스화하기 위한 토큰 자격 증명으로 사용됩니다.| 
| CommunicationIdentifier | CommunicationIdentifier는 사용자의 ID를 나타내는 데 사용되며 CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication 중 하나일 수 있습니다. |

> [!NOTE]
> 이벤트 대리자를 구현할 때 애플리케이션은 이벤트 구독이 필요한 개체에 대한 강력한 참조를 보유해야 합니다. 예를 들어 `call.addParticipant` 메서드를 호출할 때 `RemoteParticipant` 개체가 반환되고 애플리케이션에서 `RemoteParticipantDelegate`를 수신 대기하도록 대리자를 설정하는 경우 애플리케이션은 `RemoteParticipant` 개체에 대한 강력한 참조를 보유해야 합니다. 그렇지 않으면 이 개체가 수집될 경우 호출 SDK가 개체를 호출하려고 할 때 대리자가 치명적인 예외를 throw합니다.

## <a name="initialize-the-callagent"></a>CallAgent 초기화

`CallAgent`에서 `CallClient` 인스턴스를 만들려면 초기화된 후 비동기적으로 `callClient.createCallAgent` 개체를 반환하는 `CallAgent` 메서드를 사용해야 합니다.

통화 클라이언트를 만들려면 `CommunicationTokenCredential` 개체를 전달해야 합니다.

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationTokenCredential?
var userCredential: CommunicationTokenCredential?
   do {
       userCredential = try CommunicationTokenCredential(with: CommunicationTokenRefreshOptions(initialToken: token, 
                                                                     refreshProactively: true,
                                                                     tokenRefresher: self.fetchTokenSync))
   } catch {
       return
}

// tokenProvider needs to be implemented by contoso which fetches new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

위에서 만든 `CommunicationTokenCredential` 개체를 `CallClient`에 전달하고 표시 이름을 설정합니다.

```swift

callClient = CallClient()
let callAgentOptions:CallAgentOptions = CallAgentOptions()!
options.displayName = " iOS User"

callClient?.createCallAgent(userCredential: userCredential!,
    options: callAgentOptions) { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>발신 전화 걸기

통화를 만들고 시작하려면 `CallAgent`에서 API 중 하나를 호출하고 Communication Services 관리 클라이언트 라이브러리를 사용하여 프로비저닝한 사용자의 Communication Services ID를 제공해야 합니다.

통화 만들기와 시작은 동기적입니다. 통화의 모든 이벤트를 구독할 수 있는 호출 인스턴스를 받게 됩니다.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>사용자와 1:1 통화 또는 사용자 및 PSTN과 1:n 통화

```swift

let callees = [CommunicationUser(identifier: 'UserId')]
let oneToOneCall = self.callAgent.call(participants: callees, options: StartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>사용자 및 PSTN과 1:n 통화
PSTN과 통화하려면 Communication Services로 얻은 전화번호를 지정해야 합니다.
```swift

let pstnCallee = PhoneNumberIdentifier(phoneNumber: '+1999999999')
let callee = CommunicationUserIdentifier(identifier: 'UserId')
let groupCall = self.callAgent.call(participants: [pstnCallee, callee], options: StartCallOptions())

```

### <a name="place-a-11-call-with-with-video"></a>비디오로 1:1 통화
디바이스 관리자 인스턴스를 가져오려면 [여기](#device-management)를 참조하십시오.

```swift

let camera = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: camera)
let videoOptions = VideoOptions(localVideoStream: localVideoStream)

let startCallOptions = StartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUserIdentifier(identifier: 'UserId')
let call = self.callAgent?.call(participants: [callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>그룹 통화 참가
통화에 참가하려면 *CallAgent* 에서 API 중 하나를 호출해야 합니다.

```swift

let groupCallLocator = GroupCallLocator(groupId: UUID(uuidString: "uuid_string"))!
let call = self.callAgent?.join(with: groupCallLocator, joinCallOptions: JoinCallOptions())

```

### <a name="subscribe-for-incoming-call"></a>수신 전화 구독
수신 전화 이벤트 구독

```
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate
{
    // Event raised when there is an incoming call
    public func onIncomingCall(_ callAgent: CallAgent!, incomingcall: IncomingCall!) {
        self.incomingCall = incomingcall
        // Subscribe to get OnCallEnded event
        self.incomingCall?.delegate = self
    }

    // Event raised when incoming call was not answered
    public func onCallEnded(_ incomingCall: IncomingCall!, args: PropertyChangedEventArgs!) {
        self.incomingCall = nil
    }
}
```

### <a name="accept-an-incoming-call"></a>수신 전화 받기
전화를 받으려면 호출 개체에서 'accept' 메서드를 호출합니다.
CallAgent로 대리자 설정 
```swift
final class CallHandler: NSObject, CallAgentDelegate
{
    public var incomingCall: Call?
 
    public func onCallsUpdated(_ callAgent: CallAgent!, args: CallsUpdatedEventArgs!) {
        if let incomingCall = args.addedCalls?.first(where: { $0.isIncoming }) {
            self.incomingCall = incomingCall
        }
    }
}

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: firstCamera)
let acceptCallOptions = AcceptCallOptions()
acceptCallOptions!.videoOptions = VideoOptions(localVideoStream:localVideoStream!)
if let incomingCall = CallHandler().incomingCall {
   incomingCall.accept(options: acceptCallOptions) { (call, error) in
               if error == nil {
                   print("Incoming call accepted")
               } else {
                   print("Failed to accept incoming call")
               }
           }
} else {
   print("No incoming call found to accept")
}
```

## <a name="push-notification"></a>푸시 알림

모바일 푸시 알림은 모바일 디바이스에 표시되는 팝업 알림입니다. 통화를 위해 VoIP(Voice over Internet Protocol) 푸시 알림을 집중적으로 다루겠습니다. 푸시 알림 등록, 푸시 알림 처리 및 푸시 알림 등록 취소 기능을 제공할 예정입니다.

### <a name="prerequisite"></a>필수 요건

- 1단계: Xcode -> 서명 및 기능 -> 기능 추가 -> "푸시 알림"
- 2단계: Xcode -> 서명 및 기능 -> 기능 추가 -> "백그라운드 모드"
- 3단계: "백그라운드 모드"-> "VoIP" 및 "원격 알림" 선택

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="Xcode의 기능을 추가하는 방법을 보여주는 스크린샷입니다." lightbox="../media/ios/xcode-push-notification.png":::

#### <a name="register-for-push-notifications"></a>푸시 알림 등록

푸시 알림을 등록하려면 디바이스 등록 토큰을 사용하여 *CallAgent* 인스턴스에서 registerPushNotification()을 호출합니다.

초기화 성공 후 푸시 알림 등록을 호출해야 합니다. `callAgent` 개체가 destroy되면 푸시 알림을 자동으로 등록 취소하는 `logout`이 호출됩니다.


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken) { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
}

```

#### <a name="push-notification-handling"></a>푸시 알림 처리
수신 전화 푸시 알림을 받으려면 사전 페이로드를 사용하여 *CallAgent* 인스턴스에서 *handlePushNotification()* 을 호출합니다.

```swift

let callNotification = IncomingCallInformation.from(payload: pushPayload?.dictionaryPayload)

callAgent.handlePush(notification: callNotification) { (error) in
    if (error != nil) {
        print("Handling of push notification failed")
    } else {
        print("Handling of push notification was successful")
    }
}

```
#### <a name="unregister-push-notification"></a>푸시 알림 등록 취소

애플리케이션에서 언제든지 푸시 알림을 등록 취소할 수 있습니다. *CallAgent* 에서 `unregisterPushNotification` 메서드를 호출하기만 하면 됩니다.
> [!NOTE]
> 로그아웃 시 애플리케이션이 푸시 알림에서 자동으로 등록 취소되지 않습니다.

```swift

callAgent.unregisterPushNotifications { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
}

```

## <a name="mid-call-operations"></a>통화 중 작업

통화 중 비디오 및 오디오와 관련된 설정을 관리하는 다양한 작업을 수행할 수 있습니다.

### <a name="mute-and-unmute"></a>음소거 및 음소거 해제

로컬 엔드포인트를 음소거하거나 음소거를 해제하려면 `mute` 및 `unmute` 비동기 API를 사용합니다.

```swift
call!.mute { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
}

```

[비동기] 로컬 음소거 해제

```swift
call!.unmute { (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
}
```

### <a name="start-and-stop-sending-local-video"></a>로컬 비디오 보내기 시작 및 중지

통화 중 다른 참가자에게 로컬 비디오 보내기를 시작하려면 `startVideo` API를 사용하고 `camera`로 `localVideoStream`을 전달합니다.

```swift

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: firstCamera)

call!.startVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    } else {
        print("Local video failed to start")
    }
}

```

비디오 보내기를 시작하면 `LocalVideoStream` 인스턴스가 호출 인스턴스의 `localVideoStreams` 컬렉션에 추가됩니다.

```swift

call.localVideoStreams[0]

```

[비동기] 로컬 비디오를 중지하려면 `call.startVideo` 호출에서 반환된 `localVideoStream`을 전달합니다.

```swift

call!.stopVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    } else {
        print("Local video failed to stop")
    }
}

```

## <a name="remote-participants-management"></a>원격 참가자 관리

모든 원격 참가자는 `RemoteParticipant` 형식으로 표시되며 호출 인스턴스에서 `remoteParticipants` 컬렉션을 통해 사용할 수 있습니다.

### <a name="list-participants-in-a-call"></a>통화 참가자 나열

```swift

call.remoteParticipants

```

### <a name="remote-participant-properties"></a>원격 참가자 속성

```swift

// [RemoteParticipantDelegate] delegate - an object you provide to receive events from this RemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision token for another user
var identity = remoteParticipant.identity

// ParticipantStateIdle = 0, ParticipantStateEarlyMedia = 1, ParticipantStateConnecting = 2, ParticipantStateConnected = 3, ParticipantStateOnHold = 4, ParticipantStateInLobby = 5, ParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [Error] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// RemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [RemoteVideoStream, RemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>통화에 참가자 추가

통화에 참가자(사용자 또는 전화번호)를 추가하려면 `addParticipant`를 호출합니다. 그러면 원격 참가자 인스턴스가 동기적으로 반환됩니다.

```swift

let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUserIdentifier(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>통화에서 참가자 제거
통화에서 참가자(사용자 또는 전화번호)를 제거하려면 `removeParticipant` API를 호출합니다. 이는 비동기적으로 확인됩니다.

```swift

call!.remove(participant: remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}

```

## <a name="render-remote-participant-video-streams"></a>원격 참가자 비디오 스트림 렌더링

원격 참가자는 전화 통화 중에 비디오 또는 화면 공유를 시작할 수 있습니다.

### <a name="handle-remote-participant-videoscreen-sharing-streams"></a>원격 참가자 비디오/화면 공유 스트림 처리

원격 참가자의 스트림을 나열하려면 `videoStreams` 컬렉션을 검사합니다.

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="remote-video-stream-properties"></a>원격 비디오 스트림 속성

```swift

var type: MediaStreamType = remoteParticipantVideoStream.type // 'MediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-stream"></a>원격 참가자 스트림 렌더링

원격 참가자 스트림 렌더링을 시작하는 방법은 다음과 같습니다.

```swift

let renderer: Renderer? = Renderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: RendererView? = renderer?.createView(with: RenderingOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)

```

### <a name="remote-video-renderer-methods-and-properties"></a>원격 비디오 렌더러 메서드 및 속성

```swift
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```

## <a name="device-management"></a>디바이스 관리

`DeviceManager`를 사용하여 오디오/비디오 스트림을 전송하는 호출에 사용 가능한 로컬 디바이스를 열거할 수 있습니다. 또한 사용자에게 마이크/카메라 액세스 권한을 요청할 수 있습니다. `callClient` 개체에서 `deviceManager`에 액세스할 수 있습니다.

```swift

self.callClient!.getDeviceManager { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    }
```

### <a name="enumerate-local-devices"></a>로컬 디바이스 열거

로컬 디바이스에 액세스하려면 디바이스 관리자에서 열거 메서드를 사용합니다. 열거는 비동기 작업입니다.

```swift
// enumerate local cameras
var localCameras = deviceManager.cameras! // [VideoDeviceInfo, VideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.microphones! // [AudioDeviceInfo, AudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.speakers! // [AudioDeviceInfo, AudioDeviceInfo...]
``` 

### <a name="set-default-microphonespeaker"></a>기본 마이크/스피커 설정

디바이스 관리자를 사용하여 호출을 시작할 때 사용할 기본 디바이스를 설정할 수 있습니다. 스택 기본값을 설정하지 않으면 Communication Services는 OS 기본값으로 대체됩니다.

```swift
// get first microphone
var firstMicrophone = self.deviceManager!.cameras!.first
// [Synchronous] set microphone
deviceManager.setMicrophone(microphoneDevice: firstMicrophone)
// get first speaker
var firstSpeaker = self.deviceManager!.speakers!
// [Synchronous] set speaker
deviceManager.setSpeaker(speakerDevice: firstSpeaker)
```

### <a name="local-camera-preview"></a>로컬 카메라 미리 보기

`Renderer`를 사용하여 로컬 카메라에서 스트림 렌더링을 시작할 수 있습니다. 이 스트림은 다른 참가자에게 전송되지 않는 로컬 미리 보기 피드입니다. 비동기 작업입니다.

```swift

let camera: VideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: LocalVideoStream = LocalVideoStream(camera: camera)
let renderer: Renderer = Renderer(localVideoStream: localVideoStream)
self.view = try renderer!.createView()

```

### <a name="local-camera-preview-properties"></a>로컬 카메라 미리 보기 속성

렌더러에는 렌더링을 제어할 수 있는 속성 및 메서드 집합이 있습니다.

```swift

// Constructor can take in LocalVideoStream or RemoteVideoStream
let localRenderer = Renderer(localVideoStream:localVideoStream)
let remoteRenderer = Renderer(remoteVideoStream:remoteVideoStream)

// [StreamSize] size of the rendering view
localRenderer.size

// [RendererDelegate] an object you provide to receive events from this Renderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer.createView(with: RenderingOptions(scalingMode: ScalingMode.fit))

// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="eventing-model"></a>이벤트 모델

값이 변경될 때 알림을 받도록 대부분의 속성 및 컬렉션을 구독할 수 있습니다.

### <a name="properties"></a>속성
`property changed` 이벤트를 구독하는 방법은 다음과 같습니다.

```swift
call.delegate = self
// Get the property of the call state by doing get on the call's state member
public func onCallStateChanged(_ call: Call!,
                               args: PropertyChangedEventArgs!)
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>컬렉션
`collection updated` 이벤트를 구독하는 방법은 다음과 같습니다.

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func onLocalVideoStreamsChanged(_ call: Call!,
                                       args: LocalVideoStreamsUpdatedEventArgs!)
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
