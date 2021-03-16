---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 41a5f05f016a876894949c35a6610b2b49f9eb9d
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488121"
---
## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../create-communication-resource.md).
- 호출 클라이언트를 사용하도록 설정하는 `User Access Token`입니다. [`User Access Token`를 가져오는 방법](../../access-tokens.md)에 대한 자세한 정보
- 선택 사항: [애플리케이션에 통화 추가 시작](../getting-started-with-calling.md)에 대한 빠른 시작을 완료합니다.

## <a name="setting-up"></a>설치

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

> [!NOTE]
> 이 문서에서는 통화 클라이언트 라이브러리 1.0.0-beta.6 버전을 사용합니다.

`npm install` 명령을 사용하여 JavaScript용 Azure Communication Services Calling and Common 클라이언트 라이브러리를 설치합니다.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>개체 모델

Azure Communication Services 통화 클라이언트 라이브러리의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

| 이름                             | 설명                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient는 통화 클라이언트 라이브러리의 주 진입점입니다.                                                                       |
| CallAgent                        | CallAgent는 통화를 시작하고 관리하는 데 사용됩니다.                                                                                            |
| DeviceManager                    | DeviceManager는 미디어 디바이스를 관리하는 데 사용됩니다.                                                                                           |
| AzureCommunicationTokenCredential | AzureCommunicationTokenCredential 클래스는 CallAgent를 인스턴스화하는 데 사용되는 CommunicationTokenCredential 인터페이스를 구현합니다. |


## <a name="initialize-the-callclient-create-callagent-and-access-devicemanager"></a>CallClient 초기화, CallAgent 만들기 및 DeviceManager 액세스

새 `CallClient` 인스턴스를 인스턴스화합니다. 로거 인스턴스와 같은 사용자 지정 옵션을 사용하여 구성할 수 있습니다.
`CallClient`를 인스턴스화한 후에는 `CallClient` 인스턴스에서 `createCallAgent` 메서드를 호출하여 `CallAgent` 인스턴스를 만들 수 있습니다. 이렇게 하면 `CallAgent` 인스턴스 개체가 비동기적으로 반환됩니다.
`createCallAgent` 메서드는 [사용자 액세스 토큰](../../access-tokens.md)을 허용하는 `CommunicationTokenCredential`을 인수로 사용합니다.
`DeviceManager`에 액세스하려면 먼저 callAgent 인스턴스를 만들어야 합니다. 그런 다음, `getDeviceManager` 인스턴스에서 메서드를 사용하여 `CallClient` DeviceManager를 가져올 수 있습니다.

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-an-outgoing-call"></a>발신 전화 걸기

호출을 만들고 시작 하려면 CallAgent에서 Api 중 하나를 사용 하 고 통신 서비스 id 클라이언트 라이브러리를 통해 만든 사용자를 제공 해야 합니다.

통화 만들기와 시작은 동기적입니다. 통화 인스턴스를 사용하여 통화 이벤트를 구독할 수 있습니다.

## <a name="place-a-call"></a>전화 걸기

### <a name="place-a-11-call-to-a-user-or-pstn"></a>사용자 또는 PSTN에게 1:1 전화 걸기
다른 통신 서비스 사용자에 대 한 호출을 수행 하려면에서 메서드를 호출 하 `startCall` `callAgent` 고 [통신 서비스 id 라이브러리를 사용 하 여 만든](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens)호출 수신자의 CommunicationUserIdentifier를 전달 합니다.

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

PSTN에게 전화를 걸려면 `callAgent`에서 `startCall` 메서드를 호출하고 호출 수신자의 PhoneNumberIdentifier를 전달합니다.
PSTN 호출을 허용하도록 Communication Services 리소스를 구성해야 합니다.
PSTN 번호를 호출할 때 대체 호출자 ID를 지정해야 합니다. 대체 호출자 ID는 PSTN 통화의 호출자를 식별하는 전화 번호(E.164 표준 기반)를 참조합니다. 예를 들어 PSTN 통화의 대체 호출자 ID를 제공하면 전화가 수신될 때 해당 전화 번호가 호출 수신자에게 표시됩니다.

