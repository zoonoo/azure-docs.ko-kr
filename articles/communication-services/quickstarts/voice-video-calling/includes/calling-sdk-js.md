---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 5542ca2f50152e7588f32e9ac8717f691fdb4d63
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376609"
---
## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../create-communication-resource.md).
- `User Access Token`호출 클라이언트를 사용 하도록 설정 하는입니다. 을 [ `User Access Token` 다운로드 하는 방법](../../access-tokens.md) 에 대 한 자세한 내용은
- 선택 사항: [응용 프로그램에 대 한 호출 추가를 시작](../getting-started-with-calling.md) 하기 위한 빠른 시작을 완료 합니다.

## <a name="setting-up"></a>설치

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

명령을 사용 하 여 `npm install` 및 JavaScript 용 공용 클라이언트 라이브러리를 호출 하는 Azure 통신 서비스를 설치 합니다.

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
| AzureCommunicationUserCredential | AzureCommunicationUserCredential 클래스는 CallAgent를 인스턴스화하는 데 사용되는 CommunicationUserCredential 인터페이스를 구현합니다. |


## <a name="initialize-the-callclient-create-callagent-and-access-devicemanager"></a>CallClient를 초기화 하 고 Callclient를 만든 후 DeviceManager에 액세스 합니다.

새 인스턴스를 인스턴스화합니다 `CallClient` . 로 거 인스턴스와 같은 사용자 지정 옵션을 사용 하 여 구성할 수 있습니다.
`CallClient`이 인스턴스화된 후 `CallAgent` 에는 인스턴스에서 메서드를 호출 하 여 인스턴스를 만들 수 있습니다 `createCallAgent` `CallClient` . 이는 인스턴스 개체를 비동기적으로 반환 `CallAgent` 합니다.
`createCallAgent`메서드는를 인수로 사용 하 여 `CommunicationUserCredential` [사용자 액세스 토큰](../../access-tokens.md)을 허용 합니다.
`DeviceManager`먼저 callAgent 인스턴스를 만들어야 합니다. 그런 다음 `getDeviceManager` 인스턴스에서 메서드를 사용 하 여 `CallClient` devicemanager를 가져올 수 있습니다.

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationUserCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential);
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-an-outgoing-call"></a>나가는 호출을 수행 합니다.

호출을 만들고 시작 하려면 CallAgent에서 Api 중 하나를 사용 하 고 통신 서비스 관리 클라이언트 라이브러리를 통해 만든 사용자를 제공 해야 합니다.

호출 생성 및 시작은 동기식입니다. 호출 인스턴스를 사용 하면 호출 이벤트를 구독할 수 있습니다.

## <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>사용자에 게 1:1 호출 또는 사용자 및 PSTN을 사용 하 여 1: n 호출

다른 통신 서비스 사용자에 대 한 호출을 수행 하려면에서 메서드를 호출 하 `call` `callAgent` 고 [통신 서비스 관리 라이브러리를 사용 하 여 만든](../../access-tokens.md)CommunicationUser를 전달 합니다.

```js
const oneToOneCall = callAgent.call([CommunicationUser]);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>사용자 및 PSTN을 사용 하 여 1: n 호출
> [!WARNING]
> PSTN 호출은 현재 비공개 미리 보기 상태입니다. 액세스를 위해 [초기 도입자 프로그램에 적용](https://aka.ms/ACS-EarlyAdopter)합니다.
사용자 및 PSTN 번호에 대 한 1: n 호출을 수행 하려면 두 개의 호출 수신자에 대해 CommunicationUser와 전화 번호를 지정 해야 합니다.
PSTN 호출을 허용 하도록 통신 서비스 리소스를 구성 해야 합니다.
```js

const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const groupCall = callAgent.call([userCallee, pstnCallee], placeCallOptions);

