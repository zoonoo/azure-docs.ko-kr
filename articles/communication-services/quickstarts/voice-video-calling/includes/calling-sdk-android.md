---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: c0213b050745712a5c77d4861b9cfba4fc953dfd
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940073"
---
## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 배포 된 통신 서비스 리소스입니다. [통신 서비스 리소스를 만듭니다](../../create-communication-resource.md).
- `User Access Token`호출 클라이언트를 사용 하도록 설정 하는입니다. 을 [ `User Access Token` 다운로드 하는 방법](../../access-tokens.md) 에 대 한 자세한 내용은
- 선택 사항: [응용 프로그램에 대 한 호출 추가를 시작](../getting-started-with-calling.md) 하기 위한 빠른 시작을 완료 합니다.

## <a name="setting-up"></a>설치

### <a name="install-the-package"></a>패키지 설치

<!-- TODO: update with instructions on how to download, install and add package to project -->
프로젝트 수준 gradle를 찾고 `mavenCentral()` 및의 저장소 목록에 추가 해야 합니다. `buildscript``allprojects`
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
그런 다음 모듈 수준 gradle에서 종속성 섹션에 다음 줄을 추가 합니다.

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.1'
    ...
}

```

## <a name="object-model"></a>개체 모델

다음 클래스와 인터페이스는 클라이언트 라이브러리를 호출 하는 Azure Communication Services의 주요 기능 중 일부를 처리 합니다.

| 이름                                  | 설명                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| CallClient는 호출 하는 클라이언트 라이브러리에 대 한 주 진입점입니다.|
| CallAgent | CallAgent는 호출을 시작 하 고 관리 하는 데 사용 됩니다. |
| CommunicationUserCredential | CommunicationUserCredential는 CallAgent를 인스턴스화하는 토큰 자격 증명으로 사용 됩니다.|

## <a name="initialize-the-callclient-create-a-callagent-and-access-the-devicemanager"></a>CallClient를 초기화 하 고, Callclient를 만들고, DeviceManager에 액세스 합니다.

인스턴스를 만들려면 `CallAgent` 인스턴스에서 메서드를 호출 해야 `createCallAgent` `CallClient` 합니다. 이는 인스턴스 개체를 비동기적으로 반환 `CallAgent` 합니다.
`createCallAgent`메서드는를 인수로 사용 하 여 `CommunicationUserCredential` [액세스 토큰](../../access-tokens.md)을 캡슐화 합니다.
에 액세스 하려면 `DeviceManager` callAgent 인스턴스를 먼저 만든 후 메서드를 사용 `CallClient.getDeviceManager` 하 여 devicemanager를 가져올 수 있습니다.

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationUserCredential tokenCredential = new CommunicationUserCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = await callClient.createCallAgent((appContext, tokenCredential).get();
DeviceManage deviceManager = await callClient.getDeviceManager().get();
```

## <a name="place-an-outgoing-call-and-join-a-group-call"></a>나가는 호출을 수행 하 고 그룹 호출을 조인 합니다.

호출을 만들고 시작 하려면 메서드를 호출 하 고 호출 `CallClient.call()` 수신자의를 제공 해야 `Identifier` 합니다.
그룹 호출을 조인 하려면 메서드를 호출 하 `CallClient.join()` 고 groupId를 제공 해야 합니다. 그룹 Id는 GUID 또는 UUID 형식 이어야 합니다.

호출 생성 및 시작은 동기식입니다. 호출 인스턴스를 사용 하면 호출에 대 한 모든 이벤트를 구독할 수 있습니다.