> [!WARNING]
> PSTN 통화는 현재 프라이빗 미리 보기로 제공됩니다. 사용하려면 [얼리어답터 프로그램을 신청](https://aka.ms/ACS-EarlyAdopter)하세요.
```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>사용자 및 PSTN과 1:n 전화 걸기
```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>비디오 카메라로 1:1 전화 걸기
> [!WARNING]
> 현재 발신 로컬 비디오 스트림은 하나만 가능합니다.
영상 전화를 걸려면 deviceManager `getCameras()` API를 사용하여 로컬 카메라를 열거해야 합니다.
원하는 카메라를 선택한 후에는 해당 카메라를 사용하여 `LocalVideoStream` 인스턴스를 생성하고 `localVideoStream` 배열 내에 있는 `videoOptions` 항목으로 `startCall` 메서드에 전달합니다.
전화가 연결되면 선택한 카메라에서 다른 참가자로 비디오 스트림을 자동으로 보내기 시작합니다. 이는 Call.Accept() 비디오 옵션과 CallAgent.join() 비디오 옵션에도 적용됩니다.
```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
videoDeviceInfo = cameras[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

### <a name="join-a-group-call"></a>그룹 통화 참가
새 그룹 통화를 시작하거나 진행 중인 그룹 통화에 참가하려면 'join' 메서드를 사용하고 `groupId` 속성을 통해 개체를 전달합니다. 값은 GUID여야 합니다.
```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```
### <a name="join-a-teams-meeting"></a>Teams 회의 참가
Teams 회의에 참가하려면 'join' 메서드를 사용하고 회의 링크 또는 회의 좌표를 전달합니다.
```js
// Join using meeting link
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);

// Join using meeting coordinates
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receiving-an-incoming-call"></a>수신 전화 받기

`CallAgent` 인스턴스는 로그인된 ID가 수신 전화를 받을 때 `incomingCall` 이벤트를 내보냅니다. 이 이벤트를 수신 대기하려면 다음과 같은 방법으로 구독합니다.

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    //Get information about caller
    var callerInfo = incomingCall.callerInfo
    
    //accept the call
    var call = await incomingCall.accept();

    //reject the call
    incomingCall.reject();
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

`incomingCall` 이벤트는 통화를 수락하거나 거부할 수 있는 `IncomingCall` 인스턴스를 제공합니다.


## <a name="call-management"></a>통화 관리

통화 중에 통화 속성에 액세스하여 비디오 및 오디오와 관련된 설정을 관리하는 다양한 작업을 수행할 수 있습니다.

### <a name="call-properties"></a>통화 속성
* 이 통화의 고유 ID(문자열)를 가져옵니다.
```js

const callId: string = call.id;

```

* 통화의 다른 참가자에 대해 알아보려면 `call` 인스턴스의 `remoteParticipant` 컬렉션을 검사합니다. 배열에는 목록 `RemoteParticipant` 개체가 포함됩니다.
```js
const remoteParticipants = call.remoteParticipants;
```

* 수신 전화인 경우 호출자의 식별자입니다. 식별자는 `CommunicationIdentifier` 형식 중 하나입니다.
```js

const callerIdentity = call.callerInfo.identifier;

* Get the state of the Call.
```js

const callState = call.state;

```
다음과 같은 통화의 현재 상태를 나타내는 문자열을 반환합니다.
* 'None' - 초기 통화 상태입니다.
* 'Incoming' - 전화가 걸려 오는 중임을 나타내며, 수락하거나 거부해야 합니다.
* 'Connecting' - 전화를 걸었거나 수락되었을 때의 초기 전환 상태입니다.
* 'Ringing' - 발신 통화의 경우 원격 참가자의 전화벨이 울리고 있다는 뜻이며, 이들의 입장에서는 'Incoming' 전화입니다.
* 'EarlyMedia' - 전화가 연결되기 전에 알림이 재생되는 상태를 나타냅니다.
* 'Connected' - 전화가 연결되었습니다.
* 'LocalHold' - 로컬 참가자가 전화를 대기 상태로 설정했으며, 로컬 엔드포인트와 원격 참가자 간에 미디어가 전달되지 않습니다.
* 'RemoteHold' - 원격 참가자가 전화를 대기 상태로 설정했으며, 로컬 엔드포인트와 원격 참가자 간에 미디어가 전달되지 않습니다.
* 'Disconnecting' - 통화가 'Disconnected' 상태로 전환되기 전의 상태입니다.
* 'Disconnected' - 최종 통화 상태입니다.
  * 네트워크 연결이 끊어지면 약 2분 후 상태가 'Disconnected'로 전환됩니다.

* 특정 통화가 종료된 이유를 확인하려면 `callEndReason` 속성을 검사합니다.
```js

