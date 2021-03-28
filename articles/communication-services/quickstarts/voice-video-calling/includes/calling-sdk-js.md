---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 49054d9bbde67dc3670ec444e4b60c3ddf503db5
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645459"
---
## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../create-communication-resource.md).
- 호출 클라이언트를 사용 하도록 설정 하는 사용자 액세스 토큰입니다. 자세한 내용은 [액세스 토큰 만들기 및 관리](../../access-tokens.md)를 참조 하세요.
- 선택 사항: 빠른 시작을 완료 하 여 [응용 프로그램에 음성 통화를 추가](../getting-started-with-calling.md)합니다.

## <a name="install-the-sdk"></a>SDK 설치

> [!NOTE]
> 이 문서에서는 호출 SDK의 버전 1.0.0-beta. 10을 사용 합니다.

명령을 사용 하 여 `npm install` 및 JavaScript 용 일반 sdk를 호출 하는 Azure 통신 서비스를 설치 합니다.
이 문서는 버전 1.0.0-beta. 10의 호출 라이브러리에서 형식을 참조 합니다.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>개체 모델

다음 클래스와 인터페이스는 SDK를 호출 하는 Azure Communication Services의 주요 기능 중 일부를 처리 합니다.

| 이름                             | 설명                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                      | 호출 SDK의 주 진입점입니다.                                                                       |
| `CallAgent`                        | 호출을 시작 하 고 관리 하는 데 사용 됩니다.                                                                                            |
| `DeviceManager`                    | 미디어 장치를 관리 하는 데 사용 됩니다.                                                                                           |
| `AzureCommunicationTokenCredential` | 를 `CommunicationTokenCredential` 인스턴스화하는 데 사용 되는 인터페이스를 구현 `callAgent` 합니다. |

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>CallClient 인스턴스를 초기화 하 고, Callclient 인스턴스를 만들고, deviceManager에 액세스 합니다.

새 인스턴스를 만듭니다 `CallClient` . 로거 인스턴스와 같은 사용자 지정 옵션을 사용하여 구성할 수 있습니다.

인스턴스가 있는 경우 인스턴스에 `CallClient` `CallAgent` 대해 메서드를 호출 하 여 인스턴스를 만들 수 있습니다 `createCallAgent` `CallClient` . 이렇게 하면 `CallAgent` 인스턴스 개체가 비동기적으로 반환됩니다.

`createCallAgent`메서드는를 `CommunicationTokenCredential` 인수로 사용 합니다. [사용자 액세스 토큰](../../access-tokens.md)을 허용 합니다.

인스턴스를 만든 후에 `callAgent` `getDeviceManager` 는 인스턴스에 대해 메서드를 사용 하 여에 액세스할 수 있습니다 `CallClient` `deviceManager` .

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

호출을 만들고 시작 하려면의 Api 중 하나를 사용 하 `callAgent` 고 통신 서비스 ID SDK를 통해 만든 사용자를 제공 합니다.

통화 만들기와 시작은 동기적입니다. 호출 인스턴스를 사용 하면 호출 이벤트를 구독할 수 있습니다.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>1: n 호출을 사용자 또는 PSTN에 추가

다른 통신 서비스 사용자를 호출 하려면에서 메서드를 사용 하 `startCall` `callAgent` 고 `CommunicationUserIdentifier` [통신 서비스 관리 라이브러리를 사용 하 여 만든](../../access-tokens.md)수신자를 전달 합니다.

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

공용 스위치 전화 네트워크 (PSTN)에 대 한 호출을 수행 하려면에서 메서드를 사용 하 `startCall` `callAgent` 고 수신자를 전달 `PhoneNumberIdentifier` 합니다. PSTN 호출을 허용하도록 Communication Services 리소스를 구성해야 합니다.

PSTN 번호를 호출 하는 경우 대체 호출자 ID를 지정 합니다. 대체 호출자 ID는 PSTN 호출에서 호출자를 식별 하는 전화 번호 (E. 164 standard 기반)입니다. 전화 수신자가 들어오는 호출에 대해 확인 하는 전화 번호입니다.

