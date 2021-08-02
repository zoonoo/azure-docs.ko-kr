---
title: Android용 Azure Communication Services Teams Embed 사용
description: 이 개요에서는 Android용 Azure Communication Services Teams Embed 라이브러리를 사용하는 방법을 알아봅니다.
author: palatter
ms.author: palatter
ms.date: 24/02/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 73174081d416f8ff1ab532baaf62b5b466aeb178
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110163355"
---
## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../create-communication-resource.md).
- 호출 클라이언트를 사용하도록 설정하는 `User Access Token`입니다. [`User Access Token`를 가져오는 방법](../../access-tokens.md)에 대한 자세한 정보
- [애플리케이션에 Teams Embed 추가 시작](../getting-started-with-teams-embed.md)에 대한 빠른 시작을 완료합니다.

## <a name="teams-embed-events"></a>Teams Embed 이벤트

`MainActivity.java`에 다음 코드를 추가합니다.

```java
import androidx.core.content.ContextCompat;
import com.azure.android.communication.ui.meetings.MeetingUIClientCallState;
import com.azure.android.communication.ui.meetings.MeetingUIClientEventListener;
import com.azure.android.communication.ui.meetings.MeetingUIClientIdentityProvider;
import com.azure.android.communication.ui.meetings.MeetingUIClientIdentityProviderCallback;
```

클래스에 `MeetingUIClientEventListener`를 추가합니다.

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientEventListener {

    private MeetingUIClient meetingUIClient;
```

`MeetingUIClientEventListener`을 `this`으로 설정합니다.

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    createMeetingClient();
}

private void createMeetingClient() {
    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(tokenRefresher, true, ACS_TOKEN);
        meetingUIClient = new MeetingUIClient(credential);
        meetingUIClient.setMeetingUIClientEventListener(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

`onCallStateChanged` 및 `onRemoteParticipantCountChanged` 메서드를 구현합니다.

```java
@Override
public void onCallStateChanged(MeetingUIClientCallState callState) {
    switch(callState) {
        case CONNECTING:
            System.out.println("Call state changed to 'Connecting'");
            break;
        case CONNECTED:
            System.out.println("Call state changed to 'Connected'");
            break;
        case WAITING_IN_LOBBY:
            System.out.println("Call state changed to 'Waiting in Lobby'");
            break;
        case ENDED:
            System.out.println("Call state changed to 'Ended'");
            break;
    }
}

@Override
public void onRemoteParticipantCountChanged(int newCount) {
    System.out.println("Remote participant count changed: " + newCount);
}
```

## <a name="assigning-avatars-for-users"></a>사용자에 대한 아바타 할당

클래스에 `MeetingUIClientIdentityProvider`를 추가합니다.

```java
public class MainActivity extends AppCompatActivity implements MeetingUIClientIdentityProvider {

    private MeetingUIClient meetingUIClient;
```

`MeetingUIClientIdentityProvider`을 `this`으로 설정합니다.

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    createMeetingClient();
}

private void createMeetingClient() {
    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(tokenRefresher, true, ACS_TOKEN);
        meetingUIClient = new MeetingUIClient(credential);
        meetingUIClient.setMeetingUIClientIdentityProvider(this);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

각 `userMri`를 해당 아바타에 매핑합니다.

```java
@Override
public void provideAvatarFor(String userIdentifier, MeetingUIClientIdentityProviderCallback meetingIdentityProviderCallback) {
    try {
        System.out.println("MeetingUIClientIdentityProvider.provideAvatarFor called for userIdentifier: " + userIdentifier);
        if (userIdentifier.startsWith("8:teamsvisitor:")) {
            meetingIdentityProviderCallback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_avatar_placeholder_large_pink));
        } else if (userIdentifier.startsWith("8:orgid:")) {
            meetingIdentityProviderCallback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_doctor_avatar));
        } else if (userIdentifier.startsWith("8:acs:")) {
            meetingIdentityProviderCallback.onAvatarAvailable(ContextCompat.getDrawable(this, R.drawable.nodpi_avatar_placeholder_large_green));
        }
    } catch (Exception e) {
        System.out.println("MeetingUIClientIdentityProvider: Exception while provideAvatarFor for userIdentifier: " + userIdentifier + e.getMessage());
    }
}
```


다른 필수 MeetingUIClientIdentityProvider 인터페이스 메서드를 클래스에 추가하면 빈 구현으로 남아 있을 수 있습니다.

```java
@Override
public void provideDisplayNameFor(String userIdentifier, MeetingUIClientIdentityProviderCallback meetingUIClientIdentityProviderCallback) {
}

@Override
public void provideSubTitleFor(String userIdentifier, MeetingUIClientIdentityProviderCallback meetingUIClientIdentityProviderCallback) {
}
```