```

### <a name="place-a-11-call-with-video-camera"></a>비디오 카메라를 사용 하 여 1:1 호출
> [!WARNING]
> 현재 나가는 로컬 비디오 스트림이 하나만 있을 수 있습니다.
비디오 전화를 걸려면 deviceManager API를 사용 하 여 로컬 카메라를 열거 해야 `getCameraList` 합니다.
원하는 카메라를 선택한 후에는이를 사용 하 여 인스턴스를 생성 하 `LocalVideoStream` 고이를 `videoOptions` 배열 내에 있는 항목으로 `localVideoStream` 메서드에 전달 `call` 합니다.
전화를 연결 하면 선택한 카메라에서 다른 참가자로 비디오 스트림을 자동으로 보내기 시작 합니다.
```js
const deviceManager = await callClient.getDeviceManager();
const videoDeviceInfo = deviceManager.getCameraList()[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.call(['acsUserId'], placeCallOptions);

```

### <a name="join-a-group-call"></a>그룹 호출 조인
새 그룹 호출을 시작 하거나 진행 중인 그룹 호출을 조인 하려면 ' join ' 메서드를 사용 하 고 속성을 사용 하 여 개체를 전달 합니다 `groupId` . 값은 GUID 여야 합니다.
```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```

## <a name="call-management"></a>호출 관리

통화 속성에 액세스 하 고 비디오 및 오디오와 관련 된 설정을 관리 하는 호출 중에 다양 한 작업을 수행할 수 있습니다.

### <a name="call-properties"></a>호출 속성
* 이 호출의 고유 ID (문자열)를 가져옵니다.
```js

const callId: string = call.id;

```

* 호출에서 다른 참가자에 대 한 자세한 내용을 보려면 인스턴스에서 컬렉션을 검사 합니다 `remoteParticipant` `call` . 배열에 목록 개체가 포함 되어 있습니다. `RemoteParticipant`
```js
const remoteParticipants = call.remoteParticipants;
```

* 호출이 들어오는 경우 호출자의 id입니다. Id는 다음 형식 중 하나입니다. `Identifier`
```js

const callerIdentity = call.callerIdentity;

```

* 호출의 상태를 가져옵니다.
```js

const callState = call.state;

```
호출의 현재 상태를 나타내는 문자열을 반환 합니다.
* ' None '-초기 호출 상태
* ' 들어옴 '-호출이 수신 됨을 나타내고, 수락 하거나 거부 해야 합니다.
* ' 연결 중 '-호출이 배치 되거나 수락 되 면 초기 전환 상태입니다.
* ' 울림 '-나가는 호출의 경우, 원격 참가자에 대 한 호출이 걸려 있음을 나타냅니다.
* ' EarlyMedia '-호출이 연결 되기 전에 알림이 재생 되는 상태를 나타냅니다.
* ' Connected '-호출이 연결 되어 있습니다.
* ' 보유 '-호출이 대기 중 이며, 로컬 끝점과 원격 참가자 간에 미디어가 전달 되지 않습니다.
* ' 연결 끊기 '-호출이 ' Disconnected ' 상태가 되기 전에 전환 상태입니다.
* ' Disconnected '-최종 호출 상태


* 지정 된 호출이 종료 된 이유를 확인 하려면 속성을 검사 `callEndReason` 합니다.
```js

const callEndReason = call.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* 현재 호출이 들어오는 호출 인지 확인 하려면 속성을 검사 하 여를 `isIncoming` 반환 `Boolean` 합니다.
```js
const isIncoming = call.isIncoming;
```

*  현재 마이크가 음소거 되어 있는지 확인 하려면 속성을 검사 하 여를 `muted` 반환 `Boolean` 합니다.
```js

const muted = call.isMicrophoneMuted;

```

* 지정 된 끝점에서 화면 공유 스트림을 보내고 있는지 확인 하려면 속성을 확인 하 여를 `isScreenSharingOn` 반환 `Boolean` 합니다.
```js

const isScreenSharingOn = call.isScreenSharingOn;

```

* 활성 비디오 스트림을 검사 하려면 `localVideoStreams` 컬렉션에 개체가 포함 되어 있는지 확인 합니다. `LocalVideoStream`
```js

const localVideoStreams = call.localVideoStreams;

```

### <a name="mute-and-unmute"></a>음소거 및 음소거 해제

로컬 끝점을 음소거 하거나 음소거를 해제 하려면 `mute` 및 비동기 api를 사용할 수 있습니다 `unmute` .

```js

//mute local device 
await call.mute();

//unmute local device 
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>로컬 비디오 보내기 시작 및 중지


비디오를 시작 하려면 개체의 메서드를 사용 하 여 카메라를 열거 해야 `getCameraList` `deviceManager` 합니다. 그런 다음 `LocalVideoStream` 원하는 카메라를 인수로 메서드에 전달 하는 새 인스턴스를 만듭니다 `startVideo` .


```js
const localVideoStream = new LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);