const callEndReason = call.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* 현재 통화가 수신 전화인지 아니면 발신 전화인지 알아보려면 `CallDirection`을 반환하는 `direction` 속성을 검사합니다.
```js
const isIncoming = call.direction == 'Incoming';
const isOutgoing = call.direction == 'Outgoing';
```

*  현재 마이크가 음소거 되었는지 확인하려면 `Boolean`을 반환하는 `muted` 속성을 검사합니다.
```js

const muted = call.isMicrophoneMuted;

```

* 지정된 엔드포인트에서 화면 공유 스트림을 보내고 있는지 확인하려면 `Boolean`을 반환하는 `isScreenSharingOn`을 검사합니다.
```js

const isScreenSharingOn = call.isScreenSharingOn;

```

* 활성 비디오 스트림을 검사하려면 `LocalVideoStream` 개체를 포함하고 있는 `localVideoStreams` 컬렉션을 검사합니다.
```js

const localVideoStreams = call.localVideoStreams;

```

### <a name="call-ended-event"></a>통화 종료 이벤트

통화가 종료되면 `Call` 인스턴스는 `callEnded` 이벤트를 내보냅니다. 이 이벤트를 수신 대기하려면 다음과 같은 방법으로 구독합니다.

```js
const callEndHander = async (args: { callEndReason: CallEndReason }) => {
    console.log(args.callEndReason)
};

call.on('callEnded', callEndHander);
```

### <a name="mute-and-unmute"></a>음소거 및 음소거 해제

로컬 엔드포인트를 음소거하거나 음소거를 해제하려면 다음과 같이 `mute` 및 `unmute` 비동기 API를 사용합니다.

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>로컬 비디오 보내기 시작 및 중지


비디오를 시작하려면 `deviceManager` 개체의 `getCameras` 메서드를 사용하여 카메라를 열거해야 합니다. 그런 다음, 새 인스턴스 `LocalVideoStream`을 만들어서 원하는 카메라를 `startVideo` 메서드에 인수로 전달합니다.


```js
const localVideoStream = new LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);

```

비디오 전송을 성공적으로 시작하면 호출 인스턴스의 `localVideoStreams` 컬렉션에 `LocalVideoStream` 인스턴스가 추가됩니다.

```js

call.localVideoStreams[0] === localVideoStream;

```

로컬 비디오를 중지하려면 `localVideoStream` 컬렉션에서 사용할 수 있는 `localVideoStreams` 인스턴스를 전달합니다.

```js

await call.stopVideo(localVideoStream);

```

`localVideoStream` 인스턴스에서 `switchSource`를 호출하여 비디오가 전송되는 동안 다른 카메라 디바이스로 전환할 수 있습니다.

```js
const cameras = await callClient.getDeviceManager().getCameras();
localVideoStream.switchSource(cameras[1]);

```

## <a name="remote-participants-management"></a>원격 참가자 관리

모든 원격 참가자는 `RemoteParticipant` 형식으로 표시되며 호출 인스턴스에서 `remoteParticipants` 컬렉션을 통해 사용할 수 있습니다.

### <a name="list-participants-in-a-call"></a>통화 참가자 나열
`remoteParticipants` 컬렉션은 특정 통화의 원격 참가자 목록을 반환합니다.

```js

call.remoteParticipants; // [remoteParticipant, remoteParticipant....]

```

