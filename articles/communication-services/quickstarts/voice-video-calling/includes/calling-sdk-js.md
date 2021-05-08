---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 2ecbd207c4b1946a69b01f43ec2bc77d29b1a8c9
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106073164"
---
## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../create-communication-resource.md).
- 호출 클라이언트를 사용하도록 설정하는 사용자 액세스 토큰입니다. 자세한 내용은 [액세스 토큰 만들기 및 관리](../../access-tokens.md)를 참조하세요.
- 선택 사항: 빠른 시작을 완료하여 [애플리케이션에 음성 통화를 추가](../getting-started-with-calling.md)합니다.

## <a name="install-the-sdk"></a>SDK 설치

> [!NOTE]
> 이 문서에서는 ACS 통화 웹 SDK를 사용합니다.

`npm install` 명령을 사용하여 JavaScript용 Azure Communication Services 통화 SDK 및 일반 SDK를 설치합니다.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>개체 모델

Azure Communication Services 통화 SDK의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

| 이름                             | Description                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                      | 통화 SDK의 주 진입점입니다.                                                                       |
| `CallAgent`                        | 통화를 시작하고 관리하는 데 사용됩니다.                                                                                            |
| `DeviceManager`                    | 미디어 디바이스를 관리하는 데 사용됩니다.                                                                                           |
| `AzureCommunicationTokenCredential` | `callAgent`를 인스턴스화하는 데 사용되는 `CommunicationTokenCredential` 인터페이스를 구현합니다. |

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>CallClient 인스턴스를 초기화하고, CallAgent 인스턴스를 만들고, deviceManager에 액세스합니다.

새 `CallClient` 인스턴스를 만듭니다. 로거 인스턴스와 같은 사용자 지정 옵션을 사용하여 구성할 수 있습니다.

`CallClient` 인스턴스가 있으면 `CallClient` 인스턴스에서 `createCallAgent` 메서드를 호출하여 `CallAgent` 인스턴스를 만들 수 있습니다. 이렇게 하면 `CallAgent` 인스턴스 개체가 비동기적으로 반환됩니다.

`createCallAgent` 메서드는 `CommunicationTokenCredential`을 인수로 사용합니다. [사용자 액세스 토큰](../../access-tokens.md)이 허용됩니다.

`callAgent` 인스턴스를 만든 후에 `CallClient` 인스턴스에서 `getDeviceManager` 메서드를 사용하여 `deviceManager`에 액세스할 수 있습니다.

```js
// Set the logger's log level
setLogLevel('verbose');
// Redirect logger output to wherever desired. By default it logs to console
AzureLogger.log = (...args) => { console.log(...args) };
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-a-call"></a>전화 걸기

통화를 만들고 시작하려면 `callAgent`에서 API 중 하나를 사용하여 Communication Services ID SDK를 통해 만든 사용자를 제공합니다.

통화 만들기와 시작은 동기적입니다. 통화 인스턴스를 사용하여 통화 이벤트를 구독할 수 있습니다.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>사용자 또는 PSTN에게 1:n 전화 걸기

다른 Communication Services 사용자에게 전화를 걸려면 `callAgent`에서 `startCall` 메서드를 사용하고, [Communication Services 관리 라이브러리를 사용하여 만든](../../access-tokens.md) 수신자의 `CommunicationUserIdentifier`를 전달합니다.

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

PSTN(공중 교환 전화망)으로 전화를 걸려면 `callAgent`에서 `startCall` 메서드를 사용하여 수신자의 `PhoneNumberIdentifier`를 전달합니다. PSTN 호출을 허용하도록 Communication Services 리소스를 구성해야 합니다.

PSTN 번호로 전화를 거는 경우 대체 발신자 ID를 지정합니다. 대체 발신자 ID는 PSTN 통화의 발신자를 식별하는 전화 번호(E.164 표준 기반)입니다. 통화 수신자가 수신 통화에 대해 확인하는 전화번호입니다.

> [!NOTE]
> PSTN 통화는 현재 프라이빗 미리 보기로 제공됩니다. 액세스하려면 [얼리어답터 프로그램을 신청](https://aka.ms/ACS-EarlyAdopter)하세요.

1:1 통화의 경우 다음 코드를 사용합니다.

```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

1: n 통화의 경우 다음 코드를 사용합니다.