```

비디오 전송을 성공적으로 시작 하면 인스턴스는 `LocalVideoStream` 호출 인스턴스의 컬렉션에 추가 됩니다 `localVideoStreams` .

```js

call.localVideoStreams[0] === localVideoStream;

```

로컬 비디오를 중지 하려면 `localVideoStream` 컬렉션에서 사용할 수 있는 인스턴스를 전달 합니다 `localVideoStreams` .

```js

await call.stopVideo(localVideoStream);

```

인스턴스에서를 호출 하 여 비디오가 전송 되는 동안 다른 카메라 장치로 전환할 수 있습니다 `switchSource` `localVideoStream` .

```js
const source callClient.getDeviceManager().getCameraList()[1];
localVideoStream.switchSource(source);

```

## <a name="remote-participants-management"></a>원격 참여자 관리

모든 원격 참가자는 형식으로 표시 `RemoteParticipant` 되며 `remoteParticipants` 호출 인스턴스에서 컬렉션을 통해 사용할 수 있습니다.

### <a name="list-participants-in-a-call"></a>호출의 참가자 나열
`remoteParticipants`컬렉션은 지정 된 호출에서 원격 참가자 목록을 반환 합니다.

```js

call.remoteParticipants; // [remoteParticipant, remoteParticipant....]

```

### <a name="remote-participant-properties"></a>원격 참가자 속성
원격 참가자에 게는 연결 된 속성 및 컬렉션 집합이 있습니다.

* 이 원격 참가자에 대 한 식별자를 가져옵니다.
Id는 ' Identifier ' 형식 중 하나입니다.
```js
const identifier = remoteParticipant.identifier;
//It can be one of:
// { communicationUserId: '<ACS_USER_ID'> } - object representing ACS User
// { phoneNumber: '<E.164>' } - object representing phone number in E.164 format
```

* 이 원격 참가자의 상태를 가져옵니다.
```js

const state = remoteParticipant.state;
```
상태는 다음 중 하나일 수 있습니다.
* ' Idle '-초기 상태
* 참가자가 호출에 연결 하는 동안 ' 연결 중 '-전환 상태
* ' 연결 됨 '-참가자가 호출에 연결 되었습니다.
* ' 보유 '-참가자가 보류 중입니다.
* ' EarlyMedia '-참가자가 호출에 연결 되기 전에 알림이 재생 됩니다.
* ' Disconnected '-최종 상태-참가자가 호출에서 연결이 끊겼습니다.

참가자가 전화를 떠난 이유를 알아보려면 속성을 검사 합니다 `callEndReason` .
```js

const callEndReason = remoteParticipant.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* 이 원격 참가자가 음소거 되어 있는지 여부를 확인 하려면 속성을 검사 `isMuted` 합니다. `Boolean`
```js
const isMuted = remoteParticipant.isMuted;
```

* 이 원격 참가자가 말하는 지 여부를 확인 하려면 반환 하는 속성을 검사 `isSpeaking` 합니다. `Boolean`
```js

const isSpeaking = remoteParticipant.isSpeaking;

```

* 지정 된 참가자가이 호출에서 보내는 모든 비디오 스트림을 검사 하려면 `videoStreams` 컬렉션에 개체가 포함 되어 있는지 확인 합니다. `RemoteVideoStream`
```js

const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]

```


### <a name="add-a-participant-to-a-call"></a>호출에 참가자 추가

