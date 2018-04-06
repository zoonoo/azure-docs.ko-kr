---
title: Azure Mobile Engagement Android SDK 통합
description: Azure Mobile Engagement용 Android SDK의 최신 업데이트 및 절차
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d72b5014-a22b-4a7f-a470-d2b8145b5b86
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 10/10/2016
ms.author: piyushjo
ms.openlocfilehash: 21f9c1007c6b7dabc625555c66f267b2b7c579f8
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-gcm-with-mobile-engagement"></a>GCM과 Mobile Engagement를 통합하는 방법
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

> [!IMPORTANT]
> 이 가이드를 수행하기 전에 Android 문서의 Engagement를 통합하는 방법에 설명된 통합 절차를 따라야 합니다.
> 
> 이 문서는 도달률 모듈 및 Google Play 장치를 푸시하는 계획을 이미 통합한 경우에만 유용합니다. 응용 프로그램에서 도달률 캠페인을 통합하려면 먼저 Android에서 Engagement 도달률을 통합하는 방법을 읽어보세요.
> 
> 

## <a name="introduction"></a>소개
GCM을 통합하면 응용 프로그램을 푸시할 수 있습니다.

SDK로 푸시된 GCM 페이로드는 데이터 개체에 항상 `azme` 키를 포함합니다. 따라서 응용 프로그램에서 다른 용도로 GCM을 사용하는 경우 해당 키에 따라 푸시를 필터링할 수 있습니다.

> [!IMPORTANT]
> Android 2.2 이상을 실행하고, Google Play를 설치하고, Google 백그라운드 연결이 활성화된 장치만 GCM을 사용해 푸시될 수 있지만 지원되지 않는 장치(단지 의도만 이용하는 장치)에서 이 코드를 안전하게 통합할 수 있습니다.
> 
> 

## <a name="create-a-google-cloud-messaging-project-with-api-key"></a>API 키를 가진 Google Cloud Messaging 프로젝트 만들기
[!INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

## <a name="sdk-integration"></a>SDK 통합
### <a name="managing-device-registrations"></a>장치 등록 관리
각 장치는 Google 서버에 등록 명령을 보내야 하며, 그렇지 않은 경우 해당 서버에 연결할 수 없습니다.

장치는 GCM 알림에서 등록 취소할 수 있습니다(응용 프로그램이 제거된 경우 장치가 자동으로 등록 취소됨).

[Google Play SDK] 를 사용하지 않거나 등록 의도를 아직 직접 보내지 않은 경우 Engagement에서 자동으로 장치를 등록하도록 설정할 수 있습니다.

이 기능을 사용하려면 `AndroidManifest.xml` 파일의 `<application/>` 태그 내에 다음을 추가합니다.

            <!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
            <meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Engagement 푸시 서비스에 등록 ID를 전달하고 알림을 수신합니다.
Engagement 푸시 서비스에 장치의 등록 ID를 전달하고 해당 알림을 수신하려면 `AndroidManifest.xml` 파일의 `<application/>` 태그 내에 다음을 추가합니다(장치 등록을 직접 관리하는 경우에도).

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your_package_name>" />
              </intent-filter>
            </receiver>

`AndroidManifest.xml`에서(`</application>` 태그 다음) 다음 권한이 있는지 확인합니다.

            <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
            <uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
            <permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

## <a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>Mobile Engagement에 GCM API 키에 대한 액세스 권한 부여
Mobile Engagement에 GCM API 키에 대한 액세스 권한을 부여하려면 [이 가이드](mobile-engagement-android-get-started.md#grant-mobile-engagement-access-to-your-gcm-api-key) 를 따르세요.

[Google Play SDK]:https://developers.google.com/cloud-messaging/android/start