### <a name="place-a-11-call-to-a-user"></a>사용자에 게 1:1 호출 추가
다른 통신 서비스 사용자에 대 한 호출을 수행 하려면에서 메서드를 호출 하 `call` `callAgent` 고 키를 사용 하 여 개체를 전달 `communicationUserId` 합니다.
```java
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
CommunicationUser acsUserId = new CommunicationUser(<USER_ID>);
CommunicationUser participants[] = new CommunicationUser[]{ acsUserId };
call oneToOneCall = callAgent.call(appContext, participants, startCallOptions);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>사용자 및 PSTN을 사용 하 여 1: n 호출
> [!WARNING]
> 현재 PSTN 호출을 통해 사용자에 대 한 1: n 호출 및 PSTN 번호를 지정할 수 없습니다. 전화 수신자의 전화 번호를 지정 해야 합니다.
PSTN 호출을 허용 하도록 통신 서비스 리소스를 구성 해야 합니다.
```java
CommunicationUser acsUser1 = new CommunicationUser(<USER_ID>);
PhoneNumber acsUser2 = new PhoneNumber("<PHONE_NUMBER>");
CommunicationIdentifier participants[] = new CommunicationIdentifier[]{ acsUser1, acsUser2 };
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
Call groupCall = callClient.call(participants, startCallOptions);
```

### <a name="place-a-11-call-with-with-video-camera"></a>비디오 카메라를 사용 하 여 1:1 호출
> [!WARNING]
> 현재 하나의 나가는 로컬 비디오 스트림도 비디오를 사용 하 여 전화를 걸 수 있습니다. API를 사용 하 여 로컬 카메라를 열거 해야 `deviceManager` `getCameraList` 합니다.
원하는 카메라를 선택한 후에는이를 사용 하 여 인스턴스를 생성 하 `LocalVideoStream` 고이를 `videoOptions` 배열의 항목으로 `localVideoStream` `call` 메서드에 전달 합니다.
호출이 연결 되 면 선택한 카메라에서 다른 참가자로 비디오 스트림을 자동으로 보내기 시작 합니다.
```java
Context appContext = this.getApplicationContext();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
LocalVideoStream currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentVideoStream);

CommunicationUser[] participants = new CommunicationUser[]{ new CommunicationUser("<acs user id>") };
StartCallOptions startCallOptions = new StartCallOptions();
startCallOptions.setVideoOptions(videoOptions);
Call call = callAgent.call(context, participants, startCallOptions);
```

### <a name="join-a-group-call"></a>그룹 호출 조인
새 그룹 호출을 시작 하거나 진행 중인 그룹 호출을 조인 하려면 ' join ' 메서드를 호출 하 고 속성을 사용 하 여 개체를 전달 해야 `groupId` 합니다. 값은 GUID 여야 합니다.
```java
Context appContext = this.getApplicationContext();
GroupCallContext groupCallContext = new groupCallContext("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallContext, joinCallOptions);
```

## <a name="push-notification"></a>푸시 알림

### <a name="overview"></a>개요
모바일 푸시 알림은 모바일 장치에서 수신 하는 팝업 알림입니다. 을 호출 하는 경우 VoIP (Voice over Internet Protocol) 푸시 알림에 집중 하 게 됩니다. 푸시 알림을 등록 하 고, 푸시 알림을 처리 하 고, 푸시 알림을 등록 취소 하는 기능을 제공 합니다.

### <a name="prerequisite"></a>필수 조건

이 자습서에서는 FCM (Cloud Messaging)를 사용 하는 Firebase 계정 설정을 사용 하도록 설정 하 고 Firebase 클라우드 메시징은 ANH (Azure Notification Hub) 인스턴스에 연결 되어 있다고 가정 합니다. 자세한 내용은 [Azure에 Firebase 연결](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started) 을 참조 하세요.
또한이 자습서에서는 Android Studio 버전 3.6 이상을 사용 하 여 응용 프로그램을 빌드하는 것으로 가정 합니다.

FCM에서 알림 메시지를 받을 수 있으려면 Android 응용 프로그램에 대 한 사용 권한 집합이 필요 합니다. AndroidManifest.xml 파일에서 *<매니페스트 ... >* 또는 태그 아래에 다음 권한 집합을 추가 합니다. *</application>*

```XML
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

### <a name="register-for-push-notification"></a>푸시 알림 등록

- 푸시 알림을 등록 하려면 응용 프로그램이 장치 등록 토큰을 사용 하는 *Callagent* 인스턴스에서 registerPushNotification ()를 호출 해야 합니다.

- 장치 등록 토큰을 가져오는 방법
1. 아직 없는 경우 *종속성* 섹션에 다음 줄을 추가 하 여 Firebase 클라이언트 라이브러리를 응용 프로그램 모듈의 *gradle* 파일에 추가 해야 합니다.
```
    // Add the client library for Firebase Cloud Messaging
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

2. 프로젝트 수준의 *gradle* 파일에서 *종속성* 섹션에 다음을 추가 합니다 (아직 없는 경우).
```
    classpath 'com.google.gms:google-services:4.3.3'
```

3. 파일의 시작 부분에 다음 플러그 인을 추가 합니다 (아직 없는 경우).
```
apply plugin: 'com.google.gms.google-services'
```

4. 도구 모음에서 *지금 동기화* 를 선택 합니다.

5. 다음 코드 조각을 추가 하 여 클라이언트 응용 프로그램 인스턴스에 대 한 FCM 클라이언트 라이브러리에 의해 생성 된 장치 등록 토큰을 가져옵니다. 
- 인스턴스에 대 한 주 활동의 헤더에 이러한 가져오기를 추가 합니다. 코드 조각이 토큰을 검색 하는 데 필요 합니다.
```
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.iid.InstanceIdResult;
```
- 토큰을 검색 하려면이 코드 조각을 추가 합니다.
```
        FirebaseInstanceId.getInstance().getInstanceId()
                .addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
                    @Override
                    public void onComplete(@NonNull Task<InstanceIdResult> task) {
                        if (!task.isSuccessful()) {
                            Log.w(TAG, "getInstanceId failed", task.getException());
                            return;
                        }

                        // Get new Instance ID token
                        String deviceToken = task.getResult().getToken();
                        // Log
                        Log.d(TAG, "Device Registration token retrieved successfully");
                    }
                });