호출 (사용자 또는 전화 번호)에 참가자를 추가 하려면를 호출 하면 `addParticipant` 됩니다.
' Identifier ' 형식 중 하나를 제공 합니다.
그러면 원격 참가자 인스턴스가 동기적으로 반환 됩니다.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier);
```

### <a name="remove-participant-from-a-call"></a>호출에서 참가자 제거

통화 (사용자 또는 전화 번호)에서 참가자를 제거 하려면를 호출 하면 `removeParticipant` 됩니다.
' Identifier ' 형식 중 하나를 전달 해야 합니다 .이는 참가자가 호출에서 제거 되 면 비동기적으로 해결 됩니다.
참가자도 컬렉션에서 제거 됩니다 `remoteParticipants` .

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>원격 참가자 비디오 스트림 렌더링

원격 참가자의 비디오 스트림과 화면 공유 스트림을 나열 하려면 다음 컬렉션을 검사 합니다 `videoStreams` .

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.type;
```
 
을 렌더링 하려면 `RemoteVideoStream` 이벤트를 구독 해야 `isAvailableChanged` 합니다.
`isAvailable`속성이로 변경 되 면 `true` 원격 참가자가 스트림을 보냅니다.
이 작업이 완료 되 면의 새 인스턴스를 만든 `Renderer` 다음 `RendererView` 비동기 메서드를 사용 하 여 새 인스턴스를 만듭니다 `createView` .  그러면 `view.target` 모든 UI 요소에 연결할 수 있습니다.
원격 스트림의 가용성이 변경 될 때마다 전체 렌더러를 제거 하거나 특정 항목을 유지 하도록 선택할 수 `RendererView` 있지만이 경우 빈 비디오 프레임이 표시 됩니다.

```js
let renderer: Renderer;
const displayVideo = () => {
    renderer = new Renderer(remoteParticipantStream);
    const view = await renderer.createView();
    htmlElement.appendChild(view.target);
}
remoteParticipantStream.on('availabilityChanged', async () => {
    if (remoteParticipantStream.isAvailable) {
        displayVideo();
    } else {
        renderer.dispose();
    }
});
if (remoteParticipantStream.isAvailable) {
    displayVideo();
}
```

### <a name="remote-video-stream-properties"></a>원격 비디오 스트림 속성
원격 비디오 스트림에는 다음과 같은 속성이 있습니다.

* `Id` -원격 비디오 스트림의 ID
```js
const id: number = remoteVideoStream.id;
```

* `StreamSize` -원격 비디오 스트림의 크기 (너비/높이)
```js
const size: {width: number; height: number} = remoteVideoStream.size;
```

* `MediaStreamType` -' Video ' 또는 ' ScreenSharing ' 일 수 있습니다.
```js
const type: MediaStreamType = remoteVideoStream.type;
```
* `isAvailable` -원격 참가자 끝점이 현재 스트림을 전송 하 고 있는지 여부를 나타냅니다.
```js
const type: boolean = remoteVideoStream.isAvailable;
```

### <a name="renderer-methods-and-properties"></a>렌더러 메서드 및 속성

* `RendererView`나중에 응용 프로그램 UI에 연결 하 여 원격 비디오 스트림을 렌더링할 수 있는 인스턴스를 만듭니다.
```js
renderer.createView()
```

* 렌더러 및 연결 된 모든 인스턴스를 삭제 `RendererView` 합니다.
```js
renderer.dispose()
```


### <a name="rendererview-methods-and-properties"></a>RendererView 메서드 및 속성
를 만들 때 `RendererView` 및 속성을 지정할 수 있습니다 `scalingMode` `mirrored` .
크기 조정 모드는 ' Stretch ', ' 자르기 ' 또는 ' Fit ' 일 수 있습니다 `Mirrored` .를 지정 하면 렌더링 된 스트림이 상하로 대칭 이동 됩니다.

```js
const rendererView: RendererView = renderer.createView({ scalingMode, mirrored });
```
지정 된 모든 `RendererView` 인스턴스에는 `target` 렌더링 화면을 나타내는 속성이 있습니다. 이는 응용 프로그램 UI에 연결 해야 합니다.
```js
document.body.appendChild(rendererView.target);
```

