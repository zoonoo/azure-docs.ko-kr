---
title: 빠른 시작 - Azure Communication Services를 사용하여 Android 앱에 Teams 미팅 조인 추가
description: 이 빠른 시작에서는 Android용 Azure Communication Services Teams Embed 라이브러리를 사용하는 방법을 알아봅니다.
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 5ac4c53550468d33e9ed533303749d29e772d766
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108481"
---
이 빠른 시작에서는 Android용 Azure Communication Services Teams Embed 라이브러리를 사용하여 Teams 미팅에 조인하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android 스튜디오](https://developer.android.com/studio)(Android 애플리케이션 만들기용)
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../create-communication-resource.md).
- [사용자 액세스 토큰](../../access-tokens.md)(Azure Communication Service용)

## <a name="setting-up"></a>설치

### <a name="create-an-android-app-with-an-empty-activity"></a>빈 활동을 사용하여 Android 앱 만들기

Android 스튜디오에서 [새 Android 스튜디오 프로젝트 시작]을 선택합니다.

:::image type="content" source="../media/android/studio-new-project.png" alt-text="Android 스튜디오에서 선택한 '새 Android 스튜디오 프로젝트 시작' 단추를 보여 주는 스크린샷":::

"휴대폰 및 태블릿" 아래에서 "빈 활동" 프로젝트 템플릿을 선택합니다.

:::image type="content" source="../media/android/studio-blank-activity.png" alt-text="프로젝트 템플릿 화면에서 선택한 '빈 활동' 옵션을 보여 주는 스크린샷":::

프로젝트 이름을 `TeamsEmbedAndroidGettingStarted`로 지정하고, 언어를 java로 설정한 후, Minimum SDK of “API 21: Android 5.0(Lollipop)” 이상을 선택합니다.

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="프로젝트 템플릿 화면에서 선택한 '빈 활동' 옵션을 보여 주는 스크린샷 2":::


### <a name="install-the-azure-package"></a>Azure 패키지 설치

앱 수준 build.gradle에서 종속성 및 Android 섹션에 다음 줄을 추가합니다.

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
}
```

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-common:1.0.0-beta.6'
    ...
}
```

### <a name="install-the-teams-embed-package"></a>Teams Embed 패키지 설치

`MicrosoftTeamsSDK` 패키지를 다운로드합니다.

그런 다음, MicrosoftTeamsSDK 폴더를 사용자의 프로젝트 앱 폴더로 압축을 풉니다. 예: `TeamsEmbedAndroidGettingStarted/app/MicrosoftTeamsSDK`.

### <a name="add-teams-embed-package-to-your-buildgradle"></a>Teams Embed 패키지를 사용자의 build.gradle에 추가

앱 수준 `build.gradle`에서 파일 끝에 다음 줄을 추가합니다.

```groovy
apply from: 'MicrosoftTeamsSDK/MicrosoftTeamsSDK.gradle'
```

gradle 파일을 사용하여 프로젝트를 동기화합니다.

### <a name="create-application-class"></a>애플리케이션 클래스 만들기