```
6. 들어오는 호출 푸시 알림에 대 한 호출 서비스 클라이언트 라이브러리에 장치 등록 토큰 등록

```java
String deviceRegistrationToken = "some_token";
try {
    callAgent.registerPushNotification(deviceRegistrationToken).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while registering for Incoming Calls Push Notifications.")
}
```

### <a name="push-notification-handling"></a>푸시 알림 처리

- 들어오는 호출 푸시 알림을 받으려면 페이로드가 있는 *Callagent* 인스턴스에 대해 *handlepushnotification ()* 를 호출 합니다.

1. FCM에서 페이로드를 가져오기 위해 필요한 단계는 다음과 같습니다.
- *FirebaseMessagingService* Firebase client library 클래스를 확장 하 고 *onMessageReceived* 메서드를 재정의 해야 하는 새 서비스 > (> 새 서비스 > 서비스)를 만듭니다. 이 메서드는 FCM가 응용 프로그램에 푸시 알림을 전달할 때 호출 되는 이벤트 처리기입니다.

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private java.util.Map<String, String> pushNotificationMessageData;

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        // Check if message contains a notification payload.
        if (remoteMessage.getNotification() != null) {
            Log.d(TAG, "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
        else {
            pushNotificationMessageData = serializeDictionaryAsJson(remoteMessage.getData());
        }
    }
}
```
- 또한 태그 내의 AndroidManifest.xml 파일에 다음 서비스 정의를 추가 합니다 <application> .

```
        <service
            android:name=".MyFirebaseMessagingService"
            android:exported="false">
            <intent-filter>
                <action android:name="com.google.firebase.MESSAGING_EVENT" />
            </intent-filter>
        </service>
```

- 페이로드를 검색 한 후에는 *Callagent* 인스턴스에서 *handlepushnotification* 메서드를 호출 하 여 처리할 *통신 서비스* 클라이언트 라이브러리에 전달할 수 있습니다.

```java
java.util.Map<String, String> pushNotificationMessageDataFromFCM = remoteMessage.getData();
try {
    callAgent.handlePushNotification(pushNotificationMessageDataFromFCM).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while handling the Incoming Calls Push Notifications.");
}
```
푸시 알림 메시지가 성공적으로 처리 되 고 모든 이벤트 처리기가 제대로 등록 되 면 응용 프로그램이 연결 됩니다.