> [!NOTE]
> PSTN 통화는 현재 프라이빗 미리 보기로 제공됩니다. 액세스를 위해 [초기 도입자 프로그램에 적용](https://aka.ms/ACS-EarlyAdopter)합니다.

1:1 호출의 경우 다음 코드를 사용 합니다.

```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

1: n 호출의 경우 다음 코드를 사용 합니다.

```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>비디오 카메라로 1:1 전화 걸기

> [!IMPORTANT]
> 현재 발신 로컬 비디오 스트림은 하나만 가능합니다.

비디오 전화를 걸려면의 메서드를 사용 하 여 카메라를 지정 해야 `getCameras()` `deviceManager` 합니다.

카메라를 선택한 후에는이를 사용 하 여 `LocalVideoStream` 인스턴스를 생성 합니다. 내에서이 `videoOptions` 를 배열 내의 항목으로 `localVideoStream` `startCall` 메서드에 전달 합니다.

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
localVideoStream = new LocalVideoStream(camera);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

전화를 연결 하면 자동으로 선택 된 카메라에서 다른 참가자로 비디오 스트림을 보내기 시작 합니다. 이는 `Call.Accept()` 비디오 옵션 및 비디오 옵션에도 적용 됩니다 `CallAgent.join()` .

### <a name="join-a-group-call"></a>그룹 통화 참가

> [!NOTE]
> `groupId`매개 변수는 시스템 메타 데이터로 간주 되며 Microsoft에서 시스템을 실행 하는 데 필요한 작업에 사용할 수 있습니다. 값에 개인 데이터를 포함 하지 않습니다 `groupId` . Microsoft는이 매개 변수를 개인 데이터로 간주 하지 않으며 해당 콘텐츠가 Microsoft 직원에 게 표시 되거나 장기적으로 저장 될 수 있습니다.
>
> `groupId`매개 변수는 데이터를 GUID 형식으로 지정 해야 합니다. 시스템에서 개인 데이터로 간주 되지 않는 임의로 생성 된 Guid를 사용 하는 것이 좋습니다.
>

새 그룹 호출을 시작 하거나 진행 중인 그룹 호출을 조인 하려면 메서드를 사용 하 고 속성을 사용 하 `join` 여 개체를 전달 `groupId` 합니다. `groupId`값은 GUID 여야 합니다.

```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>팀 참여 모임

팀 회의에 참여 하려면 메서드를 사용 하 `join` 고 모임 링크 또는 좌표를 전달 합니다.

모임 링크를 사용 하 여 조인:

```js
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);
```

모임 좌표를 사용 하 여 조인:

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

`callAgent`인스턴스는 `incomingCall` 로그인 된 id가 들어오는 호출을 받을 때 이벤트를 내보냅니다. 이 이벤트를 수신 하려면 다음 옵션 중 하나를 사용 하 여 구독 합니다.

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

이벤트에는 `incomingCall` `incomingCall` 허용 하거나 거부할 수 있는 인스턴스가 포함 됩니다.

## <a name="manage-calls"></a>호출 관리

호출 하는 동안 호출 속성에 액세스 하 고 비디오 및 오디오 설정을 관리할 수 있습니다.

### <a name="check-call-properties"></a>통화 속성 확인

호출의 고유 ID (문자열)를 가져옵니다.

   ```js
    const callId: string = call.id;
   ```

' Call ' 인스턴스에서 컬렉션을 검사 하 여 호출의 다른 참가자에 대해 알아봅니다 `remoteParticipants` .

   ```js
   const remoteParticipants = call.remoteParticipants;
   ```

들어오는 호출의 호출자를 식별 합니다.

   ```js
   const callerIdentity = call.callerInfo.identifier;
   ```

   `identifier` 는 형식 중 하나입니다 `CommunicationIdentifier` .

호출의 상태를 가져옵니다.

   ```js
   const callState = call.state;
   ```

   다음과 같은 통화의 현재 상태를 나타내는 문자열을 반환합니다.

  - `None`: 초기 호출 상태입니다.
  - `Connecting`: 호출이 배치 되거나 수락 될 때의 초기 전환 상태입니다.
  - `Ringing`: 나가는 호출의 경우는 원격 참가자에 대 한 호출이 링 됨을 나타냅니다. 그 `Incoming` 쪽에 있습니다.
  - `EarlyMedia`: 호출이 연결 되기 전에 알림이 재생 되는 상태를 나타냅니다.
  - `Connected`: 호출이 연결 되었음을 나타냅니다.
  - `LocalHold`: 로컬 참가자가 보류 중인 호출 임을 나타냅니다. 로컬 끝점과 원격 참가자 간에 미디어가 전달 되지 않습니다.
  - `RemoteHold`: 원격 참가자가 보류 중인 호출 임을 나타냅니다. 로컬 끝점과 원격 참가자 간에 미디어가 전달 되지 않습니다.
  - `Disconnecting`: 호출이 상태로 전환 되기 전의 상태 `Disconnected` 입니다.
  - `Disconnected`: 최종 호출 상태입니다. 네트워크 연결이 끊어지면 상태는 2 분 후로 변경 됩니다 `Disconnected` .

속성을 검사 하 여 호출이 종료 된 이유를 확인 합니다 `callEndReason` .

   ```js
   const callEndReason = call.callEndReason;
   const callEndReasonCode = callEndReason.code // (number) code associated with the reason
   const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
   ```

속성을 검사 하 여 현재 호출이 들어오거나 나가는 지 알아봅니다 `direction` . `CallDirection`를 반환합니다.

  ```js
   const isIncoming = call.direction == 'Incoming';
   const isOutgoing = call.direction == 'Outgoing';
   ```

현재 마이크가 음소거 되어 있는지 확인 합니다. `Boolean`를 반환합니다.

   ```js
   const muted = call.isMuted;
   ```

속성을 확인 하 여 화면 공유 스트림이 지정 된 끝점에서 전송 되 고 있는지 확인 `isScreenSharingOn` 합니다. `Boolean`를 반환합니다.

   ```js
   const isScreenSharingOn = call.isScreenSharingOn;
   ```

컬렉션을 확인 하 여 활성 비디오 스트림을 검사 `localVideoStreams` 합니다. `LocalVideoStream`개체를 반환 합니다.

   ```js
   const localVideoStreams = call.localVideoStreams;
   ```

### <a name="check-a-callended-event"></a>CallEnded 이벤트 확인

통화가 종료되면 `call` 인스턴스는 `callEnded` 이벤트를 내보냅니다. 이 이벤트를 수신 대기 하려면 다음 코드를 사용 하 여 구독 합니다.

```js
const callEndHander = async (args: { callEndReason: CallEndReason }) => {
    console.log(args.callEndReason)
};

