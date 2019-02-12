---
title: 포함 파일
description: 포함 파일
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 02/05/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bc920493b32d500602a5b683c098d23aff855150
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823303"
---
1. **Android Studio**의 메뉴에서 **도구**를 선택하고, **SDK Manager**를 선택합니다. 
2. 프로젝트에서 사용되는 Android SDK의 대상 버전을 선택하고, **패키지 정보 표시**를 선택합니다. 

    ![Android SDK Manager - 대상 버전 선택](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. **Google API**를 선택합니다(아직 설치되지 않은 경우).

    ![Android SDK Manager - Google API 선택](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. **SDK 도구** 탭으로 전환합니다. Google Play 서비스를 아직 설치하지 않은 경우 다음 이미지처럼 **Google Play 서비스**를 선택합니다. **적용** 을 클릭하여 설치합니다. 이후 단계에서 사용할 수 있도록 SDK 경로를 기록해 둡니다.

    ![Android SDK Manager - Google Play 서비스 선택](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. **변경 확인** 대화 상자가 나타나면 **확인**을 선택합니다. 구성 요소 설치 관리자가 요청된 구성 요소를 설치합니다. 구성 요소가 설치되면 **완료**를 선택합니다.
4. **확인**을 선택하여 **새 프로젝트 설정** 대화 상자를 닫습니다.  
5. **app** 디렉터리에서 `build.gradle` 파일을 열고, 이 줄을 `dependencies` 아래에 추가합니다. 

    ```text
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. 도구 모음에서 **지금 동기화** 아이콘을 선택합니다.

    ![Gradle과 동기화](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. **AndroidManifest.xml**을 열고 이 태그를 *애플리케이션* 태그에 추가합니다.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