```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>비디오 카메라로 1:1 전화 걸기

> [!IMPORTANT]
> 현재 발신 로컬 비디오 스트림은 하나만 가능합니다.

영상 전화를 걸려면 `deviceManager`의 `getCameras()` 메서드를 사용하여 카메라를 지정해야 합니다.

카메라를 선택한 후에는 이를 사용하여 `LocalVideoStream` 인스턴스를 생성합니다. 이를 `videoOptions` 내에서 `localVideoStream` 배열 내의 한 항목으로 `startCall` 메서드에 전달합니다.

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
localVideoStream = new LocalVideoStream(camera);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

전화가 연결되면 선택한 카메라에서 다른 참가자로 비디오 스트림을 자동으로 보내기 시작합니다. 이는 `Call.Accept()` 비디오 옵션과 `CallAgent.join()` 비디오 옵션에도 적용됩니다.

### <a name="join-a-group-call"></a>그룹 통화 참가

> [!NOTE]
> `groupId` 매개 변수는 시스템 메타데이터로 간주되며 Microsoft가 시스템을 실행하는 데 필요한 작업에 사용할 수 있습니다. `groupId` 값에 개인 데이터를 포함하지 마세요. Microsoft는 이 매개 변수를 개인 데이터로 간주하지 않으며 해당 내용이 Microsoft 직원에게 표시되거나 장기적으로 저장될 수 있습니다.
>
> `groupId` 매개 변수는 데이터를 GUID 형식으로 지정해야 합니다. 시스템에서 개인 데이터로 간주되지 않는 임의로 생성된 GUID를 사용하는 것이 좋습니다.
>

새 그룹 통화를 시작하거나 진행 중인 그룹 통화에 참가하려면 `join` 메서드를 사용하고 `groupId` 속성을 통해 개체를 전달합니다. `groupId` 값은 GUID여야 합니다.

```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>Teams 회의 참가
> [!NOTE]
> 이 API는 개발자를 위한 미리 보기로 제공되며 수신한 피드백을 기반으로 변경될 수 있습니다. 프로덕션 환경에서 이 API를 사용하지 마세요. 이 API를 사용하려면 ACS 통화 웹 SDK의 '베타' 릴리스를 사용하세요.

Teams 모임에 참가하려면 `join` 메서드를 사용하고 모임 링크 또는 좌표를 전달합니다.

모임 링크를 사용하여 참가:

```js
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);
```

모임 좌표를 사용하여 조인:

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receive-an-incoming-call"></a>수신 전화 받기