### <a name="unregister-push-notification"></a>푸시 알림 등록 취소

- 응용 프로그램은 언제 든 지 푸시 알림을 등록 취소할 수 있습니다. CallAgent에서 메서드를 호출 하기만 하면 됩니다 `unregisterPushNotification()` .

```java
try {
    callAgent.unregisterPushNotifications().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```

## <a name="call-management"></a>호출 관리
통화 속성에 액세스 하 고 비디오 및 오디오와 관련 된 설정을 관리 하는 호출 중에 다양 한 작업을 수행할 수 있습니다.

### <a name="call-properties"></a>호출 속성
* 이 호출의 고유 Id를 가져옵니다.
```java
String callId = call.getCallId();
```

* 인스턴스의 다른 참가자에 대 한 자세한 내용은 인스턴스의 검사 컬렉션을 확인 `remoteParticipant` `call` 하세요.
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
```

* 호출이 들어오는 경우 호출자의 id입니다.
```java
CommunicationIdentifier callerId = call.getCallerId();
```

* 호출의 상태를 가져옵니다.
```java
CallState callState = call.getState();
```
호출의 현재 상태를 reprensting 하는 문자열을 반환 합니다.
* ' None '-초기 호출 상태
* ' 들어오는 '-호출이 수신 됨을 나타내며,이를 수락 하거나 거부 해야 합니다.
* ' 연결 중 '-호출이 배치 되거나 수락 되 면 초기 전환 상태입니다.
* ' 울림 '-나가는 호출의 경우, 원격 참가자에 대 한 호출이 신호를 받는 중임을 나타냅니다.
* ' EarlyMedia '-호출이 연결 되기 전에 공지가 재생 되는 상태를 나타냅니다.
* ' Connected '-호출이 연결 되어 있습니다.
* ' 보유 '-호출이 대기 중 이며, 로컬 끝점과 원격 참가자 간에 미디어가 전달 되지 않습니다.
* ' 연결 끊기 '-호출 전의 전환 상태를 ' Disconnected ' 상태로 전환 합니다.
* ' Disconnected '-최종 호출 상태


* 호출이 종료 된 이유를 알아보려면 속성을 검사 `callEndReason` 합니다.
코드/하위 코드를 포함 합니다 (문서에 대 한 TODO 링크).
```java
CallEndReason callEndReason = call.getCallEndReason();
int code = callEndReason.getCode();
int subCode = callEndReason.getSubCode();
```

* 현재 호출이 들어오는 호출 인지 확인 하려면 속성을 검사 합니다 `isIncoming` .
```java
boolean isIncoming = call.getIsIncoming();
```

*  현재 마이크가 음소거 되어 있는지 확인 하려면 속성을 검사 합니다 `muted` .
```java
boolean muted = call.getIsMicrophoneMuted();
```

* 활성 비디오 스트림을 검사 하려면 컬렉션을 확인 합니다 `localVideoStreams` .
```java
List<LocalVideoStream> localVideoStreams = call.getLocalVideoStreams();
```

### <a name="mute-and-unmute"></a>음소거 및 음소거 해제
로컬 끝점을 음소거 하거나 음소거를 해제 하려면 `mute` 및 비동기 api를 사용할 수 있습니다 `unmute` .
```java
call.mute().get();
call.unmute().get();
```

### <a name="start-and-stop-sending-local-video"></a>로컬 비디오 보내기 시작 및 중지
비디오를 시작 하려면 개체의 API를 사용 하 여 카메라를 열거 해야 `getCameraList` `deviceManager` 합니다.
그런 다음 원하는 카메라를 전달 하는 새 인스턴스를 만들고이 인스턴스를 `LocalVideoStream` API에서 인수로 전달 합니다. `startVideo`
```java
VideoDeviceInfo desiredCamera = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
videoOptions = new VideoOptions(currentVideoStream);
Future startVideoFuture = call.startVideo(currentVideoStream);
startVideoFuture.get();
```

비디오 전송을 시작 하면 `LocalVideoStream` 인스턴스는 `localVideoStreams` 호출 인스턴스의 컬렉션에 추가 됩니다.
```java
currentVideoStream == call.getLocalVideoStreams().get(0);
```

로컬 비디오를 중지 하려면 `localVideoStream` 컬렉션에서 사용할 수 있는 인스턴스를 전달 합니다 `localVideoStreams` .
```java
call.stopVideo(localVideoStream).get();
```

인스턴스에서를 호출 하 여 비디오가 전송 되는 동안 다른 카메라 장치로 전환할 수 있습니다 `switchSource` `localVideoStream` .
```java
localVideoStream.switchSource(source).get();
```

## <a name="remote-participants-management"></a>원격 참여자 관리

모든 원격 참가자는 형식으로 표시 되며 `RemoteParticipant` 호출 인스턴스의 컬렉션을 통해 사용할 수 있습니다 `remoteParticipants` .

### <a name="list-participants-in-a-call"></a>호출의 참가자 나열
`remoteParticipants`컬렉션은 지정 된 호출에서 원격 참가자 목록을 반환 합니다.
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
```