`TeamsEmbedAndroidGettingStarted`라는 이름의 새 Java 클래스 파일을 만듭니다. 이 클래스는 `TeamsSDKApplication`을 확장해야 하는 애플리케이션 클래스입니다. [Android 설명서](https://developer.android.com/reference/android/app/Application)

:::image type="content" source="../media/android/application-class-location.png" alt-text="Android Studio에서 애플리케이션 클래스를 만들 위치를 보여 주는 스크린샷":::

```java
package com.microsoft.teamsembedandroidgettingstarted;

import com.microsoft.teamssdk.app.TeamsSDKApplication;

public class TeamsEmbedAndroidGettingStarted extends TeamsSDKApplication {
}
```

### <a name="update-themes"></a>테마 업데이트

`theme.xml` 및 `theme.xml (night)` 파일 모두에서 스타일 이름을 `AppTheme`로 설정합니다.

:::image type="content" source="../media/android/theme-settings.png" alt-text="Android Studio에서 theme.xml 파일을 보여 주는 스크린샷":::

```xml
<style name="AppTheme" parent="Theme.AppCompat.DayNight.DarkActionBar">
```

```xml
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
```

### <a name="add-permissions-to-application-manifest"></a>애플리케이션 매니페스트에 권한 추가

애플리케이션 매니페스트(`app/src/main/AndroidManifest.xml`)에 `RECORD_AUDIO` 사용 권한을 추가합니다.  


```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
    <uses-permission android:name="android.permission.RECORD_AUDIO"/>
    <application
```

### <a name="add-app-name-and-theme-to-application-manifest"></a>애플리케이션 매니페스트에 앱 이름 및 테마 추가

매니페스트에 ‘xmlns:tools=“http://schemas.android.com/tools”를 추가합니다.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
```

`.TeamsEmbedAndroidGettingStarted`를 `android:name`에, `android:name`을 `tools:replace`에 추가 및 `android:theme`을 `@style/AppTheme`으로 변경

```xml
<application
    android:name=".TeamsEmbedAndroidGettingStarted"
    tools:replace="android:name"
    android:theme="@style/AppTheme"
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:roundIcon="@mipmap/ic_launcher_round"
    android:supportsRtl="true">
```

### <a name="set-up-the-layout-for-the-app"></a>앱 레이아웃 설정

`join_meeting`의 ID가 포함된 단추를 만듭니다. `app/src/main/res/layout/activity_main.xml`로 이동하고, 파일의 내용을 다음으로 바꿉니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/join_meeting"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Join Meeting"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>기본 활동 스캐폴딩 및 바인딩 만들기

레이아웃이 만들어지면 바인딩과 활동의 기본 스캐폴딩을 추가할 수 있습니다. 이 활동은 단추가 눌러지면 런타임 권한 요청, 미팅 클라이언트 만들기 및 미팅 조인을 처리합니다. 각각에 대해 해당 섹션에서 설명합니다. 

`onCreate` 메서드는 `getAllPermissions` 및 `createAgent`를 호출하고 `Join Meeting` 단추에 대한 바인딩을 추가하도록 재정의됩니다. 이는 활동이 만들어질 때 한 번만 발생합니다. `onCreate`에 대한 자세한 내용은 [활동 수명 주기에 관한 이해](https://developer.android.com/guide/components/activities/activity-lifecycle) 가이드를 참조하세요.

**MainActivity.java** 로 이동하고, 내용을 다음 코드로 바꿉니다.

```java
package com.yourcompany.teamsembedandroidgettingstarted;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.common.CommunicationTokenRefreshOptions;
import com.azure.android.communication.ui.meetings.MeetingJoinOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClient;

import java.util.ArrayList;
import java.util.concurrent.Callable;

public class MainActivity extends AppCompatActivity {

    private final String displayName = "John Smith";

    private MeetingUIClient meetingUIClient;
    private MeetingJoinOptions meetingJoinOptions;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        meetingJoinOptions = new MeetingJoinOptions(displayName);
        
        getAllPermissions();
        createMeetingClient();

        Button joinMeeting = findViewById(R.id.join_meeting);
        joinMeeting.setOnClickListener(l -> joinMeeting());
    }

    private void createMeetingClient() {
        // See section on creating meeting client
    }

    private void joinMeeting() {
        // See section on joining a meeting
    }

    private void getAllPermissions() {
        // See section on getting permissions
    }
}
```

### <a name="request-permissions-at-runtime"></a>런타임에 권한 요청

Android 6.0 이상(API 수준 23) 및 `targetSdkVersion` 23 이상에서는 앱이 설치될 때가 아니라 런타임에 권한이 부여됩니다. 이를 지원하기 위해 필요한 각 권한에 대해 `ActivityCompat.checkSelfPermission` 및 `ActivityCompat.requestPermissions`를 호출하도록 `getAllPermissions`를 구현할 수 있습니다.

```java
/**
 * Request each required permission if the app doesn't already have it.
 */