`callAgent` 인스턴스는 로그인된 ID가 전화를 받을 때 `incomingCall` 이벤트를 내보냅니다. 이 이벤트를 수신 대기하려면 다음 옵션 중 하나를 사용하여 구독합니다.

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {

    //Get incoming call ID
    var incomingCallId = incomingCall.id

    // Get information about caller
    var callerInfo = incomingCall.callerInfo

    // Accept the call
    var call = await incomingCall.accept();

    // Reject the call
    incomingCall.reject();

    // Subscribe to callEnded event and get the call end reason
     incomingCall.on('callEnded', args => {
        console.log(args.callEndReason);
    });

    // callEndReason is also a property of IncomingCall
    var callEndReason = incomingCall.callEndReason;
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

`incomingCall` 이벤트에는 허용 또는 거부할 수 있는 `incomingCall` 인스턴스가 포함됩니다.

## <a name="manage-calls"></a>통화 관리

통화 도중 통화 속성에 액세스하고 비디오 및 오디오 설정을 관리할 수 있습니다.

### <a name="check-call-properties"></a>통화 속성 확인

통화의 고유 ID(문자열)를 가져옵니다.

   ```js
    const callId: string = call.id;
   ```

통화의 다른 참가자에 대해 알아보려면 '통화' 인스턴스의 `remoteParticipants` 컬렉션을 검사합니다.

   ```js
   const remoteParticipants = call.remoteParticipants;
   ```

수신 통화의 발신자를 식별합니다.

   ```js
   const callerIdentity = call.callerInfo.identifier;
   ```

   `identifier`는 `CommunicationIdentifier` 유형 중 하나입니다.

통화의 상태를 가져옵니다.

   ```js
   const callState = call.state;
   ```

   다음과 같은 통화의 현재 상태를 나타내는 문자열을 반환합니다.

  - `None`: 초기 통화 상태입니다.
  - `Connecting`: 전화를 걸었거나 수락되었을 때의 초기 전환 상태입니다.
  - `Ringing`: 발신 통화의 경우 원격 참가자의 전화 벨이 울리고 있다는 뜻입니다. 상대방에서는 `Incoming`입니다.
  - `EarlyMedia`: 통화가 연결되기 전에 알림이 재생되는 상태를 나타냅니다.
  - `Connected`: 통화가 연결되었음을 나타냅니다.
  - `LocalHold`: 로컬 참가자가 통화를 대기 상태로 전환했음을 나타냅니다. 로컬 엔드포인트와 원격 참가자 간에 미디어가 전달되지 않습니다.
  - `RemoteHold`: 원격 참가자가 통화를 대기 상태로 전환했음을 나타냅니다. 로컬 엔드포인트와 원격 참가자 간에 미디어가 전달되지 않습니다.
  - `Disconnecting`: 통화가 `Disconnected` 상태로 전환되기 전의 전환 상태입니다.
  - `Disconnected`: 최종 통화 상태입니다. 네트워크 연결이 끊어지면 2분 후 상태가 `Disconnected`로 변경됩니다.

`callEndReason` 속성을 검사하여 통화가 종료된 이유를 확인합니다.

   ```js
   const callEndReason = call.callEndReason;
   const callEndReasonCode = callEndReason.code // (number) code associated with the reason
   const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
   ```

`direction` 속성을 검사하여 현재 통화가 수신 또는 발신인지 확인합니다. `CallDirection`를 반환합니다.

  ```js
   const isIncoming = call.direction == 'Incoming';
   const isOutgoing = call.direction == 'Outgoing';
   ```

현재 마이크가 음소거되었는지 확인합니다. `Boolean`를 반환합니다.

   ```js
   const muted = call.isMuted;
   ```

`isScreenSharingOn` 속성을 검사하여 지정된 엔드포인트에서 화면 공유 스트림을 보내고 있는지 확인합니다. `Boolean`를 반환합니다.

   ```js
   const isScreenSharingOn = call.isScreenSharingOn;
   ```

`localVideoStreams` 컬렉션을 확인하여 활성 비디오 스트림을 검사합니다. `LocalVideoStream` 개체를 반환합니다.

   ```js
   const localVideoStreams = call.localVideoStreams;
   ```

### <a name="check-a-callended-event"></a>callEnded 이벤트 확인

통화가 종료되면 `call` 인스턴스는 `callEnded` 이벤트를 내보냅니다. 이 이벤트를 수신 대기하려면 다음 코드를 사용하여 구독합니다.

```js
const callEndHander = async (args: { callEndReason: CallEndReason }) => {
    console.log(args.callEndReason)
};

call.on('callEnded', callEndHander);
```

### <a name="mute-and-unmute"></a>음소거 및 음소거 해제

로컬 엔드포인트를 음소거하거나 음소거를 해제하려면 `mute` 및 `unmute` 비동기 API를 사용할 수 있습니다.

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>로컬 비디오 보내기 시작 및 중지

비디오를 시작하려면 `deviceManager` 개체에서 `getCameras` 메서드를 사용하여 카메라를 지정해야 합니다. 그런 다음, 새 인스턴스 `LocalVideoStream`을 만들어서 원하는 카메라를 `startVideo` 메서드에 인수로 전달합니다.

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
const localVideoStream = new LocalVideoStream(camera);
await call.startVideo(localVideoStream);
```

비디오 전송을 성공적으로 시작하면 통화 인스턴스의 `localVideoStreams` 컬렉션에 `LocalVideoStream` 인스턴스가 추가됩니다.

```js
call.localVideoStreams[0] === localVideoStream;
```

로컬 비디오를 중지하려면 `localVideoStreams` 컬렉션에서 사용할 수 있는 `localVideoStream` 인스턴스를 전달합니다.

```js
await call.stopVideo(localVideoStream);
```

`localVideoStream` 인스턴스에서 `switchSource`를 호출하여 비디오가 전송되는 동안 다른 카메라 디바이스로 전환할 수 있습니다.

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

## <a name="manage-remote-participants"></a>원격 참가자 관리

모든 원격 참가자는 `RemoteParticipant` 형식으로 표시되며 호출 인스턴스에서 `remoteParticipants` 컬렉션을 통해 사용할 수 있습니다.

### <a name="list-the-participants-in-a-call"></a>통화 참가자 나열

`remoteParticipants` 컬렉션은 통화의 원격 참가자 목록을 반환합니다.

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="access-remote-participant-properties"></a>원격 참가자 속성에 액세스

원격 참가자에게는 연결된 속성 및 컬렉션 집합이 있습니다.

- `CommunicationIdentifier`: 원격 참가자의 식별자를 가져옵니다. ID는 `CommunicationIdentifier` 유형 중 하나입니다.

  ```js
  const identifier = remoteParticipant.identifier;
  ```

  다음 `CommunicationIdentifier` 유형 중 하나일 수 있습니다.

  - `{ communicationUserId: '<ACS_USER_ID'> }`: ACS 사용자를 나타내는 개체입니다.
  - `{ phoneNumber: '<E.164>' }`: E.164 형식의 전화 번호를 나타내는 개체입니다.
  - `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }`: Teams 사용자를 나타내는 개체입니다.
  - `{ id: string }`: 다른 유형에 해당하지 않는 식별자를 나타내는 개체입니다.

- `state`: 원격 참가자의 상태를 가져옵니다.

  ```js
  const state = remoteParticipant.state;
  ```

  상태는 다음과 같을 수 있습니다.

  - `Idle`: 초기 상태입니다.
  - `Connecting`: 참가자가 통화에 연결되는 동안의 전환 상태입니다.
  - `Ringing`: 참가자가 전화를 거는 중입니다.
  - `Connected`: 참가자가 통화에 연결되었습니다.
  - `Hold`: 참가자가 대기 중입니다.
  - `EarlyMedia`: 참가자가 통화에 연결되기 전에 재생되는 알림입니다.
  - `Disconnected`: 최종 상태입니다. 참가자의 통화 연결이 끊겼습니다. 원격 참가자의 네트워크 연결이 끊어지면 2분 후 상태가 `Disconnected`로 변경됩니다.

- `callEndReason`: 참가자가 통화를 종료한 이유를 알아보려면 `callEndReason` 속성을 확인합니다.

  ```js
  const callEndReason = remoteParticipant.callEndReason;
  const callEndReasonCode = callEndReason.code // (number) code associated with the reason
  const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
  ```

- `isMuted` 상태: 원격 참가자가 음소거되어 있는지 확인하려면 `isMuted` 속성을 확인합니다. `Boolean`를 반환합니다.

  ```js
  const isMuted = remoteParticipant.isMuted;
  ```

- `isSpeaking` 상태: 원격 참가자가 말하는 중인지 확인하려면 `isSpeaking` 속성을 확인합니다. `Boolean`를 반환합니다.

  ```js
  const isSpeaking = remoteParticipant.isSpeaking;
  ```

- `videoStreams`: 특정 참가자가 이 통화에서 보내는 모든 비디오 스트림을 검사하려면 `videoStreams` 컬렉션을 확인합니다. `RemoteVideoStream` 개체가 포함됩니다.

  ```js
  const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
  ```
- `displayName`: 이 원격 참가자의 표시 이름을 가져오려면 `displayName` 속성을 검사합니다. 문자열을 반환합니다. 

  ```js
  const displayName = remoteParticipant.displayName;
  ```

### <a name="add-a-participant-to-a-call"></a>통화에 참가자 추가

통화에 참가자(사용자 또는 전화번호)를 추가하려면 `addParticipant`를 사용할 수 있습니다. `Identifier` 유형 중 하나를 제공합니다. `remoteParticipant` 인스턴스를 반환합니다.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>통화에서 참가자 제거

통화에서 참가자(사용자 또는 전화 번호)를 제거하려면 `removeParticipant`를 호출할 수 있습니다. `Identifier` 유형 중 하나를 전달해야 합니다. 그러면 통화에서 참가자가 제거된 후 이를 비동기적으로 해결합니다. 참가자는 `remoteParticipants` 컬렉션에서도 제거됩니다.

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

`RemoteVideoStream`을 렌더링하려면 `isAvailableChanged` 이벤트를 구독해야 합니다. `isAvailable` 속성이 `true`로 변경되면 원격 참가자가 스트림을 보내는 것입니다. 이 경우 `VideoStreamRenderer`의 새 인스턴스를 만든 다음, 비동기 `createView` 메서드를 사용하여 새 `VideoStreamRendererView` 인스턴스를 만듭니다.  그러면 모든 UI 요소에 `view.target`을 연결할 수 있습니다.

원격 스트림의 가용성이 변경될 때마다 전체 `VideoStreamRenderer`, 즉, 특정 `VideoStreamRendererView`를 제거하거나 유지하도록 선택할 수 있지만, 이 경우 빈 비디오 프레임이 표시됩니다.

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let videoStreamRenderer: VideoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await videoStreamRenderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('isAvailableChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            videoStreamRenderer.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        displayVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>원격 비디오 스트림 속성

원격 비디오 스트림에는 다음과 같은 속성이 있습니다.

- `id`: 원격 비디오 스트림의 ID입니다.

  ```js
  const id: number = remoteVideoStream.id;
  ```

- `mediaStreamType`: `Video` 또는 `ScreenSharing`일 수 있습니다.

  ```js
  const type: MediaStreamType = remoteVideoStream.mediaStreamType;
  ```

- `isAvailable`: 원격 참가자 엔드포인트가 스트림을 능동적으로 전송하고 있는지 여부입니다.

  ```js
  const type: boolean = remoteVideoStream.isAvailable;
  ```

### <a name="videostreamrenderer-methods-and-properties"></a>VideoStreamRenderer 메서드 및 속성

애플리케이션 UI에 연결하여 원격 비디오 스트림을 렌더링할 수 있는 `VideoStreamRendererView` 인스턴스를 만들고, 비동기 `createView()` 메서드를 사용합니다. 이 메서드는 스트림을 렌더링할 준비가 되었을 때를 확인하고 DOM 트리의 아무 곳에나 추가될 수 있는 `video` 요소를 나타내는 `target` 속성이 있는 개체를 반환합니다.

  ```js
  videoStreamRenderer.createView()
  ```

`videoStreamRenderer` 및 모든 연결된 `VideoStreamRendererView` 인스턴스를 삭제합니다.

  ```js
  videoStreamRenderer.dispose()
  ```

### <a name="videostreamrendererview-methods-and-properties"></a>VideoStreamRendererView 메서드 및 속성

`VideoStreamRendererView`를 만들 때 `scalingMode` 및 `isMirrored` 속성을 지정할 수 있습니다. `scalingMode`는 `Stretch`, `Crop` 또는 `Fit`일 수 있습니다. `isMirrored`가 지정된 경우 렌더링된 스트림은 세로로 대칭 이동됩니다.

```js
const videoStreamRendererView: VideoStreamRendererView = await videoStreamRenderer.createView({ scalingMode, isMirrored });
```

모든 `VideoStreamRendererView` 인스턴스에는 렌더링 화면을 나타내는 `target` 속성이 있습니다. 애플리케이션 UI에서 이 속성을 연결합니다.

```js
htmlElement.appendChild(view.target);
```

나중에 `updateScalingMode` 메서드를 호출하여 `scalingMode`를 업데이트할 수 있습니다.

```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>디바이스 관리