### <a name="remote-participant-properties"></a>원격 참가자 속성
지정 된 원격 참가자에는 연결 된 속성 및 컬렉션 집합이 있습니다.

* 이 원격 참가자에 대 한 식별자를 가져옵니다.
Id는 ' Identifier ' 형식 중 하나입니다.
```java
CommunicationIdentifier participantIdentity = remoteParticipant.getId();
```

* 이 원격 참가자의 상태를 가져옵니다.
```java
ParticipantState state = remoteParticipant.getState();
```
상태는 다음 중 하나일 수 있습니다.
* ' Idle '-초기 상태
* 참가자가 호출에 연결 하는 동안 ' 연결 중 '-전환 상태
* ' 연결 됨 '-참가자가 호출에 연결 되었습니다.
* ' 보유 '-참가자가 보류 중입니다.
* ' EarlyMedia '-참가자가 호출에 연결 되기 전에 해당 상태를 재생 합니다.
* ' Disconnected '-최종 상태-참가자가 호출에서 연결이 끊겼습니다.


* 참가자가 호출을 떠난 이유를 알아보려면 속성을 검사 합니다 `callEndReason` .
```java
CallEndReason callEndReason = remoteParticipant.getCallEndReason();
```

* 이 원격 참가자가 음소거 되어 있는지 여부를 확인 하려면 속성을 검사 합니다 `isMuted` .
```java
boolean isParticipantMuted = remoteParticipant.getIsMuted();
```

* 이 원격 참가자가 말하는 지 여부를 확인 하려면 속성을 검사 합니다 `isSpeaking` .
```java
boolean isParticipantSpeaking = remoteParticipant.getIsSpeaking();
```

* 이 호출에서 지정 된 참가자가 보내는 모든 비디오 스트림을 검사 하려면 컬렉션을 확인 합니다 `videoStreams` .
```java
List<RemoteVideoStream> videoStreams = remoteParticipant.getVideoStreams(); // [RemoteVideoStream, RemoteVideoStream, ...]
```


### <a name="add-a-participant-to-a-call"></a>호출에 참가자 추가

호출 (사용자 또는 전화 번호)에 참가자를 추가 하려면를 호출 하면 `addParticipant` 됩니다. 그러면 원격 참가자 인스턴스가 동기적으로 반환 됩니다.

```java
const acsUser = new CommunicationUser("<acs user id>");
const acsPhone = new PhoneNumber("<phone number>");
RemoteParticipant remoteParticipant1 = call.addParticipant(acsUser);
RemoteParticipant remoteParticipant2 = call.addParticipant(acsPhone);
```