call.on('callEnded', callEndHander);
```

### <a name="mute-and-unmute"></a>음소거 및 음소거 해제

로컬 끝점을 음소거 하거나 음소거를 해제 하기 위해 `mute` 및 비동기 api를 사용할 수 있습니다 `unmute` .

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>로컬 비디오 보내기 시작 및 중지

비디오를 시작 하려면 개체에서 메서드를 사용 하 여 카메라를 지정 해야 `getCameras` `deviceManager` 합니다. 그런 다음 `LocalVideoStream` 원하는 카메라를 메서드에 인수로 전달 하 여의 새 인스턴스를 만듭니다 `startVideo` .

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
const localVideoStream = new LocalVideoStream(camera);
await call.startVideo(localVideoStream);
```

비디오 보내기를 성공적으로 시작 하면 `LocalVideoStream` 호출 인스턴스의 컬렉션에 인스턴스가 추가 됩니다 `localVideoStreams` .

```js
call.localVideoStreams[0] === localVideoStream;
```

로컬 비디오를 중지 하려면 `localVideoStream` 컬렉션에서 사용할 수 있는 인스턴스를 전달 합니다 `localVideoStreams` .

```js
await call.stopVideo(localVideoStream);
```

인스턴스에서를 호출 하 여 비디오를 전송 하는 동안 다른 카메라 장치로 전환할 수 있습니다 `switchSource` `localVideoStream` .

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