나중에 메서드를 호출 하 여 크기 조정 모드를 업데이트할 수 있습니다 `updateScalingMode` .
```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>디바이스 관리

`DeviceManager` 오디오/비디오 스트림을 전송 하는 호출에 사용할 수 있는 로컬 장치를 열거할 수 있습니다. 또한 네이티브 브라우저 API를 사용 하 여 사용자가 마이크 및 카메라에 액세스할 수 있는 권한을 요청할 수 있습니다.

메서드를 호출 하 여에 액세스할 수 있습니다 `deviceManager` `callClient.getDeviceManager()` .
> [!WARNING]
> 현재는 `callAgent` DeviceManager에 대 한 액세스 권한을 얻기 위해 먼저 개체를 인스턴스화해야 합니다.

```js

const deviceManager = await callClient.getDeviceManager();

```

### <a name="enumerate-local-devices"></a>로컬 장치 열거

로컬 장치에 액세스 하려면 Device Manager 열거 메서드를 사용할 수 있습니다. 열거는 동기 작업입니다.

```js

//  Get a list of available video devices for use.
const localCameras = deviceManager.getCameraList(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = deviceManager.getMicrophoneList(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = deviceManager.getSpeakerList(); // [AudioDeviceInfo, AudioDeviceInfo...]

```

### <a name="set-default-microphonespeaker"></a>기본 마이크/스피커 설정

장치 관리자를 사용 하 여 호출을 시작할 때 사용할 기본 장치를 설정할 수 있습니다.
클라이언트 기본값을 설정 하지 않으면 통신 서비스는 OS 기본값으로 대체 됩니다.

```js

// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.getMicrophone();

// Set the microphone device to use.
await deviceMicrophone.setMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.getSpeaker();

// Set the speaker device to use.
await deviceManager.setSpeaker(AudioDeviceInfo);

```

### <a name="local-camera-preview"></a>로컬 카메라 미리 보기

`DeviceManager`및 `Renderer` 를 사용 하 여 로컬 카메라에서 스트림 렌더링을 시작할 수 있습니다. 이 스트림은 다른 참가자로 전송 되지 않습니다. 로컬 미리 보기 피드입니다. 비동기 작업입니다.

```js
const localVideoDevice = deviceManager().getCameraList()[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-cameramicrophone"></a>카메라/마이크에 대 한 권한 요청

사용자에 게 다음을 통해 카메라/마이크 사용 권한을 부여 하 라는 메시지를 표시 합니다.

```js
const result = await deviceManager.askDevicePermission(audio: true, video: true);
```
`audio`및 사용 권한이 부여 되었는지 여부를 나타내는 개체를 사용 하 여 비동기적으로 확인 됩니다 `video` .
```js
console.log(result.audio);
console.log(result.video);
```

다음을 호출 하 여 지정 된 형식에 대 한 현재 권한 상태를 검사할 수 있습니다 `getPermissionState` .

```js

const result = deviceManager.getPermissionState('Microphone'); // for microphone permission state
const result = deviceManager.getPermissionState('Camera'); // for camera permission state

console.log(result); // 'Granted' | 'Denied' | 'Prompt' | 'Unknown';

```

## <a name="eventing-model"></a>이벤트 모델

값이 변경 될 때 알림을 받으려면 대부분의 속성 및 컬렉션을 구독할 수 있습니다.

### <a name="properties"></a>속성
이벤트를 구독 하려면 `property changed` :

```js

const eventHandler = () => {
    // check current value of a property, value is not passed to callback
    console.log(object.property);
};
object.on('propertyNameChanged',eventHandler);

// To unsubscribe:

object.off('propertyNameChanged',eventHandler);

```

### <a name="collections"></a>컬렉션
이벤트를 구독 하려면 `collection updated` :

```js

const eventHandler = (e) => {
    // check added elements
    console.log(e.added);
    // check removed elements
    console.log(e.removed);
};
object.on('collectionNameUpdated',eventHandler);

// To unsubscribe:

object.off('collectionNameUpdated',eventHandler);

```