### <a name="remove-participant-from-a-call"></a>호출에서 참가자 제거
통화 (사용자 또는 전화 번호)에서 참가자를 제거 하려면를 호출 하면 `removeParticipant` 됩니다.
참가자가 호출에서 제거 되 면이를 비동기적으로 확인 합니다.
참가자도 컬렉션에서 제거 됩니다 `remoteParticipants` .
```java
RemoteParticipant remoteParticipant = call.getParticipants().get(0);
call.removeParticipant(acsUser).get();
call.removeParticipant(acsPhone).get();
```

## <a name="render-remote-participant-video-streams"></a>원격 참가자 비디오 스트림 렌더링
원격 참가자의 비디오 스트림과 화면 공유 스트림을 나열 하려면 다음 컬렉션을 검사 합니다 `videoStreams` .
```java
RemoteParticipant remoteParticipant = call.getRemoteParticipants().get(0);
RemoteVideoStream remoteParticipantStream = remoteParticipant.getVideoStreams().get(0);
MediaStreamType streamType = remoteParticipantStream.getType(); // of type MediaStreamType.Video or MediaStreamType.ScreenSharing
```
 
`RemoteVideoStream`원격 참가자 로부터를 렌더링 하려면 이벤트를 구독 해야 `OnVideoStreamsUpdated` 합니다.
이벤트 내에서 `isAvailable` 속성을 true로 변경 하면 원격 참가자가 현재 스트림을 보내고 있는 경우 새 인스턴스를 만든 `Renderer` 다음 `RendererView` 비동기 API를 사용 하 여 새를 만들고 `createView` `view.target` 응용 프로그램의 UI에서 아무 곳에 나 연결할 수 있습니다.
원격 스트림의 가용성이 변경 될 때마다 전체 렌더러를 제거 하거나 특정 항목을 유지 하도록 선택할 수 `RendererView` 있지만이 경우 빈 비디오 프레임이 표시 됩니다.

```java
Renderer remoteVideoRenderer = new Renderer(remoteParticipantStream, appContext);
View uiView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
layout.addView(uiView);

remoteParticipant.addOnVideoStreamsUpdatedListener(e -> onRemoteParticipantVideoStreamsUpdated(p, e));

void onRemoteParticipantVideoStreamsUpdated(RemoteParticipant participant, RemoteVideoStreamsEvent args) {
    for(RemoteVideoStream stream : args.getAddedRemoteVideoStreams()) {
        if(stream.getIsAvailable()) {
            startRenderingVideo();
        } else {
            renderer.dispose();
        }
    }
}
```

### <a name="remote-video-stream-properties"></a>원격 비디오 스트림 속성
원격 비디오 스트림에 몇 가지 속성이 있습니다.

* `Id` -원격 비디오 스트림의 Id
```java
int id = remoteVideoStream.getId();
```

* `MediaStreamType` -' Video ' 또는 ' ScreenSharing ' 일 수 있습니다.
```java
MediaStreamType type = remoteVideoStream.getType();
```

* `isAvailable` -원격 참가자 끝점이 현재 스트림을 전송 하 고 있는지 여부를 나타냅니다.
```java
boolean availability = remoteVideoStream.getIsAvailable();
```

### <a name="renderer-methods-and-properties"></a>렌더러 메서드 및 속성
Api 다음의 렌더러 개체

* `RendererView`나중에 응용 프로그램 UI에 연결 하 여 원격 비디오 스트림을 렌더링할 수 있는 인스턴스를 만듭니다.
```java
// Create a view for a video stream
renderer.createView()
```
* 렌더러 및 `RendererView` 이 렌더러와 연결 된 모든 삭제
```java
renderer.dispose()
```

* `StreamSize` -원격 비디오 스트림의 크기 (너비/높이)
```java
StreamSize renderStreamSize = remoteVideoStream.getSize();
int width = renderStreamSize.getWidth();
int height = renderStreamSize.getHeight();
```


### <a name="rendererview-methods-and-properties"></a>RendererView 메서드 및 속성
를 만들 때 `RendererView` 이 뷰에 적용 되는 및 속성을 지정할 수 있습니다 `scalingMode` `mirrored` . 크기 조정 모드는 ' 스트레치 ' 중 하나일 수 있습니다. ' 자르기 ' | ' Fit ' `mirrored` 가로 설정 된 경우 `true` 렌더링 된 스트림은 상하로 대칭 이동 됩니다.