private void getAllPermissions() {
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 202);
    }
}
```

> [!NOTE]
> 앱을 설계할 때 언제 이러한 권한을 요청할지 해당 시기를 고려하세요. 권한은 미리 요청하는 것이 아니라 필요에 따라 요청해야 합니다. 자세한 내용은 [Android 권한 가이드](https://developer.android.com/training/permissions/requesting)를 참조하세요.

## <a name="object-model"></a>개체 모델

Azure Communication Services Teams Embed 라이브러리의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

| Name                                  | 설명                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingUIClient| MeetingUIClient는 Teams Embed 라이브러리에 대한 주요 진입점입니다. |
| MeetingJoinOptions | MeetingJoinOptions는 표시 이름 등의 구성 가능한 옵션에 사용됩니다. |
| CallState | CallState는 호출 상태 변경을 보고하는 데 사용됩니다. 옵션은 다음과 같습니다. `connecting`, `waitingInLobby`, `connected` 및 `ended`. |

## <a name="create-a-meetingclient-from-the-user-access-token"></a>사용자 액세스 토큰에서 MeetingClient 만들기

사용자 액세스 토큰을 사용하여 인증된 미팅 클라이언트를 인스턴스화할 수 있습니다. 이 토큰은 일반적으로 애플리케이션과 관련된 인증을 사용하여 서비스에 의해 생성됩니다. 사용자 액세스 토큰에 대해 자세히 알아보려면 [사용자 액세스 토큰](../../access-tokens.md) 가이드를 확인하세요. 빠른 시작에서는 `<USER_ACCESS_TOKEN>`을 Azure Communication Services 리소스에 대해 생성된 사용자 액세스 토큰으로 바꿉니다.

```java
private void createMeetingClient() {
    try {
        CommunicationTokenRefreshOptions refreshOptions = new CommunicationTokenRefreshOptions(tokenRefresher, true, "<USER_ACCESS_TOKEN>");
        CommunicationTokenCredential credential = new CommunicationTokenCredential(refreshOptions);
        meetingUIClient = new MeetingUIClient(credential);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="setup-token-refreshing"></a>토큰 새고 고침 설정

호출 가능 `tokenRefresher` 메서드를 만듭니다. 그런 다음, 사용자 토큰을 가져오는 `fetchToken` 메서드를 만듭니다. [수행 방법에 대한 지침은 여기서 확인할 수 있습니다.](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-java)

```java
Callable<String> tokenRefresher = () -> {
    return fetchToken();
};

public String fetchToken() {
    // Get token
    return USER_ACCESS_TOKEN;
}
```

## <a name="get-the-teams-meeting-link"></a>Teams 미팅 링크 가져오기

Teams 미팅 링크는 Graph API를 사용하여 검색할 수 있습니다. 자세한 내용은 [Graph 설명서](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)에서 설명하고 있습니다.
Communication Services Calling SDK는 전체 Teams 미팅 링크를 수락합니다. 이 링크는 [`joinWebUrl` 속성](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true)에서 액세스할 수 있는 `onlineMeeting` 리소스의 일부로 반환됩니다. Teams 미팅 초대 자체의 **미팅 조인** URL에서 필요한 미팅 정보를 가져올 수도 있습니다.

## <a name="start-a-meeting-using-the-meeting-client"></a>미팅 클라이언트를 사용하여 미팅 시작

`MeetingClient`를 통해 미팅에 조인할 수 있습니다. `meetingURL` 및 `JoinOptions`만 필요합니다. `<MEETING_URL>`을 Teams 미팅 URL로 바꿉니다.

```java
/**
 * Join a meeting with a meetingURL.
 */
private void joinMeeting() {
    try {
        meetingUIClient.join("<MEETING_URL>", meetingJoinOptions);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="launch-the-app-and-join-a-meeting"></a>앱을 시작하고 미팅에 조인

이제 도구 모음의 "앱 실행" 단추(Shift+F10)를 사용하여 앱을 시작할 수 있습니다. 

:::image type="content" source="../media/android/quickstart-android-join-meeting.png" alt-text="완료된 애플리케이션을 보여 주는 스크린샷":::

:::image type="content" source="../media/android/quickstart-android-joined-meeting.png" alt-text="미팅에 조인한 후 완료된 애플리케이션을 보여 주는 스크린샷":::

## <a name="sample-code"></a>샘플 코드

샘플 앱은 [GitHub](https://github.com/Azure-Samples/teams-embed-android-getting-started)에서 다운로드할 수 있습니다.