## <a name="manage-remote-participants"></a>원격 참가자 관리

모든 원격 참가자는 `RemoteParticipant` 형식으로 표시되며 호출 인스턴스에서 `remoteParticipants` 컬렉션을 통해 사용할 수 있습니다.

### <a name="list-the-participants-in-a-call"></a>호출의 참가자 나열

`remoteParticipants`컬렉션은 호출에서 원격 참가자 목록을 반환 합니다.

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="access-remote-participant-properties"></a>원격 참가자 속성 액세스

원격 참가자에 게는 연결 된 속성 및 컬렉션 집합이 있습니다.

- `CommunicationIdentifier`: 원격 참가자에 대 한 식별자를 가져옵니다. Id는 다음 형식 중 하나입니다 `CommunicationIdentifier` .

  ```js
  const identifier = remoteParticipant.identifier;
  ```

  다음 유형 중 하나일 수 있습니다 `CommunicationIdentifier` .

  - `{ communicationUserId: '<ACS_USER_ID'> }`: ACS 사용자를 나타내는 개체입니다.
  - `{ phoneNumber: '<E.164>' }`: E. 164 형식의 전화 번호를 나타내는 개체입니다.
  - `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }`: 팀 사용자를 나타내는 개체입니다.
  - `{ id: string }`: 다른 식별자 형식에 맞지 않는 개체 repredenting identifier

- `state`: 원격 참가자의 상태를 가져옵니다.

  ```js
  const state = remoteParticipant.state;
  ```

  상태는 다음과 같을 수 있습니다.

  - `Idle`: 초기 상태.
  - `Connecting`: 참가자가 호출에 연결 하는 동안 상태를 전환 합니다.
  - `Ringing`: 참가자가 신호를 합니다.
  - `Connected`: 참가자가 호출에 연결 되어 있습니다.
  - `Hold`: 참가자가 보류 중입니다.
  - `EarlyMedia`: 참가자가 호출에 연결 하기 전에 재생 되는 알림입니다.
  - `Disconnected`: 최종 상태. 호출에서 참가자의 연결이 끊어졌습니다. 원격 참가자의 네트워크 연결이 끊어지면 2 분 후에 상태가로 변경 `Disconnected` 됩니다.

- `callEndReason`: 참가자가 전화를 떠난 이유를 확인 하려면 속성을 확인 합니다 `callEndReason` .

  ```js
  const callEndReason = remoteParticipant.callEndReason;
  const callEndReasonCode = callEndReason.code // (number) code associated with the reason
  const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
  ```

- `isMuted` 상태: 원격 참가자가 음소거 되어 있는지 확인 하려면 속성을 확인 `isMuted` 합니다. `Boolean`를 반환합니다.

  ```js
  const isMuted = remoteParticipant.isMuted;
  ```

- `isSpeaking` 상태: 원격 참가자가 말하고 있는지 확인 하려면 속성을 확인 `isSpeaking` 합니다. `Boolean`를 반환합니다.

  ```js
  const isSpeaking = remoteParticipant.isSpeaking;
  ```

- `videoStreams`:이 호출에서 지정 된 참가자가 보내는 모든 비디오 스트림을 검사 하려면 컬렉션을 확인 `videoStreams` 합니다. `RemoteVideoStream`개체를 포함 합니다.

  ```js
  const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
  ```
- `displayName`:이 원격 참가자에 대 한 표시 이름을 가져오려면 `displayName` 속성에서 반환 문자열을 검사 합니다. 

  ```js
  const displayName = remoteParticipant.displayName;
  ```

### <a name="add-a-participant-to-a-call"></a>통화에 참가자 추가