### <a name="remote-participant-properties"></a>원격 참가자 속성
원격 참가자와 연결된 여러 가지 속성 및 컬렉션이 있습니다.
#### <a name="communicationidentifier"></a>CommunicationIdentifier
이 원격 참가자의 식별자를 가져옵니다.
ID는 'CommunicationIdentifier' 형식 중 하나입니다.
```js
const identifier = remoteParticipant.identifier;
```
'CommunicationIdentifier' 형식 중 하나입니다.
  * { communicationUserId: '<ACS_USER_ID'> } - ACS 사용자를 나타내는 개체
  * { phoneNumber: '<E.164>' } - 전화 번호를 E.164 형식으로 나타내는 개체
  * { microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" } - Teams 사용자를 나타내는 개체

#### <a name="state"></a>시스템 상태
이 원격 참가자의 상태를 가져옵니다.
```js

const state = remoteParticipant.state;
```
상태는 다음 중 하나입니다.
* 'Idle' - 초기 상태입니다.
* 'Connecting' - 참가자가 통화에 연결하는 동안의 전환 상태입니다.
* 'Ringing' - 참가자가 전화를 거는 중입니다.
* 'Connected' - 참가자가 전화에 연결되었습니다.
* 'Hold' - 참가자가 보류 중입니다.
* 'EarlyMedia' - 참가자가 통화에 연결되기 전에 알림이 재생됩니다.
* 'Disconnected' - 최종 상태입니다. 참가자의 통화 연결이 끊겼습니다.
  * 원격 참가자의 네트워크 연결이 끊어지면 원격 참가자의 상태는 약 2분 후에 'Disconnected'로 전환됩니다.

#### <a name="call-end-reason"></a>통화 종료 이유
참가자가 통화를 종료한 이유를 알아보려면 `callEndReason` 속성을 검사합니다.
```js
const callEndReason = remoteParticipant.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```
#### <a name="is-muted"></a>음소거됨
이 원격 참가자가 음소거되었는지 확인하려면 `Boolean`을 반환하는 `isMuted` 속성을 검사합니다.
```js
const isMuted = remoteParticipant.isMuted;
```
#### <a name="is-speaking"></a>말하는 중
이 원격 참가자가 말하는 중인지 여부를 확인하려면 `Boolean`을 반환하는 `isSpeaking` 속성을 검사합니다.
```js
const isSpeaking = remoteParticipant.isSpeaking;
```

#### <a name="video-streams"></a>비디오 스트림
특정 참가자가 이 통화에서 보내는 모든 비디오 스트림을 검사하려면 `RemoteVideoStream` 개체를 포함하고 있는 `videoStreams` 컬렉션을 확인합니다.
```js

const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]

```


### <a name="add-a-participant-to-a-call"></a>통화에 참가자 추가

통화에 참가자(사용자 또는 전화 번호)를 추가하려면 `addParticipant`를 호출합니다.
'Identifier' 형식 중 하나를 제공합니다.
그러면 원격 참가자 인스턴스가 동기적으로 반환됩니다.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-participant-from-a-call"></a>통화에서 참가자 제거

통화에서 참가자(사용자 또는 전화 번호)를 제거하려면 `removeParticipant`를 호출합니다.
'Identifier' 형식 중 하나를 전달해야 합니다. 통화에서 참가자가 제거되면 이 형식이 비동기적으로 확인됩니다.
참가자도 `remoteParticipants` 컬렉션에서 제거됩니다.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>원격 참가자 비디오 스트림 렌더링

원격 참가자의 비디오 스트림과 화면 공유 스트림을 나열하려면 `videoStreams` 컬렉션을 검사합니다.

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

`RemoteVideoStream`을 렌더링하려면 `isAvailableChanged` 이벤트를 구독해야 합니다.
`isAvailable` 속성이 `true`로 변경되면 원격 참가자가 스트림을 보내는 것입니다.
이 경우 `Renderer`의 새 인스턴스를 만든 다음, 비동기 `createView` 메서드를 사용하여 새 `RendererView` 인스턴스를 만듭니다.  그러면 모든 UI 요소에 `view.target`을 연결할 수 있습니다.
원격 스트림의 가용성이 변경될 때마다 전체 렌더러, 즉, 특정 `RendererView`를 제거하거나 유지하도록 선택할 수 있지만, 이 경우 빈 비디오 프레임이 표시됩니다.

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let renderer: Renderer = new Renderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await renderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('availabilityChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            renderer.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        displayVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>원격 비디오 스트림 속성
원격 비디오 스트림에는 다음과 같은 속성이 있습니다.

* `Id` - 원격 비디오 스트림의 ID
```js
const id: number = remoteVideoStream.id;
```

* `StreamSize` - 원격 비디오 스트림의 크기(너비/높이)
```js
const size: {width: number; height: number} = remoteVideoStream.size;
```

* `MediaStreamType` - 'Video' 또는 'ScreenSharing' 중 하나
```js
const type: MediaStreamType = remoteVideoStream.mediaStreamType;
```
* `isAvailable` - 원격 참가자 엔드포인트가 스트림을 능동적으로 전송하고 있는지 여부를 표시
```js
const type: boolean = remoteVideoStream.isAvailable;
```

### <a name="renderer-methods-and-properties"></a>렌더러 메서드 및 속성

* 나중에 애플리케이션 UI에서 연결하여 원격 비디오 스트림을 렌더링할 수 있는 `RendererView` 인스턴스를 만듭니다.
```js
renderer.createView()
```

* 렌더러 및 연결된 모든 `RendererView` 인스턴스를 삭제합니다.
```js
renderer.dispose()
```


### <a name="rendererview-methods-and-properties"></a>RendererView 메서드 및 속성
`RendererView`를 만들 때 `scalingMode` 및 `isMirrored` 속성을 지정할 수 있습니다.
크기 조정 모드는 '늘이기', '자르기' 또는 '맞춤'으로 설정할 수 있으며, `isMirrored`를 지정하면 렌더링된 스트림이 상하로 대칭 이동됩니다.

```js
const rendererView: RendererView = renderer.createView({ scalingMode, isMirrored });
```
모든 `RendererView` 인스턴스에는 `target` 렌더링 표면을 나타내는 속성이 있습니다. 이 속성을 애플리케이션 UI에서 연결해야 합니다.
```js
document.body.appendChild(rendererView.target);
```

나중에 `updateScalingMode` 메서드를 호출하여 크기 조정 모드를 업데이트할 수 있습니다.
```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>디바이스 관리

`DeviceManager`를 사용하여 오디오/비디오 스트림을 전송하는 호출에 사용 가능한 로컬 디바이스를 열거할 수 있습니다. 또한 네이티브 브라우저 API를 사용하여 사용자의 마이크 및 카메라에 액세스할 수 있는 권한을 요청할 수 있습니다.

`callClient.getDeviceManager()` 메서드를 호출하여 `deviceManager`에 액세스할 수 있습니다.
> [!WARNING]
> 현재는 DeviceManager에 대한 액세스 권한을 얻으려면 먼저 `callAgent` 개체를 인스턴스화해야 합니다.

```js

const deviceManager = await callClient.getDeviceManager();

```

### <a name="enumerate-local-devices"></a>로컬 디바이스 열거

로컬 디바이스에 액세스하려면 디바이스 관리자에서 열거 메서드를 사용하면 됩니다. 열거는 비동기 작업입니다.

```js

//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]

```

### <a name="set-default-microphonespeaker"></a>기본 마이크/스피커 설정

디바이스 관리자를 사용하여 호출을 시작할 때 사용할 기본 디바이스를 설정할 수 있습니다.
클라이언트 기본값을 설정하지 않으면 Communication Services는 OS 기본값으로 대체됩니다.

```js

// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(AudioDeviceInfo);

```

### <a name="local-camera-preview"></a>로컬 카메라 미리 보기

`DeviceManager` 및 `Renderer`를 사용하여 로컬 카메라에서 스트림 렌더링을 시작할 수 있습니다. 이 스트림은 다른 참가자에게 전송되지 않는 로컬 미리 보기 피드입니다. 비동기 작업입니다.

```js
const cameras = await deviceManager.getCameras();
const localVideoDevice = cameras[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-cameramicrophone"></a>카메라/마이크 사용 권한 요청

다음과 같이 카메라/마이크 사용 권한을 부여해 달라는 메시지를 사용자에게 표시합니다.

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```
이는 `audio` 및 `video` 사용 권한이 부여되었는지 여부를 나타내는 개체를 사용하여 비동기적으로 확인됩니다.
```js
console.log(result.audio);
console.log(result.video);
```


## <a name="call-recording-management"></a>통화 레코딩 관리

[!INCLUDE [Private Preview Notice](../../../includes/private-preview-include-section.md)]

통화 레코딩은 핵심 `Call` API의 확장 기능입니다. 먼저 레코딩 기능 API 개체를 가져와야 합니다.

```js
const callRecordingApi = call.api(Features.Recording);
```

그런 다음, 통화가 녹음되고 있는지 확인하려면 `Boolean`을 변환하는 `callRecordingApi`의 `isRecordingActive` 속성을 반환합니다.

```js
const isResordingActive = callRecordingApi.isRecordingActive;
```

레코딩 변화를 구독할 수도 있습니다.

```js
const isRecordingActiveChangedHandler = () => {
  console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);

```

## <a name="call-transfer-management"></a>통화 전송 관리

통화 전송은 핵심 `Call` API의 확장 기능입니다. 먼저 전송 기능 API 개체를 가져와야 합니다.

```js
const callTransferApi = call.api(Features.Transfer);
```

통화 전송에는 *전송자*, *수신자* 및 *전송 대상* 의 세 가지 당사자가 관련됩니다. 전송 흐름은 다음과 같이 작동합니다.

1. *전송자* 와 *수신자* 사이에 이미 연결된 통화가 있습니다.
2. *전송자* 가 통화 전송을 결정합니다(*수신자*  ->  *전송 대상*)
3. *전송자* 가 `transfer` API를 호출합니다.
4. *수신자* 는 `transferRequested` 이벤트를 통해 *전송 대상* 요청을 `accept`할 것인지 아니면 `reject`할 것인지 결정합니다.
5. *전송 대상* 은 *수신자* 가 전송 요청을 `accept`한 경우에만 수신 전화를 받습니다.

### <a name="transfer-terminology"></a>전송 용어

- 전송자 - 전송 요청을 시작하는 사람
- 수신자 - 전송자에 의해 전송 대상으로 전송되는 수신자
- 전송 대상 - 이 대상으로 전송됩니다.

현재 통화를 전송하려면 `transfer` 동기 API를 사용하면 됩니다. `transfer`는 `disableForwardingAndUnanswered` 플래그를 설정할 수 있는 선택 사항인 `TransferCallOptions`를 사용합니다.

- `disableForwardingAndUnanswered` = false - *전송 대상* 이 전송 호출에 응답하지 않으면 *전송 대상* 착신 전환 및 응답 없음 설정을 따릅니다.
- `disableForwardingAndUnanswered` = true - *전송 대상* 이 전송 호출에 응답하지 않으면 전송 시도가 종료됩니다.

```js
// transfer target can be ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

전송을 통해 `transferStateChanged` 및 `transferRequested` 이벤트를 구독할 수 있습니다. `transferRequsted` 이벤트는 `call` 인스턴스에서 제공되고, `transferStateChanged` 이벤트와 전송 `state` 및 `error`는 `transfer` 인스턴스에서 제공됩니다.

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if transfer request failed
```

수신자는 전송자가 `transferRequested` 이벤트에서 시작한 전송 요청을 `transferRequestedEventArgs`의 `accept()` 또는 `reject()`를 통해 수락하거나 거부할 수 있습니다. `transferRequestedEventArgs`에서 `targetParticipant` 정보인 `accept` 및 `reject` 메서드에 액세스할 수 있습니다.

```js
// Transferee to accept the transfer request
callTransferApi.on('transferRequested', args => {
  args.accept();
});

// Transferee to reject the transfer request
callTransferApi.on('transferRequested', args => {
  args.reject();
});
```

## <a name="eventing-model"></a>이벤트 모델
현재 값을 검사하고 향후 값에 대한 업데이트 이벤트를 구독해야 합니다.

### <a name="properties"></a>속성

```js
// Inspect current value
console.log(object.property);

// Subscribe to value updates
object.on('propertyChanged', () => {
    // Inspect new value
    console.log(object.property)
});

// Unsubscribe from updates:
object.off('propertyChanged', () => {});



// Example for inspecting call state
console.log(call.state);
call.on('stateChanged', () => {
    console.log(call.state);
});
call.off('stateChanged', () => {});
```

### <a name="collections"></a>컬렉션
```js
// Inspect current collection
object.collection.forEach(v => {
    console.log(v);
});

// Subscribe to collection updates
object.on('collectionUpdated', e => {
    // Inspect new values added to the collection
    e.added.forEach(v => {
        console.log(v);
    });
    // Inspect values removed from the collection
    e.removed.forEach(v => {
        console.log(v);
    });
});

// Unsubscribe from updates:
object.off('collectionUpdated', () => {});



// Example for subscribing to remote participants and their video streams
call.remoteParticipants.forEach(p => {
    subscribeToRemoteParticipant(p);
})

call.on('remoteParticipantsUpdated', e => {
    e.added.forEach(p => { subscribeToRemoteParticipant(p) })
    e.removed.forEach(p => { unsubscribeFromRemoteParticipant(p) })
});

function subscribeToRemoteParticipant(p) {
    console.log(p.state);
    p.on('stateChanged', () => { console.log(p.state); });
    p.videoStreams.forEach(v => { subscribeToRemoteVideoStream(v) });
    p.on('videoStreamsUpdated', e => { e.added.forEach(v => { subscribeToRemoteVideoStream(v) }) })
}
```