`deviceManager`에서는 통화에서 오디오 및 비디오 스트림을 전송할 수 있는 로컬 디바이스를 지정할 수 있습니다. 또한 네이티브 브라우저 API를 사용하여 다른 사용자의 마이크 및 카메라에 액세스할 수 있는 권한을 요청하는 데에도 사용됩니다.

`callClient.getDeviceManager()` 메서드를 호출하여 `deviceManager`에 액세스할 수 있습니다.

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>로컬 디바이스 가져오기

로컬 디바이스에 액세스하려면 `deviceManager`에서 열거 메서드를 사용합니다.

```js
//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-the-default-microphone-and-speaker"></a>기본 마이크 및 스피커 설정

`deviceManager`에서 통화를 시작하는 데 사용할 기본 디바이스를 설정할 수 있습니다. 클라이언트 기본값이 설정되지 않은 경우 Communication Services는 운영 체제 기본값을 사용합니다.

```js
// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(localMicrophones[0]);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(localSpeakers[0]);
```

### <a name="local-camera-preview"></a>로컬 카메라 미리 보기

`deviceManager` 및 `VideoStreamRenderer`를 사용하여 로컬 카메라에서 스트림 렌더링을 시작할 수 있습니다. 이 스트림은 다른 참가자에게 전송되지 않는 로컬 미리 보기 피드입니다.

```js
const cameras = await deviceManager.getCameras();
const camera = cameras[0];
const localCameraStream = new LocalVideoStream(camera);
const videoStreamRenderer = new VideoStreamRenderer(localCameraStream);
const view = await videoStreamRenderer.createView();
htmlElement.appendChild(view.target);