통화에 참가자 (사용자 또는 전화 번호)를 추가 하려면를 사용할 수 있습니다 `addParticipant` . 형식 중 하나를 제공 `Identifier` 합니다. 인스턴스를 반환 합니다 `remoteParticipant` .

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>통화에서 참가자 제거

통화에서 참가자 (사용자 또는 전화 번호)를 제거 하려면를 호출 하면 `removeParticipant` 됩니다. 형식 중 하나를 전달 해야 `Identifier` 합니다. 이는 참가자가 호출에서 제거 된 후 비동기적으로 확인 됩니다. 참가자도 컬렉션에서 제거 됩니다 `remoteParticipants` .

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

렌더링 하려면 `RemoteVideoStream` 이벤트를 구독 해야 `isAvailableChanged` 합니다. `isAvailable` 속성이 `true`로 변경되면 원격 참가자가 스트림을 보내는 것입니다. 그 후에는의 새 인스턴스를 만든 `VideoStreamRenderer` 다음 `VideoStreamRendererView` 비동기 메서드를 사용 하 여 새 인스턴스를 만듭니다 `createView` .  그런 다음 `view.target` 모든 UI 요소에 연결할 수 있습니다.

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

- `mediaStreamType`: 또는 일 수 있습니다 `Video` `ScreenSharing` .

  ```js
  const type: MediaStreamType = remoteVideoStream.mediaStreamType;
  ```

- `isAvailable`: 원격 참가자 끝점이 현재 스트림을 전송 하 고 있는지 여부를 나타냅니다.

  ```js
  const type: boolean = remoteVideoStream.isAvailable;
  ```

### <a name="videostreamrenderer-methods-and-properties"></a>VideoStreamRenderer 메서드 및 속성

`VideoStreamRendererView`응용 프로그램 UI에 연결 하 여 원격 비디오 스트림을 렌더링 하 고, 비동기 `createView()` 메서드를 사용 하 고, 스트림이 렌더링 될 준비가 되었을 때를 확인 하 고, `target` `video` DOM 트리의 아무 곳에 나 추가할 수 있는 요소를 나타내는 속성이 있는 개체를 반환할 수 있는 인스턴스를 만듭니다.

  ```js
  videoStreamRenderer.createView()
  ```

`videoStreamRenderer`및 연결 된 모든 인스턴스를 삭제 합니다 `VideoStreamRendererView` .

  ```js
  videoStreamRenderer.dispose()
  ```

### <a name="videostreamrendererview-methods-and-properties"></a>VideoStreamRendererView 메서드 및 속성

를 만들 때 `VideoStreamRendererView` 및 속성을 지정할 수 있습니다 `scalingMode` `isMirrored` . `scalingMode` , 또는 일 수 있습니다 `Stretch` `Crop` `Fit` . `isMirrored`가 지정 된 경우 렌더링 된 스트림은 세로로 대칭 이동 됩니다.

```js
const videoStreamRendererView: VideoStreamRendererView = await videoStreamRenderer.createView({ scalingMode, isMirrored });
```

모든 `VideoStreamRendererView` 인스턴스에는 `target` 렌더링 화면을 나타내는 속성이 있습니다. 응용 프로그램 UI에서이 속성을 연결 합니다.

```js
htmlElement.appendChild(view.target);
```

`scalingMode`메서드를 호출 하 여를 업데이트할 수 있습니다 `updateScalingMode` .

```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>디바이스 관리

에서는 `deviceManager` 호출에서 오디오 및 비디오 스트림을 전송할 수 있는 로컬 장치를 지정할 수 있습니다. 또한 네이티브 브라우저 API를 사용 하 여 다른 사용자의 마이크와 카메라에 대 한 액세스 권한을 요청 하는 데 도움이 됩니다.

`deviceManager`메서드를 호출 하 여에 액세스할 수 있습니다 `callClient.getDeviceManager()` .

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>로컬 장치 가져오기

로컬 장치에 액세스 하려면에서 열거형 메서드를 사용할 수 있습니다 `deviceManager` .

```js
//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-the-default-microphone-and-speaker"></a>기본 마이크 및 스피커 설정