```java
Renderer remoteVideoRenderer = new Renderer(remoteVideoStream, appContext);
RendererView rendererView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
```

그러면 만든 RendererView를 다음 코드 조각을 사용 하 여 응용 프로그램 UI에 연결할 수 있습니다.
```java
layout.addView(rendererView);
```

`updateScalingMode`ScalingMode 중 하나를 사용 하 여 RendererView 개체에서 API를 호출 하 여 나중에 크기 조정 모드를 업데이트할 수 있습니다. ScalingMode | ScalingMode를 인수로 합니다.
```java
// Update the scale mode for this view.
rendererView.updateScalingMode(ScalingMode.Crop)
```


## <a name="device-management"></a>디바이스 관리

`DeviceManager` 오디오/비디오 스트림을 전송 하는 호출에 사용할 수 있는 로컬 장치를 열거할 수 있습니다. 또한 네이티브 브라우저 API를 사용 하 여 사용자가 마이크 및 카메라에 액세스할 수 있는 권한을 요청할 수 있습니다.

`deviceManager`메서드를 호출 하 여에 액세스할 수 있습니다 `callClient.getDeviceManager()` .
> [!WARNING]
> 현재는 `callAgent` DeviceManager에 대 한 액세스 권한을 얻기 위해 먼저 개체를 인스턴스화해야 합니다.

```java
DeviceManager deviceManager = callClient.getDeviceManager().get();
```

### <a name="enumerate-local-devices"></a>로컬 장치 열거

로컬 장치에 액세스 하려면 Device Manager 열거 메서드를 사용할 수 있습니다. 열거는 동기 작업입니다.

```java
//  Get a list of available video devices for use.
List<VideoDeviceInfo> localCameras = deviceManager.getCameraList(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
List<AudioDeviceInfo> localMicrophones = deviceManager.getMicrophoneList(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
List<AudioDeviceInfo> localSpeakers = deviceManager.getSpeakerList(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-default-microphonespeaker"></a>기본 마이크/스피커 설정

장치 관리자를 사용 하 여 호출을 시작할 때 사용할 기본 장치를 설정할 수 있습니다.
클라이언트 기본값을 설정 하지 않으면 통신 서비스는 OS 기본값으로 대체 됩니다.

```java

// Get the microphone device that is being used.
AudioDeviceInfo defaultMicrophone = deviceManager.getMicrophoneList().get(0);

// Set the microphone device to use.
deviceManager.setMicrophone(defaultMicrophone);

// Get the speaker device that is being used.
AudioDeviceInfo defaultSpeaker = deviceManager.getSpeakerList().get(0);

// Set the speaker device to use.
deviceManager.setSpeaker(defaultSpeaker);
```

### <a name="local-camera-preview"></a>로컬 카메라 미리 보기

`DeviceManager`및 `Renderer` 를 사용 하 여 로컬 카메라에서 스트림 렌더링을 시작할 수 있습니다. 이 스트림은 다른 참가자로 전송 되지 않습니다. 로컬 미리 보기 피드입니다. 비동기 작업입니다.

```java
VideoDeviceInfo videoDevice = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(videoDevice, appContext);
videoOptions = new VideoOptions(currentVideoStream);

Renderer previewRenderer = new Renderer(currentVideoStream, appContext);
View uiView previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));

// Attach the renderingSurface to a viewable location on the app at this point
layout.addView(uiView);
```

## <a name="eventing-model"></a>이벤트 모델
값이 변경 될 때 알림을 받으려면 대부분의 속성 및 컬렉션을 구독할 수 있습니다.

### <a name="properties"></a>속성
이벤트를 구독 하려면 `property changed` :

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnCallStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnCallStateChangedListener(callStateChangeListener);
```

### <a name="collections"></a>컬렉션
이벤트를 구독 하려면 `collection updated` :

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```