```

### <a name="request-permission-to-camera-and-microphone"></a>카메라 및 마이크 사용 권한 요청

사용자에게 카메라 및 마이크 사용 권한을 부여하라는 메시지를 표시합니다.

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

이는 `audio` 및 `video` 사용 권한이 부여되었는지 여부를 나타내는 개체를 사용하여 확인됩니다.

```js
console.log(result.audio);
console.log(result.video);
```

## <a name="record-calls"></a>통화 녹음
> [!NOTE]
> 이 API는 개발자를 위한 미리 보기로 제공되며 수신한 피드백을 기반으로 변경될 수 있습니다. 프로덕션 환경에서 이 API를 사용하지 마세요. 이 API를 사용하려면 ACS 통화 웹 SDK의 '베타' 릴리스를 사용하세요.

통화 레코딩은 핵심 `Call` API의 확장 기능입니다. 먼저 레코딩 기능 API 개체를 가져와야 합니다.

```js
const callRecordingApi = call.api(Features.Recording);
```

그런 다음, 통화가 녹음되고 있는지 확인하려면 `callRecordingApi`의 `isRecordingActive` 속성을 검사합니다. `Boolean`를 반환합니다.

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

## <a name="transfer-calls"></a>통화 전송
> [!NOTE]
> 이 API는 개발자를 위한 미리 보기로 제공되며 수신한 피드백을 기반으로 변경될 수 있습니다. 프로덕션 환경에서 이 API를 사용하지 마세요. 이 API를 사용하려면 ACS 통화 웹 SDK의 '베타' 릴리스를 사용하세요.

통화 전송은 핵심 `Call` API의 확장 기능입니다. 먼저 전송 기능 API 개체를 가져와야 합니다.

```js
const callTransferApi = call.api(Features.Transfer);
```

통화 전환에는 세 명의 당사자가 포함됩니다.

- '전송자': 전송 요청을 시작한 사람입니다.
- '피전송자': 이 사람의 통화가 전송됩니다.
- '전송 대상': 통화가 이 사람에게 전송됩니다.

전송은 다음 단계를 따릅니다.

1. '전송자'와 '피전송자' 사이에 이미 연결된 통화가 있습니다. '전송자'가 '피전송자'와의 통화를 '전송 대상'으로 전송하기로 결정합니다.
1. '전송자'가 `transfer` API를 호출합니다.
1. '피전송자'는 `transferRequested` 이벤트를 사용하여 '전송 대상'으로의 전송 요청을 `accept`할지 아니면 `reject`할지 결정합니다.
1. '전송 대상'은 '피전송자'가 전송 요청을 수락한 경우에만 수신 통화를 받습니다.

현재 통화를 전송하려면 `transfer` API를 사용하면 됩니다. `transfer`는 사용자가 `disableForwardingAndUnanswered` 플래그를 설정할 수 있는 `transferCallOptions` 옵션을 허용합니다.

- `disableForwardingAndUnanswered = false`: '전송 대상'이 전송 통화에 응답하지 않으면 전송은 '전송 대상' 착신 전환 및 응답 없음 설정을 따릅니다.
- `disableForwardingAndUnanswered = true`: '전송 대상'이 전송 통화에 응답하지 않으면 전송 시도가 종료됩니다.

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

`transfer` API를 사용하여 `transferStateChanged` 및 `transferRequested` 이벤트를 구독할 수 있습니다. `transferRequested` 이벤트는 `call` 인스턴스에서 발생하고, `transferStateChanged` 이벤트와 전송 `state` 및 `error`는 `transfer` 인스턴스에서 발생합니다.

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

'피전송자'는 `transferRequested` 이벤트에서 `transferRequestedEventArgs`의 `accept()` 또는 `reject()`를 사용하여 '전송자'가 시작한 전송 요청을 수락하거나 거부할 수 있습니다. `transferRequestedEventArgs`에서 `targetParticipant` 정보와 `accept` 또는 `reject` 메서드에 액세스할 수 있습니다.

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

## <a name="learn-about-eventing-models"></a>이벤트 모델에 대한 자세한 정보

현재 값을 검사하고 향후 값에 대한 업데이트 이벤트를 구독합니다.

### <a name="properties"></a>속성

```js
// Inspect the current value
console.log(object.property);

// Subscribe to value updates
object.on('propertyChanged', () => {
    // Inspect new value
    console.log(object.property)
});

// Unsubscribe from updates:
object.off('propertyChanged', () => {});



// Example for inspecting a call state
console.log(call.state);
call.on('stateChanged', () => {
    console.log(call.state);
});
call.off('stateChanged', () => {});
```

### <a name="collections"></a>컬렉션

```js
// Inspect the current collection
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
