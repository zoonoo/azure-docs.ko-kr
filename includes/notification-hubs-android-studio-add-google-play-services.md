---
title: 포함 파일
description: 포함 파일
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 01/04/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: f00ca7ddf44a9d5b850cd47520970a0396a0c1b5
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54453093"
---
1. Android Studio의 도구 모음에서 아이콘을 클릭하거나 메뉴에서 **도구** > **Android** > **SDK Manager**를 클릭하여 Android SDK Manager를 엽니다. 프로젝트에서 사용되는 Android SDK의 대상 버전을 찾고, **패키지 정보 표시**를 클릭하여 열고, 아직 설치되지 않은 경우 **Google API**를 선택합니다.
2. **SDK 도구** 탭을 클릭합니다. Google Play Service를 아직 설치하지 않은 경우 아래와 같이 **Google Play Services** 를 클릭합니다. **적용** 을 클릭하여 설치합니다. 이후 단계에서 사용할 수 있도록 SDK 경로를 기록해 둡니다.

    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. 앱 디렉터리에서 `build.gradle` 파일을 엽니다.

    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)
4. `dependencies`에서 이 줄을 추가합니다.

    ```text
    compile 'com.google.android.gms:play-services-gcm:12.0.0'
    ```
5. 도구 모음에서 **Gradle 파일과 프로젝트 동기화** 아이콘을 클릭합니다.
6. **AndroidManifest.xml**을 열고 이 태그를 *애플리케이션* 태그에 추가합니다.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