에서 `deviceManager` 호출을 시작 하는 데 사용할 기본 장치를 설정할 수 있습니다. 클라이언트 기본값이 설정 되지 않은 경우 통신 서비스는 운영 체제 기본값을 사용 합니다.

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

### <a name="request-permission-to-camera-and-microphone"></a>카메라 및 마이크에 대 한 권한 요청

사용자에 게 카메라 및 마이크 사용 권한을 부여 하 라는 메시지를 표시 합니다.

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

이는 `audio` 및 사용 권한이 부여 되었는지 여부를 나타내는 개체를 사용 하 여 확인 됩니다 `video` .

```js
console.log(result.audio);
console.log(result.video);
```

## <a name="record-calls"></a>레코드 호출

[!INCLUDE [Private Preview Notice](../../../includes/private-preview-include-section.md)]

통화 레코딩은 핵심 `Call` API의 확장 기능입니다. 먼저 레코딩 기능 API 개체를 가져와야 합니다.

```js
const callRecordingApi = call.api(Features.Recording);
```

그런 다음 호출을 기록 하 고 있는지 확인 하려면 `isRecordingActive` 의 속성을 검사 `callRecordingApi` 합니다. `Boolean`를 반환합니다.

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

## <a name="transfer-calls"></a>전송 호출

통화 전송은 핵심 `Call` API의 확장 기능입니다. 먼저 전송 기능 API 개체를 가져와야 합니다.

```js
const callTransferApi = call.api(Features.Transfer);
```

호출 전송에는 세 가지 파티가 포함 됩니다.

- *전송자 또는*: 전송 요청을 시작 하는 사람입니다.
- *Transferee*: 전송 되는 사람입니다.
- *전송 대상*:로 전송 되는 사람입니다.

전송은 다음 단계를 수행 합니다.

1. *전송자 또는* *transferee* 사이에 이미 연결 된 호출이 있습니다. 전송 *또는* *transferee* 에서 *전송 대상* 으로의 호출을 전송 하기로 결정 합니다.
1. *전송자 또는* 는 API를 호출 합니다 `transfer` .
1. *Transferee* 는 `accept` 이벤트를 `reject` 사용 하 여 *전송 대상* 에 대 한 전송 요청을 할지 또는 전송 요청으로 할지 결정 합니다 `transferRequested` .
1. *전송 대상은* *transferee* 전송 요청을 수락 하는 경우에만 들어오는 호출을 수신 합니다.

현재 호출을 전송 하기 위해 API를 사용할 수 있습니다 `transfer` . `transfer` 는 `transferCallOptions` 플래그를 설정 하는 데 사용할 수 있는 옵션을 사용 합니다 `disableForwardingAndUnanswered` .

- `disableForwardingAndUnanswered = false`: 전송 *대상이* 전송 호출에 응답 하지 않는 경우 전송은 전송 *대상* 전달 및 응답 없음 설정을 따릅니다.
- `disableForwardingAndUnanswered = true`: 전송 *대상이* 전송 호출에 응답 하지 않으면 전송 시도가 종료 됩니다.

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

`transfer`API를 사용 하면 및 이벤트를 구독할 수 있습니다 `transferStateChanged` `transferRequested` . `transferRequested`이벤트는 인스턴스에서 발생 하 고 `call` `transferStateChanged` 이벤트 및 전송 `state` 및 인스턴스에서 가져옵니다 `error` `transfer` .

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

*Transferee* 는  `transferRequested` `accept()` 또는에서 또는를 사용 하 여 이벤트에서 시작 된 전송 요청을 수락 하거나 거부할 수 `reject()` 있습니다 `transferRequestedEventArgs` . `targetParticipant`에서 정보 및 또는 메서드에 액세스할 수 있습니다 `accept` `reject` `transferRequestedEventArgs` .

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

## <a name="learn-about-eventing-models"></a>이벤트 모델에 대 한 자세한 정보

현재 값을 검사 하 고 나중에 값에 대 한 업데이트 이벤트를 구독 합니다.

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
